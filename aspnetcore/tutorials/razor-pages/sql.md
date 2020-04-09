---
title: Praca z bazą danych i ASP.NET Core
author: rick-anderson
description: Wyjaśnia pracę z bazą danych i ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664340"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="6beda-103">Praca z bazą danych i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6beda-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="6beda-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Joe [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="6beda-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="6beda-105">Obiekt `RazorPagesMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="6beda-106">Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera Iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="6beda-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6beda-108">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="6beda-109">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik .</span><span class="sxs-lookup"><span data-stu-id="6beda-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="6beda-110">W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="6beda-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-111">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6beda-112">Wartość nazwy bazy danych`Database={Database name}`( ) będzie inna dla wygenerowanego kodu.</span><span class="sxs-lookup"><span data-stu-id="6beda-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="6beda-113">Wartość nazwy jest dowolna.</span><span class="sxs-lookup"><span data-stu-id="6beda-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6beda-114">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="6beda-115">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania ciągu połączenia na serwerze prawdziwej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="6beda-116">Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="6beda-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-117">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="6beda-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6beda-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6beda-119">LocalDB to lekka wersja aparatu bazy danych programu SQL Server Express przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="6beda-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="6beda-120">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="6beda-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6beda-121">Domyślnie baza danych `*.mdf` LocalDB `C:\Users\<user>\` tworzy pliki w katalogu.</span><span class="sxs-lookup"><span data-stu-id="6beda-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="6beda-122">Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="6beda-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](sql/_static/ssox.png)

* <span data-ttu-id="6beda-124">Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz **polecenie Wyświetl projektanta:**</span><span class="sxs-lookup"><span data-stu-id="6beda-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu kontekstowe otwarte w tabeli Film](sql/_static/design.png)

  ![Tabele filmów otwarte w projektancie](sql/_static/dv.png)

<span data-ttu-id="6beda-127">Zanotuj ikonę klawisza obok pozycji `ID`.</span><span class="sxs-lookup"><span data-stu-id="6beda-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="6beda-128">Domyślnie EF tworzy właściwość o nazwie `ID` klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="6beda-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="6beda-129">Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz polecenie **Wyświetl dane:**</span><span class="sxs-lookup"><span data-stu-id="6beda-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Otwieranie tabeli filmowych z danymi tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6beda-131">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="6beda-132">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="6beda-132">Seed the database</span></span>

<span data-ttu-id="6beda-133">Utwórz nową `SeedData` klasę o nazwie w folderze *Modele* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6beda-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="6beda-134">Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.</span><span class="sxs-lookup"><span data-stu-id="6beda-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="6beda-135">Dodaj inicjatora nasion</span><span class="sxs-lookup"><span data-stu-id="6beda-135">Add the seed initializer</span></span>

<span data-ttu-id="6beda-136">W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="6beda-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="6beda-137">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="6beda-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6beda-138">Wywołaj metodę seed, przekazując do niej kontekst.</span><span class="sxs-lookup"><span data-stu-id="6beda-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="6beda-139">Zutylizuj kontekst po zakończeniu metody inicjatora.</span><span class="sxs-lookup"><span data-stu-id="6beda-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="6beda-140">Poniższy kod przedstawia zaktualizowany plik *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="6beda-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="6beda-141">Następujący wyjątek `Update-Database` występuje, gdy nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="6beda-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="6beda-142">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6beda-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-143">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6beda-144">Usuń wszystkie rekordy w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-144">Delete all the records in the DB.</span></span> <span data-ttu-id="6beda-145">Można to zrobić za pomocą linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="6beda-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="6beda-146">Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="6beda-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="6beda-147">Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="6beda-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="6beda-148">Można to zrobić za pomocą dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="6beda-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="6beda-149">Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę:**</span><span class="sxs-lookup"><span data-stu-id="6beda-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ikona zasobnika systemowego usługi IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * <span data-ttu-id="6beda-152">Jeśli program VS jest uruchomiony w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="6beda-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="6beda-153">Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5.</span><span class="sxs-lookup"><span data-stu-id="6beda-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6beda-154">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6beda-155">Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony).</span><span class="sxs-lookup"><span data-stu-id="6beda-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="6beda-156">Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="6beda-157">Aplikacja pokazuje dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="6beda-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="6beda-158">Następny samouczek poprawi prezentację danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6beda-159">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6beda-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6beda-160">[Poprzedni: Rusztowanie Razor Strony](xref:tutorials/razor-pages/page)
> [Dalej: Aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="6beda-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="6beda-161">Obiekt `RazorPagesMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="6beda-162">Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera Iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="6beda-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-163">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6beda-164">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="6beda-165">Aby uzyskać więcej informacji `ConfigureServices`na temat metod stosowanych w programie , zobacz:</span><span class="sxs-lookup"><span data-stu-id="6beda-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="6beda-166">[Ogólne rozporządzenie o ochronie danych (RODO)](xref:security/gdpr) w `CookiePolicyOptions`ASP.NET Core dla .</span><span class="sxs-lookup"><span data-stu-id="6beda-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="6beda-167">Wersja setcompatibility</span><span class="sxs-lookup"><span data-stu-id="6beda-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="6beda-168">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik .</span><span class="sxs-lookup"><span data-stu-id="6beda-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="6beda-169">W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="6beda-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-170">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6beda-171">Wartość nazwy bazy danych`Database={Database name}`( ) będzie inna dla wygenerowanego kodu.</span><span class="sxs-lookup"><span data-stu-id="6beda-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="6beda-172">Wartość nazwy jest dowolna.</span><span class="sxs-lookup"><span data-stu-id="6beda-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6beda-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6beda-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6beda-174">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="6beda-175">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania ciągu połączenia na serwerze prawdziwej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="6beda-176">Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="6beda-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-177">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="6beda-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6beda-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6beda-179">LocalDB to lekka wersja aparatu bazy danych programu SQL Server Express przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="6beda-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="6beda-180">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="6beda-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6beda-181">Domyślnie baza danych `*.mdf` LocalDB `C:/Users/<user/>` tworzy pliki w katalogu.</span><span class="sxs-lookup"><span data-stu-id="6beda-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="6beda-182">Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="6beda-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](sql/_static/ssox.png)

* <span data-ttu-id="6beda-184">Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz **polecenie Wyświetl projektanta:**</span><span class="sxs-lookup"><span data-stu-id="6beda-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu kontekstowe otwarte w tabeli Film](sql/_static/design.png)

  ![Otwarcie tabeli filmów w projektancie](sql/_static/dv.png)

<span data-ttu-id="6beda-187">Zanotuj ikonę klawisza obok pozycji `ID`.</span><span class="sxs-lookup"><span data-stu-id="6beda-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="6beda-188">Domyślnie EF tworzy właściwość o nazwie `ID` klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="6beda-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="6beda-189">Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz polecenie **Wyświetl dane:**</span><span class="sxs-lookup"><span data-stu-id="6beda-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Otwieranie tabeli filmowych z danymi tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6beda-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6beda-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6beda-192">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="6beda-193">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="6beda-193">Seed the database</span></span>

<span data-ttu-id="6beda-194">Utwórz nową `SeedData` klasę o nazwie w folderze *Modele* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6beda-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="6beda-195">Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.</span><span class="sxs-lookup"><span data-stu-id="6beda-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="6beda-196">Dodaj inicjatora nasion</span><span class="sxs-lookup"><span data-stu-id="6beda-196">Add the seed initializer</span></span>

<span data-ttu-id="6beda-197">W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="6beda-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="6beda-198">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="6beda-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6beda-199">Wywołaj metodę seed, przekazując do niej kontekst.</span><span class="sxs-lookup"><span data-stu-id="6beda-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="6beda-200">Zutylizuj kontekst po zakończeniu metody inicjatora.</span><span class="sxs-lookup"><span data-stu-id="6beda-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="6beda-201">Poniższy kod przedstawia zaktualizowany plik *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="6beda-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="6beda-202">Aplikacja produkcyjna nie `Database.Migrate`będzie wywoływać .</span><span class="sxs-lookup"><span data-stu-id="6beda-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="6beda-203">Jest dodawany do poprzedniego kodu, aby zapobiec `Update-Database` następującemu wyjątkowi, gdy nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="6beda-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="6beda-204">SqlException: Nie można otworzyć bazy danych "RazorPagesMovieContext-21" wymagane przez login.</span><span class="sxs-lookup"><span data-stu-id="6beda-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="6beda-205">Logowanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="6beda-205">The login failed.</span></span>
<span data-ttu-id="6beda-206">Nie można zalogować się dla użytkownika "nazwa użytkownika".</span><span class="sxs-lookup"><span data-stu-id="6beda-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="6beda-207">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6beda-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6beda-208">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6beda-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6beda-209">Usuń wszystkie rekordy w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-209">Delete all the records in the DB.</span></span> <span data-ttu-id="6beda-210">Można to zrobić za pomocą linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="6beda-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="6beda-211">Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="6beda-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="6beda-212">Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="6beda-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="6beda-213">Można to zrobić za pomocą dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="6beda-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="6beda-214">Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę:**</span><span class="sxs-lookup"><span data-stu-id="6beda-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ikona zasobnika systemowego usługi IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * <span data-ttu-id="6beda-217">Jeśli program VS jest uruchomiony w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="6beda-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="6beda-218">Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5.</span><span class="sxs-lookup"><span data-stu-id="6beda-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6beda-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6beda-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6beda-220">Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony).</span><span class="sxs-lookup"><span data-stu-id="6beda-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="6beda-221">Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="6beda-222">Aplikacja pokazuje dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="6beda-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6beda-223">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6beda-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6beda-224">Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony).</span><span class="sxs-lookup"><span data-stu-id="6beda-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="6beda-225">Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="6beda-226">Aplikacja pokazuje dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="6beda-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="6beda-227">Aplikacja pokazuje dane rozstawione:</span><span class="sxs-lookup"><span data-stu-id="6beda-227">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w Chrome pokazująca dane filmu](sql/_static/m55.png)

<span data-ttu-id="6beda-229">Następny samouczek oczyści prezentację danych.</span><span class="sxs-lookup"><span data-stu-id="6beda-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6beda-230">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6beda-230">Additional resources</span></span>

* [<span data-ttu-id="6beda-231">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="6beda-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="6beda-232">[Poprzedni: Rusztowanie Razor Strony](xref:tutorials/razor-pages/page)
> [Dalej: Aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="6beda-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
