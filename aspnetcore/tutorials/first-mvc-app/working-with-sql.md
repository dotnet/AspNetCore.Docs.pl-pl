---
title: Część 5. Korzystanie z bazy danych w aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 5. Dodawanie modelu do aplikacji ASP.NET Core MVC
ms.author: riande
ms.date: 11/10/2020
no-loc:
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422655"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="6b80b-103">Część 5. Korzystanie z bazy danych w aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6b80b-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b80b-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b80b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6b80b-105">`MvcMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6b80b-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="6b80b-106">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="6b80b-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b80b-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b80b-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="6b80b-108">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="6b80b-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="6b80b-109">W przypadku lokalnego opracowywania pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="6b80b-109">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/:::no-loc(appsettings.json):::?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6b80b-110">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6b80b-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="6b80b-111">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="6b80b-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="6b80b-112">W przypadku lokalnego opracowywania pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="6b80b-112">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="6b80b-113">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia do SQL Server produkcyjnej.</span><span class="sxs-lookup"><span data-stu-id="6b80b-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="6b80b-114">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="6b80b-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b80b-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b80b-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="6b80b-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6b80b-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6b80b-117">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="6b80b-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="6b80b-118">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="6b80b-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6b80b-119">Domyślnie baza danych LocalDB tworzy pliki *MDF* w katalogu *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="6b80b-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="6b80b-120">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="6b80b-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](working-with-sql/_static/ssox.png)

* <span data-ttu-id="6b80b-122">Kliknij prawym przyciskiem myszy `Movie` tabelę **> View Designer**</span><span class="sxs-lookup"><span data-stu-id="6b80b-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu kontekstowe jest otwarte w tabeli filmów](working-with-sql/_static/design.png)

  ![Tabela filmów otwarta w projektancie](working-with-sql/_static/dv.png)

<span data-ttu-id="6b80b-125">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="6b80b-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="6b80b-126">Domyślnie EF utworzy właściwość o nazwie `ID` klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="6b80b-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="6b80b-127">Kliknij prawym przyciskiem myszy `Movie` tabelę, **> wyświetlić dane**</span><span class="sxs-lookup"><span data-stu-id="6b80b-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu kontekstowe jest otwarte w tabeli filmów](working-with-sql/_static/ssox2.png)

  ![Otwórz tabelę filmów pokazującą dane tabeli](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6b80b-130">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6b80b-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="6b80b-131">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="6b80b-131">Seed the database</span></span>

<span data-ttu-id="6b80b-132">Utwórz nową klasę o nazwie `SeedData` w folderze *models* .</span><span class="sxs-lookup"><span data-stu-id="6b80b-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="6b80b-133">Zastąp wygenerowany kod następującym:</span><span class="sxs-lookup"><span data-stu-id="6b80b-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="6b80b-134">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="6b80b-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="6b80b-135">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="6b80b-135">Add the seed initializer</span></span>

<span data-ttu-id="6b80b-136">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6b80b-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="6b80b-137">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6b80b-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b80b-138">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b80b-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b80b-139">Usuń wszystkie rekordy z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6b80b-139">Delete all the records in the DB.</span></span> <span data-ttu-id="6b80b-140">Można to zrobić za pomocą linków usuwania w przeglądarce lub z SSOX.</span><span class="sxs-lookup"><span data-stu-id="6b80b-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="6b80b-141">Wymuś inicjalizację aplikacji (wywołaj metody z `Startup` klasy), aby była uruchamiana Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="6b80b-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="6b80b-142">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="6b80b-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="6b80b-143">Można to zrobić przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="6b80b-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="6b80b-144">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**</span><span class="sxs-lookup"><span data-stu-id="6b80b-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ikona paska zadań IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="6b80b-147">W przypadku uruchamiania programu VS w trybie innym niż debugowanie naciśnij klawisz F5, aby uruchomić w trybie debugowania</span><span class="sxs-lookup"><span data-stu-id="6b80b-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="6b80b-148">W przypadku uruchamiania programu VS w trybie debugowania Zatrzymaj debuger i naciśnij klawisz F5.</span><span class="sxs-lookup"><span data-stu-id="6b80b-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6b80b-149">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6b80b-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6b80b-150">Usuń wszystkie rekordy z bazy danych (w związku z czym zostanie uruchomiona Metoda inicjatora).</span><span class="sxs-lookup"><span data-stu-id="6b80b-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="6b80b-151">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="6b80b-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="6b80b-152">Aplikacja pokazuje dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="6b80b-152">The app shows the seeded data.</span></span>

![Aplikacja filmowa MVC otwarta w przeglądarce Microsoft Edge pokazująca dane filmu](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="6b80b-154">[Poprzedni](adding-model.md) 
>  [Dalej](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="6b80b-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b80b-155">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b80b-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6b80b-156">`MvcMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6b80b-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="6b80b-157">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="6b80b-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b80b-158">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b80b-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="6b80b-159">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="6b80b-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="6b80b-160">W przypadku lokalnego opracowywania pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="6b80b-160">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/:::no-loc(appsettings.json):::?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6b80b-161">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6b80b-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="6b80b-162">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="6b80b-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="6b80b-163">W przypadku lokalnego opracowywania pobiera parametry połączenia z *:::no-loc(appsettings.json):::* pliku:</span><span class="sxs-lookup"><span data-stu-id="6b80b-163">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="6b80b-164">Podczas wdrażania aplikacji na serwerze testowym lub produkcyjnym można użyć zmiennej środowiskowej lub innego podejścia do ustawiania parametrów połączenia dla rzeczywistych SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6b80b-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="6b80b-165">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="6b80b-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b80b-166">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b80b-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="6b80b-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6b80b-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6b80b-168">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="6b80b-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="6b80b-169">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="6b80b-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6b80b-170">Domyślnie baza danych LocalDB tworzy pliki *MDF* w katalogu *C:/Users/{User}* .</span><span class="sxs-lookup"><span data-stu-id="6b80b-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="6b80b-171">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="6b80b-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](working-with-sql/_static/ssox.png)

