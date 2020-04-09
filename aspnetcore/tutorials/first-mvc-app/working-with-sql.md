---
title: Praca z sqlem w ASP.NET podstawowej aplikacji MVC
author: rick-anderson
description: Dowiedz się więcej o korzystaniu z usługi SQL Server LocalDB lub SQLite w ASP.NET aplikacji Core MVC.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665040"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="1c29d-103">Praca z sql w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c29d-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1c29d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c29d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1c29d-105">Obiekt `MvcMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="1c29d-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="1c29d-106">Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w pliku *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="1c29d-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c29d-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c29d-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1c29d-108">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik .</span><span class="sxs-lookup"><span data-stu-id="1c29d-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1c29d-109">W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c29d-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1c29d-110">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1c29d-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="1c29d-111">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik .</span><span class="sxs-lookup"><span data-stu-id="1c29d-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1c29d-112">W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c29d-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="1c29d-113">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania ciągu połączenia na produkcyjny program SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1c29d-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="1c29d-114">Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="1c29d-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c29d-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c29d-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="1c29d-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1c29d-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1c29d-117">LocalDB to lekka wersja aparatu baz danych programu SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="1c29d-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="1c29d-118">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c29d-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1c29d-119">Domyślnie baza danych LocalDB tworzy pliki *mdf* w katalogu *C:/Users/{user}.*</span><span class="sxs-lookup"><span data-stu-id="1c29d-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="1c29d-120">Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1c29d-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](working-with-sql/_static/ssox.png)

* <span data-ttu-id="1c29d-122">Kliknij prawym `Movie` przyciskiem myszy tabelę **> Zobacz projektanta**</span><span class="sxs-lookup"><span data-stu-id="1c29d-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/design.png)

  ![Otwarcie tabeli filmów w projektancie](working-with-sql/_static/dv.png)

<span data-ttu-id="1c29d-125">Zanotuj ikonę klawisza obok pozycji `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c29d-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="1c29d-126">Domyślnie EF spowoduje, że `ID` właściwość o nazwie klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="1c29d-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="1c29d-127">Kliknij prawym `Movie` przyciskiem myszy tabelę **> Wyświetl dane**</span><span class="sxs-lookup"><span data-stu-id="1c29d-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/ssox2.png)

  ![Otwieranie tabeli filmowych z danymi tabeli](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1c29d-130">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1c29d-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="1c29d-131">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="1c29d-131">Seed the database</span></span>

<span data-ttu-id="1c29d-132">Utwórz nową `SeedData` klasę o nazwie w folderze *Modele.*</span><span class="sxs-lookup"><span data-stu-id="1c29d-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="1c29d-133">Zastąp wygenerowany kod następującymi:</span><span class="sxs-lookup"><span data-stu-id="1c29d-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="1c29d-134">Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.</span><span class="sxs-lookup"><span data-stu-id="1c29d-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="1c29d-135">Dodaj inicjatora nasion</span><span class="sxs-lookup"><span data-stu-id="1c29d-135">Add the seed initializer</span></span>

<span data-ttu-id="1c29d-136">Zastąp zawartość *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1c29d-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="1c29d-137">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1c29d-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c29d-138">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c29d-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c29d-139">Usuń wszystkie rekordy w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="1c29d-139">Delete all the records in the DB.</span></span> <span data-ttu-id="1c29d-140">Można to zrobić za pomocą linków do usuwania w przeglądarce lub z SSOX.</span><span class="sxs-lookup"><span data-stu-id="1c29d-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="1c29d-141">Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="1c29d-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="1c29d-142">Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="1c29d-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="1c29d-143">Można to zrobić za pomocą dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="1c29d-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="1c29d-144">Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**</span><span class="sxs-lookup"><span data-stu-id="1c29d-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ikona zasobnika systemowego usługi IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="1c29d-147">Jeśli korzystasz z usługi VS w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania</span><span class="sxs-lookup"><span data-stu-id="1c29d-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="1c29d-148">Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5</span><span class="sxs-lookup"><span data-stu-id="1c29d-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1c29d-149">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1c29d-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1c29d-150">Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony).</span><span class="sxs-lookup"><span data-stu-id="1c29d-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="1c29d-151">Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="1c29d-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="1c29d-152">Aplikacja pokazuje dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="1c29d-152">The app shows the seeded data.</span></span>

![Aplikacja MVC Movie otwarta w programie Microsoft Edge z danymi filmowymi](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1c29d-154">[Poprzedni](adding-model.md)
> [następny](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="1c29d-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1c29d-155">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c29d-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1c29d-156">Obiekt `MvcMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="1c29d-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="1c29d-157">Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w pliku *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="1c29d-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c29d-158">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c29d-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="1c29d-159">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik .</span><span class="sxs-lookup"><span data-stu-id="1c29d-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1c29d-160">W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c29d-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1c29d-161">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1c29d-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="1c29d-162">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik .</span><span class="sxs-lookup"><span data-stu-id="1c29d-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1c29d-163">W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c29d-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="1c29d-164">Podczas wdrażania aplikacji na serwerze testowym lub produkcyjnym można użyć zmiennej środowiskowej lub innego podejścia, aby ustawić ciąg połączenia na prawdziwy program SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1c29d-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="1c29d-165">Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="1c29d-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c29d-166">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c29d-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="1c29d-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1c29d-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1c29d-168">LocalDB to lekka wersja aparatu baz danych programu SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="1c29d-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="1c29d-169">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c29d-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1c29d-170">Domyślnie baza danych LocalDB tworzy pliki *mdf* w katalogu *C:/Users/{user}.*</span><span class="sxs-lookup"><span data-stu-id="1c29d-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="1c29d-171">Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1c29d-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](working-with-sql/_static/ssox.png)

* <span data-ttu-id="1c29d-173">Kliknij prawym `Movie` przyciskiem myszy tabelę **> Zobacz projektanta**</span><span class="sxs-lookup"><span data-stu-id="1c29d-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/design.png)

  ![Otwarcie tabeli filmów w projektancie](working-with-sql/_static/dv.png)

<span data-ttu-id="1c29d-176">Zanotuj ikonę klawisza obok pozycji `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c29d-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="1c29d-177">Domyślnie EF spowoduje, że `ID` właściwość o nazwie klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="1c29d-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="1c29d-178">Kliknij prawym `Movie` przyciskiem myszy tabelę **> Wyświetl dane**</span><span class="sxs-lookup"><span data-stu-id="1c29d-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/ssox2.png)

  ![Otwieranie tabeli filmowych z danymi tabeli](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1c29d-181">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1c29d-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="1c29d-182">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="1c29d-182">Seed the database</span></span>

<span data-ttu-id="1c29d-183">Utwórz nową `SeedData` klasę o nazwie w folderze *Modele.*</span><span class="sxs-lookup"><span data-stu-id="1c29d-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="1c29d-184">Zastąp wygenerowany kod następującymi:</span><span class="sxs-lookup"><span data-stu-id="1c29d-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="1c29d-185">Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.</span><span class="sxs-lookup"><span data-stu-id="1c29d-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="1c29d-186">Dodaj inicjatora nasion</span><span class="sxs-lookup"><span data-stu-id="1c29d-186">Add the seed initializer</span></span>

<span data-ttu-id="1c29d-187">Zastąp zawartość *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1c29d-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="1c29d-188">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1c29d-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c29d-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c29d-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c29d-190">Usuń wszystkie rekordy w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="1c29d-190">Delete all the records in the DB.</span></span> <span data-ttu-id="1c29d-191">Można to zrobić za pomocą linków do usuwania w przeglądarce lub z SSOX.</span><span class="sxs-lookup"><span data-stu-id="1c29d-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="1c29d-192">Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="1c29d-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="1c29d-193">Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="1c29d-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="1c29d-194">Można to zrobić za pomocą dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="1c29d-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="1c29d-195">Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**</span><span class="sxs-lookup"><span data-stu-id="1c29d-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ikona zasobnika systemowego usługi IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="1c29d-198">Jeśli korzystasz z usługi VS w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania</span><span class="sxs-lookup"><span data-stu-id="1c29d-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="1c29d-199">Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5</span><span class="sxs-lookup"><span data-stu-id="1c29d-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1c29d-200">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1c29d-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1c29d-201">Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony).</span><span class="sxs-lookup"><span data-stu-id="1c29d-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="1c29d-202">Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="1c29d-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="1c29d-203">Aplikacja pokazuje dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="1c29d-203">The app shows the seeded data.</span></span>

![Aplikacja MVC Movie otwarta w programie Microsoft Edge z danymi filmowymi](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1c29d-205">[Poprzedni](adding-model.md)
> [następny](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="1c29d-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
