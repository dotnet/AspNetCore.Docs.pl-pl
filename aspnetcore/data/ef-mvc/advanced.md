---
title: 'Samouczek: informacje na temat scenariuszy zaawansowanych — ASP.NET MVC z EF Core'
description: W tym samouczku przedstawiono przydatne tematy umożliwiające przechodzenie poza podstawowe informacje na temat tworzenia ASP.NET Core aplikacji sieci Web korzystających z Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/advanced
ms.openlocfilehash: ebeb581cf79f2d2ab60de7df43d042fa3185cd32
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212733"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="8ddf6-103">Samouczek: informacje na temat scenariuszy zaawansowanych — ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="8ddf6-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="8ddf6-104">W poprzednim samouczku zaimplementowano dziedziczenie w hierarchii na poziomie tabeli.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="8ddf6-105">W tym samouczku przedstawiono kilka tematów, które są przydatne, gdy wykraczasz poza podstawowe informacje na temat tworzenia ASP.NET Core aplikacji sieci Web korzystających z Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="8ddf6-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ddf6-107">Wykonywanie nieprzetworzonych zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="8ddf6-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="8ddf6-108">Wywoływanie zapytania w celu zwrócenia jednostek</span><span class="sxs-lookup"><span data-stu-id="8ddf6-108">Call a query to return entities</span></span>
> * <span data-ttu-id="8ddf6-109">Wywoływanie zapytania w celu zwrócenia innych typów</span><span class="sxs-lookup"><span data-stu-id="8ddf6-109">Call a query to return other types</span></span>
> * <span data-ttu-id="8ddf6-110">Wywoływanie zapytania Update</span><span class="sxs-lookup"><span data-stu-id="8ddf6-110">Call an update query</span></span>
> * <span data-ttu-id="8ddf6-111">Badanie zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="8ddf6-111">Examine SQL queries</span></span>
> * <span data-ttu-id="8ddf6-112">Tworzenie warstwy abstrakcji</span><span class="sxs-lookup"><span data-stu-id="8ddf6-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="8ddf6-113">Informacje o automatycznym wykrywaniu zmian</span><span class="sxs-lookup"><span data-stu-id="8ddf6-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="8ddf6-114">Dowiedz się więcej na temat EF Core kodu źródłowego i planów programistycznych</span><span class="sxs-lookup"><span data-stu-id="8ddf6-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="8ddf6-115">Dowiedz się, jak używać dynamicznego LINQ do uproszczenia kodu</span><span class="sxs-lookup"><span data-stu-id="8ddf6-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8ddf6-116">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8ddf6-116">Prerequisites</span></span>