* <span data-ttu-id="6b80b-173">Kliknij prawym przyciskiem myszy `Movie` tabelę **> View Designer**</span><span class="sxs-lookup"><span data-stu-id="6b80b-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu kontekstowe jest otwarte w tabeli filmów](working-with-sql/_static/design.png)

  ![Tabela filmów otwarta w projektancie](working-with-sql/_static/dv.png)

<span data-ttu-id="6b80b-176">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="6b80b-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="6b80b-177">Domyślnie EF utworzy właściwość o nazwie `ID` klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="6b80b-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="6b80b-178">Kliknij prawym przyciskiem myszy `Movie` tabelę, **> wyświetlić dane**</span><span class="sxs-lookup"><span data-stu-id="6b80b-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu kontekstowe jest otwarte w tabeli filmów](working-with-sql/_static/ssox2.png)

  ![Otwórz tabelę filmów pokazującą dane tabeli](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6b80b-181">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6b80b-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="6b80b-182">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="6b80b-182">Seed the database</span></span>

<span data-ttu-id="6b80b-183">Utwórz nową klasę o nazwie `SeedData` w folderze *models* .</span><span class="sxs-lookup"><span data-stu-id="6b80b-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="6b80b-184">Zastąp wygenerowany kod następującym:</span><span class="sxs-lookup"><span data-stu-id="6b80b-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="6b80b-185">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="6b80b-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="6b80b-186">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="6b80b-186">Add the seed initializer</span></span>

<span data-ttu-id="6b80b-187">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6b80b-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="6b80b-188">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6b80b-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b80b-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b80b-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b80b-190">Usuń wszystkie rekordy z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6b80b-190">Delete all the records in the DB.</span></span> <span data-ttu-id="6b80b-191">Można to zrobić za pomocą linków usuwania w przeglądarce lub z SSOX.</span><span class="sxs-lookup"><span data-stu-id="6b80b-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="6b80b-192">Wymuś inicjalizację aplikacji (wywołaj metody z `Startup` klasy), aby była uruchamiana Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="6b80b-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="6b80b-193">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="6b80b-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="6b80b-194">Można to zrobić przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="6b80b-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="6b80b-195">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**</span><span class="sxs-lookup"><span data-stu-id="6b80b-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ikona paska zadań IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="6b80b-198">W przypadku uruchamiania programu VS w trybie innym niż debugowanie naciśnij klawisz F5, aby uruchomić w trybie debugowania</span><span class="sxs-lookup"><span data-stu-id="6b80b-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="6b80b-199">W przypadku uruchamiania programu VS w trybie debugowania Zatrzymaj debuger i naciśnij klawisz F5.</span><span class="sxs-lookup"><span data-stu-id="6b80b-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6b80b-200">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6b80b-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6b80b-201">Usuń wszystkie rekordy z bazy danych (w związku z czym zostanie uruchomiona Metoda inicjatora).</span><span class="sxs-lookup"><span data-stu-id="6b80b-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="6b80b-202">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="6b80b-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="6b80b-203">Aplikacja pokazuje dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="6b80b-203">The app shows the seeded data.</span></span>

![Aplikacja filmowa MVC otwarta w przeglądarce Microsoft Edge pokazująca dane filmu](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="6b80b-205">[Poprzedni](adding-model.md) 
>  [Dalej](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="6b80b-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
