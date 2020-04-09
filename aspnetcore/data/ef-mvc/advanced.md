---
title: 'Samouczek: Dowiedz się więcej o zaawansowanych scenariuszach - ASP.NET MVC z EF Core'
description: W tym samouczku przedstawiono przydatne tematy wykraczające poza podstawy tworzenia aplikacji sieci web ASP.NET Core, które używają programu Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: fc6f8d8c4ab09848cf316be2e522bf5ce3b9ac76
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416234"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="c5a6b-103">Samouczek: Dowiedz się więcej o zaawansowanych scenariuszach - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="c5a6b-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="c5a6b-104">W poprzednim samouczku zaimplementowano dziedziczenie tabeli na hierarchię.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="c5a6b-105">W tym samouczku przedstawiono kilka tematów, które są przydatne, aby być świadomym, gdy wykracza poza podstawy tworzenia ASP.NET podstawowych aplikacji sieci web, które używają Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="c5a6b-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5a6b-107">Wykonywanie nieprzetworzonych zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="c5a6b-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="c5a6b-108">Wywoływanie kwerendy w celu zwrócenia encji</span><span class="sxs-lookup"><span data-stu-id="c5a6b-108">Call a query to return entities</span></span>
> * <span data-ttu-id="c5a6b-109">Wywoływanie kwerendy w celu zwrócenia innych typów</span><span class="sxs-lookup"><span data-stu-id="c5a6b-109">Call a query to return other types</span></span>
> * <span data-ttu-id="c5a6b-110">Wywoływanie kwerendy aktualizującej</span><span class="sxs-lookup"><span data-stu-id="c5a6b-110">Call an update query</span></span>
> * <span data-ttu-id="c5a6b-111">Badanie zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="c5a6b-111">Examine SQL queries</span></span>
> * <span data-ttu-id="c5a6b-112">Tworzenie warstwy abstrakcji</span><span class="sxs-lookup"><span data-stu-id="c5a6b-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="c5a6b-113">Dowiedz się więcej o automatycznym wykrywaniu zmian</span><span class="sxs-lookup"><span data-stu-id="c5a6b-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="c5a6b-114">Dowiedz się więcej o kodzie źródłowym EF Core i planach rozwoju</span><span class="sxs-lookup"><span data-stu-id="c5a6b-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="c5a6b-115">Dowiedz się, jak używać dynamicznego linq w celu uproszczenia kodu</span><span class="sxs-lookup"><span data-stu-id="c5a6b-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c5a6b-116">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c5a6b-116">Prerequisites</span></span>

