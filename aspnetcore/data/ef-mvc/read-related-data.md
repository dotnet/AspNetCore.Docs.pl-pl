---
title: 'Samouczek: odczytywanie powiązanych danych — ASP.NET MVC z EF Core'
description: W tym samouczku zostaną odczytane i wyświetlone powiązane dane, czyli dane, które Entity Framework ładowane do właściwości nawigacji.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: abd5c4e474e30c119e2bea9e3cce3d2b277e0daf
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212752"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="b3a7c-103">Samouczek: odczytywanie powiązanych danych — ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="b3a7c-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="b3a7c-104">W poprzednim samouczku został ukończony model danych szkoły.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="b3a7c-105">W tym samouczku zostaną odczytane i wyświetlone powiązane dane, czyli dane, które Entity Framework ładowane do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="b3a7c-106">Na poniższych ilustracjach przedstawiono strony, z którymi będziesz korzystać.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-106">The following illustrations show the pages that you'll work with.</span></span>

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

<span data-ttu-id="b3a7c-109">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b3a7c-110">Dowiedz się, jak ładować powiązane dane</span><span class="sxs-lookup"><span data-stu-id="b3a7c-110">Learn how to load related data</span></span>
> * <span data-ttu-id="b3a7c-111">Utwórz stronę kursów</span><span class="sxs-lookup"><span data-stu-id="b3a7c-111">Create a Courses page</span></span>
> * <span data-ttu-id="b3a7c-112">Tworzenie strony instruktorów</span><span class="sxs-lookup"><span data-stu-id="b3a7c-112">Create an Instructors page</span></span>
> * <span data-ttu-id="b3a7c-113">Informacje o jawnym ładowaniu</span><span class="sxs-lookup"><span data-stu-id="b3a7c-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b3a7c-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b3a7c-114">Prerequisites</span></span>