* [<span data-ttu-id="8ddf6-117">Implementowanie dziedziczenia</span><span class="sxs-lookup"><span data-stu-id="8ddf6-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="8ddf6-118">Wykonywanie nieprzetworzonych zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="8ddf6-118">Perform raw SQL queries</span></span>

<span data-ttu-id="8ddf6-119">Jedną z zalet korzystania z Entity Framework jest to, że pozwala to uniknąć zbyt ścisłego powiązana kodu z konkretną metodą przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="8ddf6-120">Robi to przez generowanie zapytań i poleceń SQL, które również uwalniają użytkownika od konieczności pisania.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="8ddf6-121">Ale istnieją wyjątkowe scenariusze, które należy wykonać w celu uruchomienia określonych zapytań SQL, które zostały utworzone ręcznie.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="8ddf6-122">W tych scenariuszach Entity Framework interfejs API Code First zawiera metody umożliwiające przekazywanie poleceń SQL bezpośrednio do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="8ddf6-123">Dostępne są następujące opcje w EF Core 1,0:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="8ddf6-124">Użyj `DbSet.FromSql` metody dla zapytań, które zwracają typy jednostek.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="8ddf6-125">Zwracane obiekty muszą być typu oczekiwanego przez `DbSet` obiekt i są automatycznie śledzone przez kontekst bazy danych, chyba że zostanie [wyłączone śledzenie](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="8ddf6-126">Użyj `Database.ExecuteSqlCommand` polecenia dla poleceń niezwiązanych z kwerendą.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="8ddf6-127">Jeśli musisz uruchomić zapytanie, które zwraca typy, które nie są jednostkami, możesz użyć ADO.NET z połączeniem bazy danych udostępnionym przez EF.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="8ddf6-128">Zwrócone dane nie są śledzone przez kontekst bazy danych, nawet jeśli ta metoda jest używana do pobierania typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="8ddf6-129">Gdy jest zawsze prawdziwe w przypadku wykonywania poleceń SQL w aplikacji sieci Web, należy podjąć środki ostrożności, aby chronić witrynę przed atakami polegającymi na iniekcji SQL.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="8ddf6-130">Jednym ze sposobów jest użycie zapytań parametrycznych w celu upewnienia się, że ciągi przesłane przez stronę sieci Web nie mogą być interpretowane jako polecenia SQL.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="8ddf6-131">W tym samouczku użyjesz zapytań parametrycznych podczas integrowania danych wejściowych użytkownika z zapytaniem.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="8ddf6-132">Wywoływanie zapytania w celu zwrócenia jednostek</span><span class="sxs-lookup"><span data-stu-id="8ddf6-132">Call a query to return entities</span></span>

<span data-ttu-id="8ddf6-133">`DbSet<TEntity>`Klasa zawiera metodę, której można użyć do wykonania zapytania zwracającego jednostkę typu `TEntity` .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="8ddf6-134">Aby zobaczyć, jak to działa, Zmień kod w `Details` metodzie kontrolera działu.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="8ddf6-135">W *DepartmentsController.cs*, w `Details` metodzie, Zastąp kod pobierający dział z `FromSql` wywołaniem metody, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="8ddf6-136">Aby sprawdzić, czy nowy kod działa prawidłowo, wybierz kartę **działy** , a następnie **szczegóły** dla jednego z działów.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Szczegóły działu](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="8ddf6-138">Wywoływanie zapytania w celu zwrócenia innych typów</span><span class="sxs-lookup"><span data-stu-id="8ddf6-138">Call a query to return other types</span></span>

<span data-ttu-id="8ddf6-139">Wcześniej utworzono siatkę statystyk uczniów dla strony informacje, która wykazała liczbę studentów dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="8ddf6-140">Uzyskano dane z zestawu jednostek studentów ( `_context.Students` ) i używane LINQ do projekcji wyników do listy `EnrollmentDateGroup` obiektów modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="8ddf6-141">Załóżmy, że chcesz napisać sam kod SQL, zamiast używać LINQ.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="8ddf6-142">W tym celu należy uruchomić zapytanie SQL zwracające coś innego niż obiekty Entity.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="8ddf6-143">W EF Core 1,0 jednym ze sposobów jest zapisanie kodu ADO.NET i nawiązanie połączenia z bazą danych EF.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="8ddf6-144">W *HomeController.cs*Zastąp `About` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="8ddf6-145">Dodaj instrukcję using:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="8ddf6-146">Uruchom aplikację i przejdź do strony informacje.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-146">Run the app and go to the About page.</span></span> <span data-ttu-id="8ddf6-147">Wyświetla te same dane, które wcześniej zaistniały.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-147">It displays the same data it did before.</span></span>

![Informacje o stronie](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="8ddf6-149">Wywoływanie zapytania Update</span><span class="sxs-lookup"><span data-stu-id="8ddf6-149">Call an update query</span></span>

<span data-ttu-id="8ddf6-150">Załóżmy, że administratorzy uniwersytetów firmy Contoso chcą wykonywać globalne zmiany w bazie danych, na przykład zmieniając liczbę kredytów dla każdego kursu.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="8ddf6-151">Jeśli University ma dużą liczbę kursów, może być nieefektywny do pobrania ich wszystkie jako jednostki i indywidualnie zmienić.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="8ddf6-152">W tej sekcji zostanie zaimplementowana Strona sieci Web, która umożliwia użytkownikowi określenie współczynnika, przez który należy zmienić liczbę kredytów dla wszystkich kursów, i wprowadzić zmiany przez wykonanie instrukcji SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="8ddf6-153">Strona sieci Web będzie wyglądać jak na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-153">The web page will look like the following illustration:</span></span>

![Strona aktualizacji kredytów kursu](advanced/_static/update-credits.png)

<span data-ttu-id="8ddf6-155">W *CoursesController.cs*Dodaj metody UpdateCourseCredits dla narzędzia HttpGet i HTTPPOST:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="8ddf6-156">Gdy kontroler przetwarza żądanie narzędzia HttpGet, nic nie jest zwracane w `ViewData["RowsAffected"]` , a widok wyświetla puste pole tekstowe i przycisk Prześlij, jak pokazano na poprzedniej ilustracji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="8ddf6-157">Po kliknięciu przycisku **Aktualizuj** Metoda HTTPPOST jest wywoływana, a mnożnik ma wartość wprowadzoną w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="8ddf6-158">Następnie kod wykonuje instrukcję SQL, która aktualizuje kursy i zwraca liczbę odnośnych wierszy do widoku w `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="8ddf6-159">Gdy widok pobiera `RowsAffected` wartość, wyświetlana jest liczba zaktualizowanych wierszy.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="8ddf6-160">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *widoki/kursy* , a następnie kliknij polecenie **Dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="8ddf6-161">W oknie dialogowym **Dodaj nowy element** kliknij **ASP.NET Core** w obszarze **zainstalowane** w okienku po lewej stronie, kliknij pozycję \*\* Razor Wyświetl\*\*i nazwij nowy widok *UpdateCourseCredits. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="8ddf6-162">W obszarze *widoki/kursy/UpdateCourseCredits. cshtml*Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="8ddf6-163">Uruchom `UpdateCourseCredits` metodę, wybierając kartę **kursy** , a następnie dodając wartość "/UpdateCourseCredits" na końcu adresu URL na pasku adresu przeglądarki (na przykład: `http://localhost:5813/Courses/UpdateCourseCredits` ).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="8ddf6-164">Wprowadź liczbę w polu tekstowym:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-164">Enter a number in the text box:</span></span>

![Strona aktualizacji kredytów kursu](advanced/_static/update-credits.png)

<span data-ttu-id="8ddf6-166">Kliknij przycisk **Update** (Aktualizuj).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-166">Click **Update**.</span></span> <span data-ttu-id="8ddf6-167">Zostanie wyświetlona liczba zaatakowanych wierszy:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-167">You see the number of rows affected:</span></span>

![Aktualizuj środki na stronie kredytów kursu](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="8ddf6-169">Kliknij przycisk **Wstecz, aby** wyświetlić listę kursów o zmienionej liczbie kredytów.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="8ddf6-170">Należy pamiętać, że kod produkcyjny zapewni, że aktualizacje będą zawsze powodować prawidłowe dane.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="8ddf6-171">Kod uproszczony przedstawiony w tym miejscu może pomnożyć liczbę kredytów wystarczającą do wyniku liczby większe niż 5.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="8ddf6-172">( `Credits` Właściwość ma `[Range(0, 5)]` atrybut.) Kwerenda Update będzie działała, ale nieprawidłowe dane mogą spowodować nieoczekiwane wyniki w innych częściach systemu, które zakładają, że liczba kredytów wynosi 5 lub mniej.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="8ddf6-173">Aby uzyskać więcej informacji na temat nieprzetworzonych zapytań SQL, zobacz [RAW zapytania SQL](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="8ddf6-174">Badanie zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="8ddf6-174">Examine SQL queries</span></span>

<span data-ttu-id="8ddf6-175">Czasami warto zobaczyć rzeczywiste zapytania SQL, które są wysyłane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="8ddf6-176">Wbudowane funkcje rejestrowania dla ASP.NET Core są automatycznie używane przez EF Core do zapisywania dzienników zawierających dane SQL na potrzeby zapytań i aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="8ddf6-177">W tej sekcji zobaczysz przykłady rejestrowania w programie SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="8ddf6-178">Otwórz *StudentsController.cs* i w `Details` metodzie Ustaw punkt przerwania w `if (student == null)` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="8ddf6-179">Uruchom aplikację w trybie debugowania i przejdź do strony szczegółów dla ucznia.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="8ddf6-180">Przejdź do okna **dane wyjściowe** zawierające dane wyjściowe debugowania, a następnie Wyświetl zapytanie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="8ddf6-181">Zobaczysz coś tutaj, co może się zdarzyć: kod SQL wybiera do 2 wierszy ( `TOP(2)` ) z tabeli Person.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="8ddf6-182">`SingleOrDefaultAsync`Metoda nie ma wpływu na 1 wiersz na serwerze.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="8ddf6-183">Oto dlaczego:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-183">Here's why:</span></span>

* <span data-ttu-id="8ddf6-184">Jeśli zapytanie zwróci wiele wierszy, metoda zwraca wartość null.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="8ddf6-185">Aby określić, czy zapytanie zwróci wiele wierszy, EF musi sprawdzić, czy zwraca co najmniej 2.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="8ddf6-186">Należy pamiętać, że nie jest konieczne używanie trybu debugowania i zatrzymywanie w punkcie przerwania w celu uzyskania danych wyjściowych rejestrowania w oknie **danych wyjściowych** .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="8ddf6-187">Jest to wygodny sposób na zatrzymanie rejestrowania w punkcie, w którym chcesz zobaczyć dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="8ddf6-188">Jeśli tego nie zrobisz, rejestrowanie jest kontynuowane, a użytkownik musi przewijać się ponownie, aby znaleźć interesujące Cię składniki.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="8ddf6-189">Tworzenie warstwy abstrakcji</span><span class="sxs-lookup"><span data-stu-id="8ddf6-189">Create an abstraction layer</span></span>

<span data-ttu-id="8ddf6-190">Wielu deweloperów pisze kod, aby zaimplementować wzorce repozytorium i jednostki pracy jako otokę otaczającą kod, który działa z Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="8ddf6-191">Wzorce te mają na celu utworzenie warstwy abstrakcji między warstwą dostępu do danych i warstwą logiki biznesowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="8ddf6-192">Wdrożenie tych wzorców może pomóc w izolowaniu aplikacji od zmian w magazynie danych oraz w celu ułatwienia zautomatyzowanych testów jednostkowych lub projektowania opartego na testach (TDD).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="8ddf6-193">Jednak zapisanie dodatkowego kodu w celu zaimplementowania tych wzorców nie zawsze jest najlepszym wyborem dla aplikacji korzystających z EF z kilku powodów:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="8ddf6-194">Sama klasa kontekstu EF izolowana od kodu z kodu specyficznego dla magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="8ddf6-195">Klasa kontekstu EF może działać jako Klasa jednostki pracy dla aktualizacji bazy danych korzystających z EF.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="8ddf6-196">EF zawiera funkcje do implementowania usługi TDD bez pisania kodu repozytorium.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="8ddf6-197">Informacje o sposobach implementacji wzorców repozytorium i jednostki pracy można znaleźć [w wersji Entity Framework 5 tej serii samouczków](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="8ddf6-198">Entity Framework Core implementuje dostawcę bazy danych w pamięci, który może być używany do testowania.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="8ddf6-199">Aby uzyskać więcej informacji, zobacz [test with inMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="8ddf6-200">Automatyczne wykrywanie zmian</span><span class="sxs-lookup"><span data-stu-id="8ddf6-200">Automatic change detection</span></span>

<span data-ttu-id="8ddf6-201">Entity Framework określa, w jaki sposób jednostka została zmieniona (i w związku z tym, które aktualizacje muszą być wysyłane do bazy danych), porównując bieżące wartości jednostki z oryginalnymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="8ddf6-202">Oryginalne wartości są przechowywane, gdy obiekt jest wysyłany lub dołączany.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="8ddf6-203">Niektóre metody, które powodują automatyczne wykrywanie zmian, są następujące:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="8ddf6-204">DbContext. metody SaveChanges</span><span class="sxs-lookup"><span data-stu-id="8ddf6-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="8ddf6-205">DbContext. entry</span><span class="sxs-lookup"><span data-stu-id="8ddf6-205">DbContext.Entry</span></span>

* <span data-ttu-id="8ddf6-206">ChangeTracker. wpisy</span><span class="sxs-lookup"><span data-stu-id="8ddf6-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="8ddf6-207">Jeśli śledzisz dużą liczbę jednostek i wywołujesz jedną z tych metod wiele razy w pętli, możesz uzyskać znaczące ulepszenia wydajności, tymczasowo wyłączając automatyczne wykrywanie zmian przy użyciu `ChangeTracker.AutoDetectChangesEnabled` właściwości.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="8ddf6-208">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="8ddf6-209">EF Core kod źródłowy i plany programistyczne</span><span class="sxs-lookup"><span data-stu-id="8ddf6-209">EF Core source code and development plans</span></span>

<span data-ttu-id="8ddf6-210">Źródło Entity Framework Core ma wartość [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="8ddf6-211">Repozytorium EF Core zawiera nocne kompilacje, Śledzenie problemów, specyfikacje funkcji, projektowanie notatek na spotkaniu i [plan do przyszłego rozwoju](https://github.com/dotnet/efcore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="8ddf6-212">Można tworzyć i znajdować usterki oraz współtworzyć.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="8ddf6-213">Chociaż kod źródłowy jest otwarty, Entity Framework Core jest w pełni obsługiwany jako produkt firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="8ddf6-214">Zespół Entity Framework firmy Microsoft zachowuje kontrolę nad tym, jakie wkłady są akceptowane i sprawdza wszystkie zmiany kodu w celu zapewnienia jakości każdej wersji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="8ddf6-215">Odtwarzanie z istniejącej bazy danych</span><span class="sxs-lookup"><span data-stu-id="8ddf6-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="8ddf6-216">Aby odtworzyć model danych, w tym klasy jednostek z istniejącej bazy danych, użyj polecenia [szkielet-DbContext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="8ddf6-217">Zobacz [samouczek z wprowadzeniem](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="8ddf6-218">Używanie dynamicznego LINQ do uproszczenia kodu</span><span class="sxs-lookup"><span data-stu-id="8ddf6-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="8ddf6-219">[Trzeci samouczek z tej serii](sort-filter-page.md) pokazuje, jak pisać kod LINQ przez znakowanie nazw kolumn w `switch` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="8ddf6-220">Z dwiema kolumnami do wyboru, to działa prawidłowo, ale jeśli masz wiele kolumn, kod może uzyskać pełne informacje.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="8ddf6-221">Aby rozwiązać ten problem, można użyć metody, `EF.Property` Aby określić nazwę właściwości jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="8ddf6-222">Aby wypróbować to podejście, Zastąp `Index` metodę w `StudentsController` poniższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="8ddf6-223">Podziękowania</span><span class="sxs-lookup"><span data-stu-id="8ddf6-223">Acknowledgments</span></span>

<span data-ttu-id="8ddf6-224">Tomasz Dykstra i Rick Anderson (Twitter @RickAndMSFT ) zapisały ten samouczek.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="8ddf6-225">Rowan Miller, Diego Vega i inni członkowie zespołu Entity Framework mogą uzyskać przeglądy kodu i pomóc w debugowaniu problemów, które powstały podczas pisania kodu dla samouczków.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="8ddf6-226">Jan Goldman i Paul pracował nad aktualizacją samouczka dla ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="8ddf6-227">Rozwiązywanie typowych problemów</span><span class="sxs-lookup"><span data-stu-id="8ddf6-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="8ddf6-228">ContosoUniversity.dll używany przez inny proces</span><span class="sxs-lookup"><span data-stu-id="8ddf6-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="8ddf6-229">Komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-229">Error message:</span></span>

> <span data-ttu-id="8ddf6-230">Nie można otworzyć elementu "...bin\Debug\netcoreapp1.0\ContosoUniversity.dll" do zapisu — "proces nie może uzyskać dostępu do pliku" ...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll ", ponieważ jest on używany przez inny proces.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="8ddf6-231">Rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-231">Solution:</span></span>

<span data-ttu-id="8ddf6-232">Zatrzymaj lokację w IIS Express.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="8ddf6-233">Przejdź do paska zadań systemu Windows, Znajdź IIS Express i kliknij prawym przyciskiem myszy jego ikonę, wybierz witrynę firmy Contoso University, a następnie kliknij pozycję **Zatrzymaj lokację**.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="8ddf6-234">Migracja szkieletowa bez kodu w metodach up i Down</span><span class="sxs-lookup"><span data-stu-id="8ddf6-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="8ddf6-235">Możliwa przyczyna:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-235">Possible cause:</span></span>

<span data-ttu-id="8ddf6-236">Polecenia EF CLI nie są automatycznie zamykane i zapisują pliki kodu.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="8ddf6-237">Jeśli masz niezapisane zmiany po uruchomieniu `migrations add` polecenia, EF nie znajdzie zmian.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="8ddf6-238">Rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-238">Solution:</span></span>

<span data-ttu-id="8ddf6-239">Uruchom `migrations remove` polecenie, Zapisz zmiany kodu i ponownie uruchom `migrations add` polecenie.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="8ddf6-240">Błędy podczas uruchamiania aktualizacji bazy danych</span><span class="sxs-lookup"><span data-stu-id="8ddf6-240">Errors while running database update</span></span>

<span data-ttu-id="8ddf6-241">Podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane, można uzyskać inne błędy.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="8ddf6-242">Jeśli wystąpią błędy migracji, nie można rozwiązać tego problemu, możesz zmienić nazwę bazy danych w parametrach połączenia lub usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="8ddf6-243">W przypadku nowej bazy danych nie ma żadnych danych do migracji, a polecenie Update-Database jest znacznie bardziej gotowe do wykonania bez błędów.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="8ddf6-244">Najprostszym podejściem jest zmiana nazwy bazy danych w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="8ddf6-245">Przy następnym uruchomieniu `database update` zostanie utworzona nowa baza danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="8ddf6-246">Aby usunąć bazę danych w programie SSOX, kliknij prawym przyciskiem myszy bazę danych, kliknij polecenie **Usuń**, a następnie w oknie dialogowym **Usuwanie bazy danych** wybierz pozycję **Zamknij istniejące połączenia** i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="8ddf6-247">Aby usunąć bazę danych przy użyciu interfejsu wiersza polecenia, uruchom `database drop` polecenie CLI:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="8ddf6-248">Błąd lokalizowania wystąpienia SQL Server</span><span class="sxs-lookup"><span data-stu-id="8ddf6-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="8ddf6-249">Komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-249">Error Message:</span></span>

> <span data-ttu-id="8ddf6-250">Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="8ddf6-251">Serwer nie został znaleziony lub był niedostępny.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="8ddf6-252">Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany do zezwalania na połączenia zdalne.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="8ddf6-253">(Dostawca: interfejsy sieciowe SQL, błąd: 26 — błąd lokalizowania określonego serwera/wystąpienia)</span><span class="sxs-lookup"><span data-stu-id="8ddf6-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="8ddf6-254">Rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-254">Solution:</span></span>

<span data-ttu-id="8ddf6-255">Sprawdź parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-255">Check the connection string.</span></span> <span data-ttu-id="8ddf6-256">Jeśli plik bazy danych został ręcznie usunięty, Zmień nazwę bazy danych w ciągu konstrukcyjnym, aby zacząć od nowa baza danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="8ddf6-257">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="8ddf6-257">Get the code</span></span>

[<span data-ttu-id="8ddf6-258">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="8ddf6-259">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8ddf6-259">Additional resources</span></span>

<span data-ttu-id="8ddf6-260">Aby uzyskać więcej informacji na temat EF Core, zobacz [dokumentację Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="8ddf6-261">Dostępna jest również książka: [Entity Framework Core w działaniu](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="8ddf6-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="8ddf6-262">Aby uzyskać informacje na temat sposobu wdrażania aplikacji sieci Web, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="8ddf6-263">Aby uzyskać informacje dotyczące innych tematów odnoszących się do ASP.NET Core MVC, takich jak uwierzytelnianie i autoryzacja, zobacz <xref:index> .</span><span class="sxs-lookup"><span data-stu-id="8ddf6-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ddf6-264">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="8ddf6-264">Next steps</span></span>

<span data-ttu-id="8ddf6-265">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="8ddf6-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ddf6-266">Wykonywanie nieprzetworzonych zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="8ddf6-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="8ddf6-267">Wywołuje zapytanie w celu zwrócenia jednostek</span><span class="sxs-lookup"><span data-stu-id="8ddf6-267">Called a query to return entities</span></span>
> * <span data-ttu-id="8ddf6-268">Wywoływana kwerenda zwracająca inne typy</span><span class="sxs-lookup"><span data-stu-id="8ddf6-268">Called a query to return other types</span></span>
> * <span data-ttu-id="8ddf6-269">Nazywane kwerendą Update</span><span class="sxs-lookup"><span data-stu-id="8ddf6-269">Called an update query</span></span>
> * <span data-ttu-id="8ddf6-270">Zbadaj zapytania SQL</span><span class="sxs-lookup"><span data-stu-id="8ddf6-270">Examined SQL queries</span></span>
> * <span data-ttu-id="8ddf6-271">Utworzono warstwę abstrakcji</span><span class="sxs-lookup"><span data-stu-id="8ddf6-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="8ddf6-272">Informacje o automatycznym wykrywaniu zmian</span><span class="sxs-lookup"><span data-stu-id="8ddf6-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="8ddf6-273">Informacje o EF Core kodzie źródłowym i planach programistycznych</span><span class="sxs-lookup"><span data-stu-id="8ddf6-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="8ddf6-274">Dowiesz się, jak używać dynamicznego LINQ do uproszczenia kodu</span><span class="sxs-lookup"><span data-stu-id="8ddf6-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="8ddf6-275">Ta seria samouczków została zakończona przy użyciu Entity Framework Core w aplikacji ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="8ddf6-276">Ta seria pracowała z nową bazą danych; Alternatywą jest odtwarzanie modelu z istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8ddf6-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8ddf6-277">Samouczek: EF Core z MVC, istniejąca baza danych</span><span class="sxs-lookup"><span data-stu-id="8ddf6-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