* [<span data-ttu-id="c5a6b-117">Implementowanie dziedziczenia</span><span class="sxs-lookup"><span data-stu-id="c5a6b-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="c5a6b-118">Wykonywanie nieprzetworzonych zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="c5a6b-118">Perform raw SQL queries</span></span>

<span data-ttu-id="c5a6b-119">Jedną z zalet korzystania z entity framework jest, że unika wiązania kodu zbyt ściśle do określonej metody przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="c5a6b-120">Czyni to poprzez generowanie zapytań SQL i poleceń dla Ciebie, co również uwalnia od konieczności pisania ich samodzielnie.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="c5a6b-121">Istnieją jednak wyjątkowe scenariusze, gdy trzeba uruchomić określone zapytania SQL, które zostały utworzone ręcznie.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="c5a6b-122">W tych scenariuszach interfejs API code framework framework zawiera metody, które umożliwiają przekazywanie poleceń SQL bezpośrednio do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="c5a6b-123">W EF Core 1.0 dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="c5a6b-124">Użyj `DbSet.FromSql` metody dla kwerend, które zwracają typy jednostek.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="c5a6b-125">Zwrócone obiekty muszą być typu `DbSet` oczekiwanego przez obiekt i są automatycznie śledzone przez kontekst bazy danych, chyba że [zostanie wyłączone śledzenie](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="c5a6b-126">Użyj `Database.ExecuteSqlCommand` dla poleceń innych niż kwerendy.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="c5a6b-127">Jeśli musisz uruchomić kwerendę, która zwraca typy, które nie są jednostkami, można użyć ADO.NET z połączeniem bazy danych dostarczonym przez EF.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="c5a6b-128">Zwrócone dane nie są śledzone przez kontekst bazy danych, nawet jeśli ta metoda jest używana do pobierania typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="c5a6b-129">Jak zawsze podczas wykonywania poleceń SQL w aplikacji sieci web, należy podjąć środki ostrożności, aby chronić witrynę przed atakami iniekcji SQL.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="c5a6b-130">Jednym ze sposobów, aby to zrobić, jest użycie sparametryzowanych zapytań, aby upewnić się, że ciągi przesyłane przez stronę sieci web nie mogą być interpretowane jako polecenia SQL.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="c5a6b-131">W tym samouczku użyjesz sparametryzowanych zapytań podczas integrowania danych wejściowych użytkownika do kwerendy.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="c5a6b-132">Wywoływanie kwerendy w celu zwrócenia encji</span><span class="sxs-lookup"><span data-stu-id="c5a6b-132">Call a query to return entities</span></span>

<span data-ttu-id="c5a6b-133">Klasa `DbSet<TEntity>` zawiera metodę, której można użyć do wykonania kwerendy `TEntity`zwracanej jednostki typu .</span><span class="sxs-lookup"><span data-stu-id="c5a6b-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="c5a6b-134">Aby zobaczyć, jak to działa, zmienisz kod w metodzie `Details` kontrolera działu.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="c5a6b-135">W *DepartmentsController.cs*, w `Details` metodzie, zastąp kod, `FromSql` który pobiera dział wywołaniem metody, jak pokazano w poniższym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="c5a6b-136">Aby sprawdzić, czy nowy kod działa poprawnie, wybierz kartę **Działy,** a następnie **pozycję Szczegóły** dla jednego z działów.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Szczegóły działu](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="c5a6b-138">Wywoływanie kwerendy w celu zwrócenia innych typów</span><span class="sxs-lookup"><span data-stu-id="c5a6b-138">Call a query to return other types</span></span>

<span data-ttu-id="c5a6b-139">Wcześniej utworzono siatkę statystyk uczniów dla strony Informacje, która pokazywała liczbę uczniów dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="c5a6b-140">Masz dane z zestawu jednostek`_context.Students`Studenci ( ) i używane LINQ `EnrollmentDateGroup` do projektu wyników na liście obiektów modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="c5a6b-141">Załóżmy, że chcesz napisać sam SQL, a nie przy użyciu LINQ.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="c5a6b-142">Aby to zrobić, należy uruchomić kwerendę SQL, która zwraca coś innego niż obiekty jednostki.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="c5a6b-143">W EF Core 1.0 jednym ze sposobów, aby to zrobić, jest napisać ADO.NET kodu i uzyskać połączenie z bazą danych z EF.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="c5a6b-144">W *HomeController.cs*, zastąp `About` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="c5a6b-145">Dodaj instrukcję using:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="c5a6b-146">Uruchom aplikację i przejdź do strony Informacje.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-146">Run the app and go to the About page.</span></span> <span data-ttu-id="c5a6b-147">Wyświetla te same dane, co wcześniej.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-147">It displays the same data it did before.</span></span>

![Strona Informacje](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="c5a6b-149">Wywoływanie kwerendy aktualizującej</span><span class="sxs-lookup"><span data-stu-id="c5a6b-149">Call an update query</span></span>

<span data-ttu-id="c5a6b-150">Załóżmy, że administratorzy contoso university chcą wykonywać globalne zmiany w bazie danych, takie jak zmiana liczby kredytów dla każdego kursu.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="c5a6b-151">Jeśli uniwersytet ma dużą liczbę kursów, byłoby nieefektywne, aby pobrać je wszystkie jako podmioty i zmienić je indywidualnie.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="c5a6b-152">W tej sekcji zaimplementujesz stronę sieci web, która umożliwia użytkownikowi określenie współczynnika, za pomocą którego można zmienić liczbę kredytów dla wszystkich kursów, a zmienisz ją, wykonując instrukcję SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="c5a6b-153">Strona internetowa będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-153">The web page will look like the following illustration:</span></span>

![Strona Aktualizowanie kredytów kursu](advanced/_static/update-credits.png)

<span data-ttu-id="c5a6b-155">W *CoursesController.cs*, dodaj Metody UpdateCourseCredits dla HttpGet i HttpPost:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="c5a6b-156">Gdy kontroler przetwarza żądanie HttpGet, nic `ViewData["RowsAffected"]`nie jest zwracany w , a widok wyświetla puste pole tekstowe i przycisk prześlij, jak pokazano na poprzedniej ilustracji.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="c5a6b-157">Po kliknięciu przycisku **Aktualizuj** wywoływana jest metoda HttpPost, a mnożnik zawiera wartość wpisana w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="c5a6b-158">Następnie kod wykonuje sql, który aktualizuje kursy i zwraca liczbę wierszy, których dotyczy problem, do widoku w `ViewData`programie .</span><span class="sxs-lookup"><span data-stu-id="c5a6b-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="c5a6b-159">Gdy widok pobiera `RowsAffected` wartość, wyświetla liczbę zaktualizowanych wierszy.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="c5a6b-160">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Widoki/Kursy,* a następnie kliknij polecenie **Dodaj > Nowy element**.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="c5a6b-161">W oknie dialogowym **Dodawanie nowego elementu** kliknij pozycję ASP.NET **Core** w obszarze **Zainstalowane** w lewym okienku, kliknij pozycję **Razor View**i nazwij nowy widok *UpdateCourseCredits.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="c5a6b-162">W *views/courses/updatecoursecredits.cshtml*, zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="c5a6b-163">Uruchom `UpdateCourseCredits` metodę, wybierając kartę **Kursy,** a następnie dodając "/UpdateCourseCredits" na końcu adresu URL na pasku adresu przeglądarki (na przykład: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="c5a6b-164">Wprowadź numer w polu tekstowym:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-164">Enter a number in the text box:</span></span>

![Strona Aktualizowanie kredytów kursu](advanced/_static/update-credits.png)

<span data-ttu-id="c5a6b-166">Kliknij przycisk **Update** (Aktualizuj).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-166">Click **Update**.</span></span> <span data-ttu-id="c5a6b-167">Widzisz liczbę wierszy, których to dotyczy:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-167">You see the number of rows affected:</span></span>

![Dotyczy wierszy strony Aktualizowanie kredytów kursu](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="c5a6b-169">Kliknij **przycisk Wstecz do listy,** aby wyświetlić listę kursów ze zmienioną liczbą kredytów.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="c5a6b-170">Należy zauważyć, że kod produkcyjny zapewni, że aktualizacje zawsze powodują prawidłowe dane.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="c5a6b-171">Podany tutaj uproszczony kod może pomnożyć liczbę kredytów na tyle, aby zwiększyć liczbę większą niż 5.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="c5a6b-172">(Właściwość `Credits` ma `[Range(0, 5)]` atrybut.) Kwerenda aktualizująca będzie działać, ale nieprawidłowe dane mogą powodować nieoczekiwane wyniki w innych częściach systemu, które zakładają, że liczba kredytów wynosi 5 lub mniej.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="c5a6b-173">Aby uzyskać więcej informacji na temat nieprzetworzonych zapytań SQL, zobacz [Nieprzetworzone kwerendy SQL](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="c5a6b-174">Badanie zapytań SQL</span><span class="sxs-lookup"><span data-stu-id="c5a6b-174">Examine SQL queries</span></span>

<span data-ttu-id="c5a6b-175">Czasami jest to przydatne, aby móc zobaczyć rzeczywiste zapytania SQL, które są wysyłane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="c5a6b-176">Wbudowana funkcja rejestrowania dla ASP.NET Core jest automatycznie używana przez EF Core do pisania dzienników zawierających sql dla zapytań i aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="c5a6b-177">W tej sekcji zobaczysz kilka przykładów rejestrowania SQL.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="c5a6b-178">Otwórz *StudentsController.cs* i w `Details` metodzie ustawić punkt `if (student == null)` przerwania na instrukcji.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="c5a6b-179">Uruchom aplikację w trybie debugowania i przejdź do strony Szczegóły dla ucznia.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="c5a6b-180">Przejdź do okna **Dane wyjściowe** z wyświetlonymi wyjściami debugowania, a zostanie wyświetloną kwerenda:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

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

<span data-ttu-id="c5a6b-181">Zauważysz tutaj coś, co może Cię zaskoczyć: SQL wybiera`TOP(2)`maksymalnie 2 wiersze ( ) z tabeli Osoba.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="c5a6b-182">Metoda `SingleOrDefaultAsync` nie rozpoznać do 1 wiersza na serwerze.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="c5a6b-183">Oto dlaczego:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-183">Here's why:</span></span>

* <span data-ttu-id="c5a6b-184">Jeśli kwerenda zwróci wiele wierszy, metoda zwraca wartość null.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="c5a6b-185">Aby ustalić, czy kwerenda zwróci wiele wierszy, EF musi sprawdzić, czy zwraca co najmniej 2.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="c5a6b-186">Należy zauważyć, że nie trzeba używać trybu debugowania i zatrzymać w punkcie przerwania, aby uzyskać dane wyjściowe rejestrowania w oknie **Dane wyjściowe.**</span><span class="sxs-lookup"><span data-stu-id="c5a6b-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="c5a6b-187">Jest to po prostu wygodny sposób, aby zatrzymać rejestrowanie w punkcie, który chcesz spojrzeć na dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="c5a6b-188">Jeśli tego nie zrobisz, rejestrowanie jest kontynuowane i musisz przewinąć do tyłu, aby znaleźć interesujące Cię części.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="c5a6b-189">Tworzenie warstwy abstrakcji</span><span class="sxs-lookup"><span data-stu-id="c5a6b-189">Create an abstraction layer</span></span>

<span data-ttu-id="c5a6b-190">Wielu deweloperów napisać kod do zaimplementowania repozytorium i jednostki wzorców pracy jako otoki wokół kodu, który współpracuje z Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="c5a6b-191">Te wzorce są przeznaczone do tworzenia warstwy abstrakcji między warstwą dostępu do danych a warstwą logiki biznesowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="c5a6b-192">Implementowanie tych wzorców może pomóc izolować aplikację od zmian w magazynie danych i może ułatwić automatyczne testowanie jednostek lub programowanie oparte na testach (TDD).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="c5a6b-193">Jednak pisanie dodatkowego kodu w celu zaimplementowania tych wzorców nie zawsze jest najlepszym wyborem dla aplikacji korzystających z EF, z kilku powodów:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="c5a6b-194">Klasa kontekstu EF sama izoluje kod z kodu specyficznego dla magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="c5a6b-195">Klasa kontekstu EF może działać jako klasa jednostki pracy dla aktualizacji bazy danych, które można wykonać przy użyciu EF.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="c5a6b-196">EF zawiera funkcje implementacji TDD bez pisania kodu repozytorium.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="c5a6b-197">Aby uzyskać informacje na temat implementacji repozytorium i jednostki wzorców pracy, zobacz [Entity Framework 5 wersja tej serii samouczków](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="c5a6b-198">Entity Framework Core implementuje dostawcę bazy danych w pamięci, który może służyć do testowania.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="c5a6b-199">Aby uzyskać więcej informacji, zobacz [Testowanie za pomocą usługi InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="c5a6b-200">Automatyczne wykrywanie zmian</span><span class="sxs-lookup"><span data-stu-id="c5a6b-200">Automatic change detection</span></span>

<span data-ttu-id="c5a6b-201">Entity Framework określa, jak jednostka została zmieniona (i w związku z tym, które aktualizacje muszą być wysyłane do bazy danych) przez porównanie bieżących wartości jednostki z oryginalnymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="c5a6b-202">Oryginalne wartości są przechowywane, gdy jednostka jest wyszukiwana lub dołączona.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="c5a6b-203">Niektóre metody, które powodują automatyczne wykrywanie zmian są następujące:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="c5a6b-204">DbContext.SaveZmienia się</span><span class="sxs-lookup"><span data-stu-id="c5a6b-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="c5a6b-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="c5a6b-205">DbContext.Entry</span></span>

* <span data-ttu-id="c5a6b-206">Pozycje ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="c5a6b-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="c5a6b-207">Jeśli śledzisz dużą liczbę jednostek i dzwonisz do jednej z tych metod wiele razy w pętli, możesz uzyskać znaczną poprawę wydajności, tymczasowo wyłączając automatyczne wykrywanie zmian przy użyciu `ChangeTracker.AutoDetectChangesEnabled` właściwości.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="c5a6b-208">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="c5a6b-209">Ef Podstawowy kod źródłowy i plany rozwoju</span><span class="sxs-lookup"><span data-stu-id="c5a6b-209">EF Core source code and development plans</span></span>

<span data-ttu-id="c5a6b-210">Źródło core programu Entity [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)Framework znajduje się na poziomie .</span><span class="sxs-lookup"><span data-stu-id="c5a6b-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="c5a6b-211">Repozytorium EF Core zawiera kompilacje nocne, śledzenie problemów, specyfikacje funkcji, notatki ze spotkania projektowego i [plan rozwoju w przyszłości.](https://github.com/dotnet/efcore/wiki/Roadmap)</span><span class="sxs-lookup"><span data-stu-id="c5a6b-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="c5a6b-212">Możesz zgłosić lub znaleźć błędy i przyczynić się.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="c5a6b-213">Mimo że kod źródłowy jest otwarty, Entity Framework Core jest w pełni obsługiwany jako produkt firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="c5a6b-214">Zespół microsoft entity framework zachowuje kontrolę nad tym, które wkłady są akceptowane i testuje wszystkie zmiany kodu, aby zapewnić jakość każdej wersji.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="c5a6b-215">Inżynier odwrotny z istniejącej bazy danych</span><span class="sxs-lookup"><span data-stu-id="c5a6b-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="c5a6b-216">Aby odtworzyć model danych, w tym klasy jednostek z istniejącej bazy danych, należy użyć polecenia [szkieletu-dbcontext.](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)</span><span class="sxs-lookup"><span data-stu-id="c5a6b-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="c5a6b-217">Zobacz [samouczek wprowadzenie](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="c5a6b-218">Użyj dynamicznego LINQ, aby uprościć kod</span><span class="sxs-lookup"><span data-stu-id="c5a6b-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="c5a6b-219">[Trzeci samouczek z tej serii](sort-filter-page.md) pokazuje, jak napisać kod LINQ przez hard-coding nazwy kolumn w instrukcji. `switch`</span><span class="sxs-lookup"><span data-stu-id="c5a6b-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="c5a6b-220">Z dwóch kolumn do wyboru, to działa dobrze, ale jeśli masz wiele kolumn kod może uzyskać pełne.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="c5a6b-221">Aby rozwiązać ten problem, `EF.Property` można użyć metody, aby określić nazwę właściwości jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="c5a6b-222">Aby wypróbować to podejście, `Index` zastąp metodę w poniższym `StudentsController` kodzie.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="c5a6b-223">Podziękowania</span><span class="sxs-lookup"><span data-stu-id="c5a6b-223">Acknowledgments</span></span>

<span data-ttu-id="c5a6b-224">Tom Dykstra i Rick @RickAndMSFTAnderson (twitter) napisał ten poradnik.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="c5a6b-225">Rowan Miller, Diego Vega i inni członkowie zespołu Entity Framework pomagali w przeglądaniu kodu i pomagali w debugowaniu problemów, które pojawiły się podczas pisania kodu dla samouczków.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="c5a6b-226">John Parente i Paul Goldman pracowali nad aktualizacją samouczka dla ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="c5a6b-227">Rozwiązywanie typowych problemów</span><span class="sxs-lookup"><span data-stu-id="c5a6b-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="c5a6b-228">ContosoUniversity.dll używany przez inny proces</span><span class="sxs-lookup"><span data-stu-id="c5a6b-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="c5a6b-229">Komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-229">Error message:</span></span>

> <span data-ttu-id="c5a6b-230">Nie można otworzyć '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' do zapisu -- "Proces nie może uzyskać dostępu do pliku '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll', ponieważ jest on używany przez inny proces.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="c5a6b-231">Rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-231">Solution:</span></span>

<span data-ttu-id="c5a6b-232">Zatrzymaj witrynę w układzie IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="c5a6b-233">Przejdź do zasobnika systemowego systemu Windows, znajdź program IIS Express i kliknij prawym przyciskiem myszy jego ikonę, wybierz witrynę Uniwersytetu Contoso, a następnie kliknij pozycję **Zatrzymaj witrynę**.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="c5a6b-234">Szkielety migracji bez kodu w metodach w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="c5a6b-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="c5a6b-235">Możliwa przyczyna:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-235">Possible cause:</span></span>

<span data-ttu-id="c5a6b-236">Polecenia EF CLI nie zamykają automatycznie i nie zapisują plików kodu.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="c5a6b-237">Jeśli po uruchomieniu `migrations add` polecenia wprowadzono niezapisane zmiany, ef nie znajdzie zmian.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="c5a6b-238">Rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-238">Solution:</span></span>

<span data-ttu-id="c5a6b-239">Uruchom `migrations remove` polecenie, zapisz zmiany kodu i `migrations add` uruchom ponownie polecenie.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="c5a6b-240">Błędy podczas uruchamiania aktualizacji bazy danych</span><span class="sxs-lookup"><span data-stu-id="c5a6b-240">Errors while running database update</span></span>

<span data-ttu-id="c5a6b-241">Istnieje możliwość uzyskania innych błędów podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="c5a6b-242">Jeśli widzisz błędy migracji, których nie można rozwiązać, możesz zmienić nazwę bazy danych w ciągu połączenia lub usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="c5a6b-243">W nowej bazie danych nie ma żadnych danych do migracji, a polecenie update-database jest znacznie bardziej prawdopodobne, aby zakończyć bez błędów.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="c5a6b-244">Najprostszym podejściem jest zmiana nazwy bazy danych w *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="c5a6b-245">Przy następnym uruchomieniu `database update`zostanie utworzona nowa baza danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="c5a6b-246">Aby usunąć bazę danych w SSOX, kliknij prawym przyciskiem myszy bazę danych, kliknij polecenie **Usuń**, a następnie w oknie dialogowym **Usuwanie bazy danych** wybierz pozycję Zamknij istniejące **połączenia** i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="c5a6b-247">Aby usunąć bazę danych przy `database drop` użyciu interfejsu wiersza polecenia, uruchom polecenie CLI:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="c5a6b-248">Błąd podczas lokalizowania wystąpienia programu SQL Server</span><span class="sxs-lookup"><span data-stu-id="c5a6b-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="c5a6b-249">Komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-249">Error Message:</span></span>

> <span data-ttu-id="c5a6b-250">Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="c5a6b-251">Serwer nie został znaleziony lub był niedostępny.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="c5a6b-252">Sprawdź, czy nazwa wystąpienia jest poprawna i czy program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="c5a6b-253">(dostawca: interfejsy sieciowe SQL, błąd: 26 - Określono błąd lokalizowania serwera/wystąpienia)</span><span class="sxs-lookup"><span data-stu-id="c5a6b-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="c5a6b-254">Rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-254">Solution:</span></span>

<span data-ttu-id="c5a6b-255">Sprawdź parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-255">Check the connection string.</span></span> <span data-ttu-id="c5a6b-256">Jeśli plik bazy danych został ręcznie usunięty, zmień nazwę bazy danych w ciągu budowy, aby rozpocząć od nowa z nową bazą danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="c5a6b-257">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="c5a6b-257">Get the code</span></span>

[<span data-ttu-id="c5a6b-258">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="c5a6b-259">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c5a6b-259">Additional resources</span></span>

<span data-ttu-id="c5a6b-260">Aby uzyskać więcej informacji na temat EF Core, zobacz [dokumentację Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="c5a6b-261">Dostępna jest również książka: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="c5a6b-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="c5a6b-262">Aby uzyskać informacje na temat wdrażania <xref:host-and-deploy/index>aplikacji sieci web, zobacz .</span><span class="sxs-lookup"><span data-stu-id="c5a6b-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c5a6b-263">Aby uzyskać informacje na temat innych tematów związanych z ASP.NET <xref:index>Core MVC, takich jak uwierzytelnianie i autoryzacja, zobacz .</span><span class="sxs-lookup"><span data-stu-id="c5a6b-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c5a6b-264">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="c5a6b-264">Next steps</span></span>

<span data-ttu-id="c5a6b-265">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="c5a6b-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5a6b-266">Wykonywane nieprzetworzone kwerendy SQL</span><span class="sxs-lookup"><span data-stu-id="c5a6b-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="c5a6b-267">Wywoływane zapytanie do zwracania encji</span><span class="sxs-lookup"><span data-stu-id="c5a6b-267">Called a query to return entities</span></span>
> * <span data-ttu-id="c5a6b-268">Wywołana kwerenda w celu zwrócenia innych typów</span><span class="sxs-lookup"><span data-stu-id="c5a6b-268">Called a query to return other types</span></span>
> * <span data-ttu-id="c5a6b-269">Wywoływana kwerenda aktualizująca</span><span class="sxs-lookup"><span data-stu-id="c5a6b-269">Called an update query</span></span>
> * <span data-ttu-id="c5a6b-270">Zbadane kwerendy SQL</span><span class="sxs-lookup"><span data-stu-id="c5a6b-270">Examined SQL queries</span></span>
> * <span data-ttu-id="c5a6b-271">Utworzono warstwę abstrakcyjną</span><span class="sxs-lookup"><span data-stu-id="c5a6b-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="c5a6b-272">Dowiedz się więcej o automatycznym wykrywaniu zmian</span><span class="sxs-lookup"><span data-stu-id="c5a6b-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="c5a6b-273">Dowiedz się więcej o kodzie źródłowym EF Core i planach rozwoju</span><span class="sxs-lookup"><span data-stu-id="c5a6b-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="c5a6b-274">Dowiedz się, jak używać dynamicznego LINQ w celu uproszczenia kodu</span><span class="sxs-lookup"><span data-stu-id="c5a6b-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="c5a6b-275">Spowoduje to ukończenie tej serii samouczków dotyczących korzystania z rdzenia entity framework w aplikacji ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="c5a6b-276">Ta seria pracowała z nową bazą danych; alternatywą jest odtwolinie modelu z istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c5a6b-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c5a6b-277">Samouczek: EF Core z MVC, istniejąca baza danych</span><span class="sxs-lookup"><span data-stu-id="c5a6b-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
