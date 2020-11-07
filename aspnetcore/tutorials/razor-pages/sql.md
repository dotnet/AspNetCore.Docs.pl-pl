---
title: Część 4, współpraca z bazą danych
author: rick-anderson
description: 'Część 4 z serii samouczków na :::no-loc(Razor)::: stronach.'
ms.author: riande
ms.date: 09/26/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e78b5b6dab7145413ae8612bfeb352f328ec86a
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360736"
---
# <a name="part-4-work-with-a-database-and-aspnet-core"></a><span data-ttu-id="c35bb-103">Część 4, współpraca z bazą danych i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c35bb-103">Part 4, work with a database and ASP.NET Core</span></span>

<span data-ttu-id="c35bb-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="c35bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c35bb-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c35bb-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c35bb-106">`:::no-loc(Razor):::PagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-106">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="c35bb-107">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="c35bb-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-108">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-109">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="c35bb-110">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz.</span><span class="sxs-lookup"><span data-stu-id="c35bb-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="c35bb-111">W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="c35bb-111">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c35bb-113">Wygenerowane parametry połączenia będą podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="c35bb-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-114">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="c35bb-115">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="c35bb-116">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c35bb-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-117">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="c35bb-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c35bb-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c35bb-119">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="c35bb-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="c35bb-120">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="c35bb-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c35bb-121">Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:\Users\<user>\` katalogu.</span><span class="sxs-lookup"><span data-stu-id="c35bb-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="c35bb-122">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c35bb-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menu Widok](sql/_static/5/ssox.png)

1. <span data-ttu-id="c35bb-124">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-124">Right-click on the `Movie` table and select **View Designer** :</span></span>

   ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/5/design.png)

   ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

   <span data-ttu-id="c35bb-127">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="c35bb-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="c35bb-128">Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="c35bb-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="c35bb-129">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-129">Right-click on the `Movie` table and select **View Data** :</span></span>

   ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-131">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="c35bb-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="c35bb-132">SQLite</span></span>