* [<span data-ttu-id="b3a7c-115">Tworzenie złożonego modelu danych</span><span class="sxs-lookup"><span data-stu-id="b3a7c-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="b3a7c-116">Dowiedz się, jak ładować powiązane dane</span><span class="sxs-lookup"><span data-stu-id="b3a7c-116">Learn how to load related data</span></span>

<span data-ttu-id="b3a7c-117">Istnieje kilka sposobów, które oprogramowanie mapowanie relacyjne (ORM), takie jak Entity Framework, może ładować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b3a7c-118">Ładowanie eager.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-118">Eager loading.</span></span> <span data-ttu-id="b3a7c-119">Po odczytaniu jednostki są pobierane powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="b3a7c-120">Zwykle powoduje to pojedyncze zapytanie sprzężenia, które pobiera wszystkie dane, które są zbędne.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b3a7c-121">Należy określić eager ładowania w Entity Framework Core przy użyciu `Include` metod i `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Przykład ładowania eager](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="b3a7c-123">Niektóre dane można pobrać w oddzielnych zapytaniach, a EF "naprawia" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="b3a7c-124">Oznacza to, że EF automatycznie dodaje oddzielnie pobrane jednostki, w których należą do właściwości nawigacji wcześniej pobranych jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="b3a7c-125">W przypadku zapytania pobierającego powiązane dane można użyć `Load` metody zamiast metody, która zwraca listę lub obiekt, na przykład `ToList` lub `Single` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="b3a7c-127">Jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-127">Explicit loading.</span></span> <span data-ttu-id="b3a7c-128">Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b3a7c-129">Napiszesz kod, który pobiera powiązane dane, jeśli jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="b3a7c-130">Tak jak w przypadku eager ładowania z oddzielnymi zapytaniami, jawne ładowanie powoduje wysłanie wielu zapytań do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="b3a7c-131">Różnica polega na tym, że z jawnym ładowaniem kod określa właściwości nawigacji do załadowania.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b3a7c-132">W Entity Framework Core 1,1 można użyć `Load` metody do wykonania jawnego ładowania.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b3a7c-133">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-133">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b3a7c-135">Ładowanie z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-135">Lazy loading.</span></span> <span data-ttu-id="b3a7c-136">Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b3a7c-137">Jednak przy pierwszej próbie uzyskania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b3a7c-138">Zapytanie jest wysyłane do bazy danych przy każdej próbie pobrania danych z właściwości nawigacji po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="b3a7c-139">Entity Framework Core 1,0 nie obsługuje ładowania z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="b3a7c-140">Zagadnienia dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="b3a7c-140">Performance considerations</span></span>

<span data-ttu-id="b3a7c-141">Jeśli wiesz, że potrzebujesz pokrewnych danych dla każdej pobranej jednostki, ładowanie eager często oferuje najlepszą wydajność, ponieważ pojedyncze zapytanie wysyłane do bazy danych jest zwykle bardziej wydajne niż osobne zapytania dla każdej pobranej jednostki.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="b3a7c-142">Załóżmy na przykład, że każdy dział ma dziesięć powiązanych kursów.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="b3a7c-143">Eager ładowanie wszystkich powiązanych danych spowoduje tylko pojedyncze zapytanie (join) i pojedynczą rundę do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="b3a7c-144">Oddzielne zapytanie dotyczące kursów dla każdego działu spowoduje jedenaście rund do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="b3a7c-145">Dodatkowe podróże do bazy danych są szczególnie niekorzystne w przypadku opóźnień.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="b3a7c-146">Z drugiej strony w niektórych scenariuszach oddzielne zapytania są bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="b3a7c-147">Eager ładowanie wszystkich powiązanych danych w jednym zapytaniu może spowodować wygenerowanie bardzo złożonej sprzężenia, co SQL Server nie może przetworzyć efektywnie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="b3a7c-148">Lub jeśli chcesz uzyskać dostęp do właściwości nawigacji jednostki tylko dla podzbioru zestawu obiektów, które są przetwarzane, osobne zapytania mogą działać lepiej, ponieważ eager ładowanie wszystkiego z góry spowodowałoby pobranie większej ilości danych niż jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="b3a7c-149">Jeśli wydajność ma kluczowe znaczenie, najlepszym rozwiązaniem jest przetestowanie wydajności obu metod w celu zapewnienia najlepszego wyboru.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="b3a7c-150">Utwórz stronę kursów</span><span class="sxs-lookup"><span data-stu-id="b3a7c-150">Create a Courses page</span></span>

<span data-ttu-id="b3a7c-151">Jednostka kursu zawiera właściwość nawigacji, która zawiera jednostkę działu działu, do której jest przypisany kurs.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="b3a7c-152">Aby wyświetlić nazwę przypisanego działu na liście kursów, należy uzyskać Właściwość Name z jednostki działu, która znajduje się we `Course.Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="b3a7c-153">Utwórz kontroler o nazwie CoursesController dla typu jednostki kursu, używając tych samych opcji dla **kontrolera MVC z widokami, używając Entity Framework** szkieletu, który był wcześniej przeznaczony dla kontrolera uczniów, jak pokazano na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Dodaj kontroler kursów](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="b3a7c-155">Otwórz *CoursesController.cs* i Przeanalizuj `Index` metodę.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="b3a7c-156">Funkcja automatycznego tworzenia szkieletów określiła eager ładowania dla `Department` właściwości nawigacji przy użyciu `Include` metody.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="b3a7c-157">Zastąp `Index` metodę poniższym kodem, który używa bardziej odpowiedniej nazwy dla `IQueryable` zwracanych jednostek kursu ( `courses` zamiast `schoolContext` ):</span><span class="sxs-lookup"><span data-stu-id="b3a7c-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="b3a7c-158">Otwórz *Widok widoki/kursy/index. cshtml* i Zastąp kod szablonu poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="b3a7c-159">Zmiany są wyróżnione:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="b3a7c-160">Do kodu szkieletowego wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="b3a7c-161">Zmieniono nagłówek z indeksu na kursy.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="b3a7c-162">Dodano kolumnę **liczbową** , która wyświetla `CourseID` wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b3a7c-163">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle nie są oznaczane przez użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b3a7c-164">Jednak w tym przypadku klucz podstawowy ma znaczenie i chcesz go wyświetlić.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="b3a7c-165">Zmieniono kolumnę **działu** , aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b3a7c-166">Kod wyświetla `Name` Właściwość jednostki działu, która jest ładowana do `Department` właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b3a7c-167">Uruchom aplikację i wybierz kartę **kursy** , aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="b3a7c-169">Tworzenie strony instruktorów</span><span class="sxs-lookup"><span data-stu-id="b3a7c-169">Create an Instructors page</span></span>

<span data-ttu-id="b3a7c-170">W tej sekcji utworzysz kontroler i widok dla jednostki instruktora, aby wyświetlić stronę instruktorów:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

<span data-ttu-id="b3a7c-172">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b3a7c-173">Lista instruktorów wyświetla powiązane dane z jednostki OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="b3a7c-174">Jednostki instruktora i OfficeAssignment są w relacji jeden-do-zero-lub-jednego.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b3a7c-175">Będziesz używać eager ładowania dla jednostek OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="b3a7c-176">Jak wyjaśniono wcześniej, ładowanie eager jest zwykle bardziej wydajne, gdy potrzebne są powiązane dane dla wszystkich pobranych wierszy tabeli podstawowej.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="b3a7c-177">W takim przypadku chcesz wyświetlić przypisania pakietu Office dla wszystkich wyświetlanych instruktorów.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="b3a7c-178">Gdy użytkownik wybierze instruktora, wyświetlane są powiązane jednostki kursu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="b3a7c-179">Jednostki instruktora i kursy znajdują się w relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b3a7c-180">Będziesz używać eager ładowania dla jednostek kursu i powiązanych z nimi jednostek działu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="b3a7c-181">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są kursy tylko dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="b3a7c-182">Jednak w tym przykładzie pokazano, jak używać eager ładowania dla właściwości nawigacji w obrębie jednostek, które są same we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="b3a7c-183">Gdy użytkownik wybierze kurs, zostanie wyświetlona wartość powiązane dane z zestawu jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="b3a7c-184">Jednostki kursu i rejestracji znajdują się w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="b3a7c-185">W przypadku jednostek rejestracji i powiązanych z nimi jednostek uczniów będziesz używać oddzielnych zapytań.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="b3a7c-186">Utwórz model widoku dla widoku indeksu instruktora</span><span class="sxs-lookup"><span data-stu-id="b3a7c-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="b3a7c-187">Na stronie instruktorzy są wyświetlane dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="b3a7c-188">W związku z tym utworzysz model widoku zawierający trzy właściwości, z których każda będzie zawierać dane dla jednej z tabel.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="b3a7c-189">W folderze *SchoolViewModels* Utwórz *InstructorIndexData.cs* i Zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="b3a7c-190">Tworzenie kontrolera i widoków instruktora</span><span class="sxs-lookup"><span data-stu-id="b3a7c-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="b3a7c-191">Utwórz kontroler instruktorów z akcjami odczyt/zapis EF, jak pokazano na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Dodaj kontroler instruktorów](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="b3a7c-193">Otwórz *InstructorsController.cs* i Dodaj instrukcję using dla przestrzeni nazw modele widoków:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="b3a7c-194">Zamień metodę index na następujący kod, aby wykonać eager ładowanie powiązanych danych i umieścić je w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="b3a7c-195">Metoda akceptuje opcjonalne dane trasy ( `id` ) i parametr ciągu zapytania ( `courseID` ), który udostępnia wartości identyfikatora wybranego instruktora i wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="b3a7c-196">Parametry są udostępniane przez **Wybieranie** hiperlinków na stronie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="b3a7c-197">Kod rozpoczyna się od utworzenia wystąpienia modelu widoku i umieszczenie go w liście instruktorów.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="b3a7c-198">Kod określa eager ładowania dla `Instructor.OfficeAssignment` i `Instructor.CourseAssignments` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="b3a7c-199">We `CourseAssignments` Właściwości Właściwość `Course` jest ładowana, a w tym, `Enrollments` `Department` właściwości i są załadowane, a w każdej `Enrollment` jednostce `Student` jest załadowana właściwość.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b3a7c-200">Ponieważ widok zawsze wymaga jednostki OfficeAssignment, to bardziej wydajne, aby pobrać te elementy w tym samym zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="b3a7c-201">Jednostki kursu są wymagane w przypadku wybrania na stronie sieci Web instruktora, dlatego pojedyncze zapytanie jest lepiej niż wiele zapytań tylko wtedy, gdy strona jest wyświetlana częściej jako kurs wybrany niż bez.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="b3a7c-202">Kod powtarza się `CourseAssignments` i `Course` ponieważ potrzebne są dwie właściwości z `Course` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="b3a7c-203">Pierwszy ciąg `ThenInclude` wywołań pobiera `CourseAssignment.Course` , `Course.Enrollments` , i `Enrollment.Student` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="b3a7c-204">W tym momencie w kodzie inne `ThenInclude` byłyby dla właściwości nawigacji `Student` , które nie są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="b3a7c-205">Ale wywoływanie `Include` zaczyna się od `Instructor` właściwości, więc należy ponownie przejść przez łańcuch, tym razem określając `Course.Department` zamiast `Course.Enrollments` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="b3a7c-206">Poniższy kod jest wykonywany po wybraniu instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="b3a7c-207">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b3a7c-208">Właściwość widok modelu `Courses` jest następnie ładowana z jednostkami kursu z tej właściwości nawigacji tego instruktora `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="b3a7c-209">`Where`Metoda zwraca kolekcję, ale w tym przypadku kryteria przekazane do tej metody powodują zwrócenie tylko pojedynczej jednostki instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="b3a7c-210">`Single`Metoda konwertuje kolekcję na pojedynczą jednostkę instruktora, która zapewnia dostęp do właściwości tej jednostki `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="b3a7c-211">`CourseAssignments`Właściwość zawiera `CourseAssignment` jednostki, z których mają być tylko powiązane `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="b3a7c-212">Możesz użyć `Single` metody w kolekcji, gdy wiesz, że kolekcja będzie zawierać tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="b3a7c-213">Pojedyncza Metoda zgłasza wyjątek, jeśli kolekcja została przeniesiona do niej pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="b3a7c-214">Alternatywą jest `SingleOrDefault` , która zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="b3a7c-215">Jednak w takim przypadku, w wyniku wyjątku (od próby znalezienia `Courses` właściwości w odwołaniu o wartości null), komunikat o wyjątku będzie mniej jasno wskazywać przyczynę problemu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="b3a7c-216">Po wywołaniu `Single` metody można również przekazać warunek WHERE zamiast wywołania `Where` metody osobno:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="b3a7c-217">Zamiast:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="b3a7c-218">Następnie, jeśli wybrano kurs, wybrany kurs zostanie pobrany z listy kursów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="b3a7c-219">Następnie właściwość modelu widoku `Enrollments` jest ładowana z jednostkami rejestracji z `Enrollments` właściwości nawigacji tego kursu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="b3a7c-220">Modyfikowanie widoku indeksu instruktora</span><span class="sxs-lookup"><span data-stu-id="b3a7c-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="b3a7c-221">W obszarze *widoki/instruktorzy/index. cshtml*Zastąp kod szablonu poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="b3a7c-222">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-222">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="b3a7c-223">Wprowadzono następujące zmiany w istniejącym kodzie:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="b3a7c-224">Zmieniono klasę modelu na `InstructorIndexData` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="b3a7c-225">Zmieniono tytuł strony z **indeksu** na **Instruktorzy**.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="b3a7c-226">Dodano kolumnę **pakietu Office** , która `item.OfficeAssignment.Location` jest wyświetlana tylko wtedy, gdy `item.OfficeAssignment` nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b3a7c-227">(Ponieważ jest to relacja "jeden do zera" lub jeden-do-jednego, nie może być powiązana jednostka OfficeAssignment).</span><span class="sxs-lookup"><span data-stu-id="b3a7c-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b3a7c-228">Dodano kolumnę **kursów** , która wyświetla nauczanie kursów przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b3a7c-229">Aby uzyskać więcej informacji, zobacz sekcję [jawne przejście liniowe](xref:mvc/views/razor#explicit-line-transition) w Razor artykule Składnia artykułu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="b3a7c-230">Dodano kod, który dynamicznie dodaje `class="success"` do `tr` elementu wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="b3a7c-231">Ustawia kolor tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b3a7c-232">Dodano nowe hiperłącze z etykietą **SELECT** zaraz przed innymi łączami w każdym wierszu, co powoduje wysłanie wybranego identyfikatora instruktora do `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="b3a7c-233">Uruchom aplikację i wybierz kartę **Instruktorzy** . Na stronie jest wyświetlana Właściwość Location powiązanych jednostek OfficeAssignment i pustej komórki tabeli, gdy nie istnieje powiązana jednostka OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Nie wybrano niczego ze strony indeksu instruktorów](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="b3a7c-235">W pliku *viewss/instruktors/index. cshtml* po elemencie zamykającej tabeli (na końcu pliku) Dodaj następujący kod.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="b3a7c-236">Ten kod wyświetla listę kursów związanych z instruktorem w przypadku wybrania instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="b3a7c-237">Ten kod odczytuje `Courses` Właściwość modelu widoku w celu wyświetlenia listy kursów.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="b3a7c-238">Zawiera również hiperłącze **SELECT** , które wysyła identyfikator wybranego kursu do `Index` metody akcji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="b3a7c-239">Odśwież stronę i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="b3a7c-240">Teraz zobaczysz siatkę wyświetlającą kursy przypisane do wybranego instruktora, a dla każdego kursu zobaczysz nazwę przypisanego działu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Wybrany instruktor strony indeksu instruktora](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="b3a7c-242">Po dodaniu bloku kodu Dodaj następujący kod.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="b3a7c-243">Spowoduje to wyświetlenie listy studentów, którzy są rejestrowani w kursie po wybraniu tego kursu.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="b3a7c-244">Ten kod odczytuje Właściwość Enrollments modelu widoku w celu wyświetlenia listy uczniów zarejestrowanych w kursie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="b3a7c-245">Odśwież stronę ponownie i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="b3a7c-246">Następnie wybierz kurs, aby zobaczyć listę zarejestrowanych studentów i ich klasy.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Wybrany instruktor i kurs dla instruktorów](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="b3a7c-248">Informacje o jawnym załadowaniu</span><span class="sxs-lookup"><span data-stu-id="b3a7c-248">About explicit loading</span></span>

<span data-ttu-id="b3a7c-249">Po pobraniu listy instruktorów w *InstructorsController.cs*określono eager ładowania dla `CourseAssignments` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="b3a7c-250">Załóżmy, że oczekujesz, że użytkownicy rzadko chcą widzieć rejestracje w wybranym instruktorze i kursie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="b3a7c-251">W takim przypadku możesz chcieć załadować dane rejestracji tylko wtedy, gdy jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="b3a7c-252">Aby zapoznać się z przykładem, jak przeprowadzić jawne ładowanie, Zastąp `Index` metodę następującym kodem, który usuwa eager ładowania na potrzeby rejestracji i ładuje tę właściwość jawnie.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="b3a7c-253">Zmiany kodu są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="b3a7c-254">Nowy kod odrzuca metodę *ThenInclude* wywołań danych rejestracji z kodu, który pobiera jednostki instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="b3a7c-255">Spowoduje to również porzucanie `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="b3a7c-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="b3a7c-256">Jeśli wybrano instruktora i kurs, wyróżniony kod pobiera jednostki rejestracji dla wybranego kursu i jednostek uczniów dla każdej rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="b3a7c-257">Uruchom aplikację, przejdź do strony indeks instruktorów, aby zobaczyć, co jest wyświetlane na stronie, chociaż zmieniono sposób pobierania danych.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="b3a7c-258">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="b3a7c-258">Get the code</span></span>

[<span data-ttu-id="b3a7c-259">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="b3a7c-260">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="b3a7c-260">Next steps</span></span>

<span data-ttu-id="b3a7c-261">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b3a7c-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b3a7c-262">Dowiesz się, jak ładować powiązane dane</span><span class="sxs-lookup"><span data-stu-id="b3a7c-262">Learned how to load related data</span></span>
> * <span data-ttu-id="b3a7c-263">Utworzono stronę kursów</span><span class="sxs-lookup"><span data-stu-id="b3a7c-263">Created a Courses page</span></span>
> * <span data-ttu-id="b3a7c-264">Utworzono stronę instruktorów</span><span class="sxs-lookup"><span data-stu-id="b3a7c-264">Created an Instructors page</span></span>
> * <span data-ttu-id="b3a7c-265">Informacje o jawnym ładowaniu</span><span class="sxs-lookup"><span data-stu-id="b3a7c-265">Learned about explicit loading</span></span>

<span data-ttu-id="b3a7c-266">Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="b3a7c-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b3a7c-267">Aktualizowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="b3a7c-267">Update related data</span></span>](update-related-data.md)