<span data-ttu-id="c35bb-133">Stan witryny sieci Web programu [SQLite](https://www.sqlite.org/) :</span><span class="sxs-lookup"><span data-stu-id="c35bb-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="c35bb-134">Oprogramowanie SQLite to samodzielna, osadzona, w pełni funkcjonalna, publiczna i ogólnie oparta na usłudze SQL Database aparat.</span><span class="sxs-lookup"><span data-stu-id="c35bb-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="c35bb-135">SQLite to najbardziej używany aparat bazy danych na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="c35bb-136">Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych programu SQLite i ją przeglądać.</span><span class="sxs-lookup"><span data-stu-id="c35bb-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="c35bb-137">Poniższy obraz pochodzi z [przeglądarki DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="c35bb-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="c35bb-138">Jeśli masz ulubione narzędzie SQLite, pozostaw komentarz na temat tego, co Ci się podoba.</span><span class="sxs-lookup"><span data-stu-id="c35bb-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Przeglądarka DB dla oprogramowania SQLite przedstawiająca bazę danych filmów](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="c35bb-140">W tym samouczku zostanie użyta funkcja *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="c35bb-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="c35bb-141">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="c35bb-142">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="c35bb-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="c35bb-143">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="c35bb-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="c35bb-144">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="c35bb-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="c35bb-145">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="c35bb-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="c35bb-146">Zamiast tego, po zmianie schematu baza danych zostanie porzucona i utworzona.</span><span class="sxs-lookup"><span data-stu-id="c35bb-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="c35bb-147">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="c35bb-148">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="c35bb-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="c35bb-149">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-149">Creating a new table.</span></span>
>* <span data-ttu-id="c35bb-150">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="c35bb-151">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-151">Dropping the old table.</span></span>
>* <span data-ttu-id="c35bb-152">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-152">Renaming the new table.</span></span>
>
><span data-ttu-id="c35bb-153">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="c35bb-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="c35bb-154">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="c35bb-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="c35bb-155">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="c35bb-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="c35bb-156">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="c35bb-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="c35bb-157">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="c35bb-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="c35bb-158">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="c35bb-158">Seed the database</span></span>

<span data-ttu-id="c35bb-159">:::no-loc(Create)::: Nowa klasa o nazwie `SeedData` w folderze *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="c35bb-159">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="c35bb-160">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="c35bb-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="c35bb-161">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="c35bb-161">Add the seed initializer</span></span>

<span data-ttu-id="c35bb-162">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="c35bb-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Program.cs)]

<span data-ttu-id="c35bb-163">W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="c35bb-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="c35bb-164">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="c35bb-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c35bb-165">Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="c35bb-166">Usuń kontekst, gdy metoda inicjatora zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="c35bb-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="c35bb-167">[Instrukcja using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="c35bb-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="c35bb-168">Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="c35bb-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="c35bb-169">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c35bb-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-170">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c35bb-171">:::no-loc(Delete)::: wszystkie rekordy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-171">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="c35bb-172">Korzystanie z linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="c35bb-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="c35bb-173">Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="c35bb-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="c35bb-174">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="c35bb-175">Zatrzymaj i uruchom ponownie usługi IIS przy użyciu dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="c35bb-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="c35bb-176">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień, a następnie wybierz polecenie **Zakończ** lub **Zatrzymaj lokację** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site** :</span></span>

      ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu kontekstowe](sql/_static/stopIIS.png)

   1. <span data-ttu-id="c35bb-179">Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="c35bb-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="c35bb-180">Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="c35bb-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-181">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c35bb-182">:::no-loc(Delete)::: wszystkie rekordy w bazie danych, więc zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="c35bb-182">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="c35bb-183">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="c35bb-184">Aplikacja pokazuje dane z rozrzutu:</span><span class="sxs-lookup"><span data-stu-id="c35bb-184">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w przeglądarce przedstawiająca dane filmu](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="c35bb-186">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c35bb-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c35bb-187">[Poprzednie: Rusztowania :::no-loc(Razor)::: ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="c35bb-187">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="c35bb-188">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c35bb-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c35bb-189">`:::no-loc(Razor):::PagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-189">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="c35bb-190">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="c35bb-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-191">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-192">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="c35bb-193">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz.</span><span class="sxs-lookup"><span data-stu-id="c35bb-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="c35bb-194">W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="c35bb-194">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-195">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c35bb-196">Wygenerowane parametry połączenia będą podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="c35bb-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-197">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="c35bb-198">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="c35bb-199">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="c35bb-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-200">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="c35bb-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c35bb-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c35bb-202">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="c35bb-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="c35bb-203">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="c35bb-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c35bb-204">Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:\Users\<user>\` katalogu.</span><span class="sxs-lookup"><span data-stu-id="c35bb-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="c35bb-205">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c35bb-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](sql/_static/ssox.png)

* <span data-ttu-id="c35bb-207">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-207">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/design.png)

  ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

<span data-ttu-id="c35bb-210">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="c35bb-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="c35bb-211">Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="c35bb-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="c35bb-212">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-212">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-214">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="c35bb-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="c35bb-215">SQLite</span></span>

<span data-ttu-id="c35bb-216">Stan witryny sieci Web programu [SQLite](https://www.sqlite.org/) :</span><span class="sxs-lookup"><span data-stu-id="c35bb-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="c35bb-217">Oprogramowanie SQLite to samodzielna, osadzona, w pełni funkcjonalna, publiczna i ogólnie oparta na usłudze SQL Database aparat.</span><span class="sxs-lookup"><span data-stu-id="c35bb-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="c35bb-218">SQLite to najbardziej używany aparat bazy danych na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="c35bb-219">Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych programu SQLite i ją przeglądać.</span><span class="sxs-lookup"><span data-stu-id="c35bb-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="c35bb-220">Poniższy obraz pochodzi z [przeglądarki DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="c35bb-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="c35bb-221">Jeśli masz ulubione narzędzie SQLite, pozostaw komentarz na temat tego, co Ci się podoba.</span><span class="sxs-lookup"><span data-stu-id="c35bb-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Przeglądarka DB dla oprogramowania SQLite przedstawiająca bazę danych filmów](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="c35bb-223">W tym samouczku zostanie użyta funkcja *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="c35bb-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="c35bb-224">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="c35bb-225">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="c35bb-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="c35bb-226">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="c35bb-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="c35bb-227">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="c35bb-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="c35bb-228">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="c35bb-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="c35bb-229">Zamiast tego, po zmianie schematu baza danych zostanie porzucona i utworzona.</span><span class="sxs-lookup"><span data-stu-id="c35bb-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="c35bb-230">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="c35bb-231">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="c35bb-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="c35bb-232">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-232">Creating a new table.</span></span>
>* <span data-ttu-id="c35bb-233">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="c35bb-234">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-234">Dropping the old table.</span></span>
>* <span data-ttu-id="c35bb-235">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c35bb-235">Renaming the new table.</span></span>
>
><span data-ttu-id="c35bb-236">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="c35bb-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="c35bb-237">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="c35bb-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="c35bb-238">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="c35bb-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="c35bb-239">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="c35bb-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="c35bb-240">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="c35bb-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="c35bb-241">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="c35bb-241">Seed the database</span></span>

<span data-ttu-id="c35bb-242">:::no-loc(Create)::: Nowa klasa o nazwie `SeedData` w folderze *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="c35bb-242">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="c35bb-243">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="c35bb-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="c35bb-244">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="c35bb-244">Add the seed initializer</span></span>

<span data-ttu-id="c35bb-245">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="c35bb-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Program.cs)]

<span data-ttu-id="c35bb-246">W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="c35bb-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="c35bb-247">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="c35bb-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c35bb-248">Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="c35bb-249">Usuń kontekst, gdy metoda inicjatora zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="c35bb-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="c35bb-250">[Instrukcja using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="c35bb-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="c35bb-251">Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="c35bb-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="c35bb-252">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c35bb-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-253">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c35bb-254">:::no-loc(Delete)::: wszystkie rekordy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-254">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="c35bb-255">Użyj linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="c35bb-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="c35bb-256">Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="c35bb-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="c35bb-257">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="c35bb-258">Zatrzymaj i uruchom ponownie usługi IIS przy użyciu dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="c35bb-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="c35bb-259">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * <span data-ttu-id="c35bb-262">Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="c35bb-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="c35bb-263">Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="c35bb-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-264">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c35bb-265">:::no-loc(Delete)::: wszystkie rekordy w bazie danych, więc zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="c35bb-265">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="c35bb-266">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="c35bb-267">Aplikacja pokazuje dane z rozrzutu:</span><span class="sxs-lookup"><span data-stu-id="c35bb-267">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="c35bb-269">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c35bb-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c35bb-270">[Poprzednie: Rusztowania :::no-loc(Razor)::: ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="c35bb-270">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c35bb-271">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c35bb-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c35bb-272">`:::no-loc(Razor):::PagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-272">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="c35bb-273">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="c35bb-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-274">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-275">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="c35bb-276">Aby uzyskać więcej informacji na temat metod używanych w programie `ConfigureServices` , zobacz:</span><span class="sxs-lookup"><span data-stu-id="c35bb-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="c35bb-277">[Obsługa ogólne rozporządzenie o ochronie danych UE (Rodo) w ASP.NET Core](xref:security/gdpr) dla `:::no-loc(Cookie):::PolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="c35bb-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `:::no-loc(Cookie):::PolicyOptions`.</span></span>
* [<span data-ttu-id="c35bb-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="c35bb-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="c35bb-279">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz.</span><span class="sxs-lookup"><span data-stu-id="c35bb-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="c35bb-280">W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="c35bb-280">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-281">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c35bb-282">Wygenerowane parametry połączenia będą podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="c35bb-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/:::no-loc(appsettings.json):::)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c35bb-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c35bb-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-284">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="c35bb-285">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="c35bb-286">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="c35bb-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-287">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="c35bb-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c35bb-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c35bb-289">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="c35bb-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="c35bb-290">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="c35bb-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c35bb-291">Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:/Users/<user/>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="c35bb-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="c35bb-292">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c35bb-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](sql/_static/ssox.png)

* <span data-ttu-id="c35bb-294">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-294">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![Menu kontekstowe jest otwarte w tabeli filmów](sql/_static/design.png)

  ![Tabela filmów otwarta w projektancie](sql/_static/dv.png)

<span data-ttu-id="c35bb-297">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="c35bb-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="c35bb-298">Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="c35bb-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="c35bb-299">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-299">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c35bb-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c35bb-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-302">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="c35bb-303">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="c35bb-303">Seed the database</span></span>

<span data-ttu-id="c35bb-304">:::no-loc(Create)::: Nowa klasa o nazwie `SeedData` w folderze *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="c35bb-304">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="c35bb-305">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="c35bb-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="c35bb-306">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="c35bb-306">Add the seed initializer</span></span>

<span data-ttu-id="c35bb-307">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="c35bb-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Program.cs)]

<span data-ttu-id="c35bb-308">W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="c35bb-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="c35bb-309">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="c35bb-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c35bb-310">Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="c35bb-311">Usuń kontekst, gdy metoda inicjatora zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="c35bb-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="c35bb-312">[Instrukcja using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="c35bb-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="c35bb-313">Aplikacja produkcyjna nie będzie wywoływana `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="c35bb-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="c35bb-314">Jest on dodawany do poprzedniego kodu, aby zapobiec następującemu wyjątku, gdy `Update-Database` nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="c35bb-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="c35bb-315">SqlException: nie można otworzyć bazy danych " :::no-loc(Razor)::: PagesMovieContext-21" żądanej przez nazwę logowania.</span><span class="sxs-lookup"><span data-stu-id="c35bb-315">SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="c35bb-316">Logowanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="c35bb-316">The login failed.</span></span>
<span data-ttu-id="c35bb-317">Logowanie użytkownika "Nazwa użytkownika" nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="c35bb-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="c35bb-318">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c35bb-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c35bb-319">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c35bb-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c35bb-320">:::no-loc(Delete)::: wszystkie rekordy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-320">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="c35bb-321">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="c35bb-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="c35bb-322">Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="c35bb-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="c35bb-323">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="c35bb-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="c35bb-324">Można to zrobić przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="c35bb-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="c35bb-325">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę** :</span><span class="sxs-lookup"><span data-stu-id="c35bb-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * <span data-ttu-id="c35bb-328">Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="c35bb-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="c35bb-329">Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="c35bb-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c35bb-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c35bb-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c35bb-331">:::no-loc(Delete)::: wszystkie rekordy w bazie danych, więc zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="c35bb-331">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="c35bb-332">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c35bb-333">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c35bb-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c35bb-334">:::no-loc(Delete)::: wszystkie rekordy w bazie danych, więc zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="c35bb-334">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="c35bb-335">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="c35bb-336">Aplikacja pokazuje dane z rozrzutu:</span><span class="sxs-lookup"><span data-stu-id="c35bb-336">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55https.png)

<span data-ttu-id="c35bb-338">Następny samouczek czyści prezentację danych.</span><span class="sxs-lookup"><span data-stu-id="c35bb-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c35bb-339">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c35bb-339">Additional resources</span></span>

* [<span data-ttu-id="c35bb-340">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="c35bb-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="c35bb-341">[Poprzednie: Rusztowania :::no-loc(Razor)::: ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="c35bb-341">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
