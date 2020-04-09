---
title: 'Poradnik: Odczyt powiązanych danych - ASP.NET MVC z EF Core'
description: W tym samouczku będziesz czytać i wyświetlać powiązane dane — czyli dane, które entity framework ładuje do właściwości nawigacji.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: a6e63723101ab09219db81ee9796c3938a612226
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657109"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="a3267-103">Poradnik: Odczyt powiązanych danych - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="a3267-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="a3267-104">W poprzednim samouczku ukończono model danych szkoły.</span><span class="sxs-lookup"><span data-stu-id="a3267-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="a3267-105">W tym samouczku będziesz czytać i wyświetlać powiązane dane — czyli dane, które entity framework ładuje do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a3267-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="a3267-106">Na poniższych ilustracjach przedstawiono strony, z którymi będziesz pracować.</span><span class="sxs-lookup"><span data-stu-id="a3267-106">The following illustrations show the pages that you'll work with.</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

<span data-ttu-id="a3267-109">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a3267-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a3267-110">Dowiedz się, jak załadować powiązane dane</span><span class="sxs-lookup"><span data-stu-id="a3267-110">Learn how to load related data</span></span>
> * <span data-ttu-id="a3267-111">Tworzenie strony Kursy</span><span class="sxs-lookup"><span data-stu-id="a3267-111">Create a Courses page</span></span>
> * <span data-ttu-id="a3267-112">Tworzenie strony Instruktorzy</span><span class="sxs-lookup"><span data-stu-id="a3267-112">Create an Instructors page</span></span>
> * <span data-ttu-id="a3267-113">Dowiedz się więcej o jawnym ładowaniu</span><span class="sxs-lookup"><span data-stu-id="a3267-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a3267-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a3267-114">Prerequisites</span></span>

* [<span data-ttu-id="a3267-115">Tworzenie złożonego modelu danych</span><span class="sxs-lookup"><span data-stu-id="a3267-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="a3267-116">Dowiedz się, jak załadować powiązane dane</span><span class="sxs-lookup"><span data-stu-id="a3267-116">Learn how to load related data</span></span>

<span data-ttu-id="a3267-117">Istnieje kilka sposobów, że oprogramowanie orm (Object-relational Mapping), takie jak Entity Framework, może załadować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="a3267-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="a3267-118">Gorliwy załadunek.</span><span class="sxs-lookup"><span data-stu-id="a3267-118">Eager loading.</span></span> <span data-ttu-id="a3267-119">Podczas odczytywania jednostki powiązane dane są pobierane wraz z nią.</span><span class="sxs-lookup"><span data-stu-id="a3267-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="a3267-120">Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="a3267-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="a3267-121">Określić wczesne ładowanie w entity `Include` framework `ThenInclude` core przy użyciu i metody.</span><span class="sxs-lookup"><span data-stu-id="a3267-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="a3267-123">Można pobrać niektóre dane w oddzielnych kwerend i EF "rozwiązuje" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a3267-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="a3267-124">Oznacza to, że EF automatycznie dodaje oddzielnie pobrane jednostki, w których należą do właściwości nawigacji wcześniej pobranych jednostek.</span><span class="sxs-lookup"><span data-stu-id="a3267-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="a3267-125">Dla kwerendy pobierającej powiązane dane można `Load` użyć metody zamiast metody zwracanej listy lub `ToList` `Single`obiektu, takiej jak lub .</span><span class="sxs-lookup"><span data-stu-id="a3267-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="a3267-127">Jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="a3267-127">Explicit loading.</span></span> <span data-ttu-id="a3267-128">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="a3267-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="a3267-129">Piszesz kod, który pobiera powiązane dane, jeśli jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="a3267-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="a3267-130">Podobnie jak w przypadku zapału ładowania z oddzielnych zapytań, jawne ładowanie powoduje wiele zapytań wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a3267-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="a3267-131">Różnica polega na tym, że przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane.</span><span class="sxs-lookup"><span data-stu-id="a3267-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="a3267-132">W entity framework core 1.1 `Load` można użyć metody do jawnego ładowania.</span><span class="sxs-lookup"><span data-stu-id="a3267-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="a3267-133">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a3267-133">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="a3267-135">Z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="a3267-135">Lazy loading.</span></span> <span data-ttu-id="a3267-136">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="a3267-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="a3267-137">Jednak przy pierwszej próbie uzyskania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="a3267-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="a3267-138">Kwerenda jest wysyłana do bazy danych za każdym razem, gdy próbujesz uzyskać dane z właściwości nawigacji po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="a3267-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="a3267-139">Entity Framework Core 1.0 nie obsługuje ładowania z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="a3267-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="a3267-140">Zagadnienia dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="a3267-140">Performance considerations</span></span>

<span data-ttu-id="a3267-141">Jeśli wiesz, że potrzebujesz powiązanych danych dla każdej pobranej jednostki, wczesne ładowanie często oferuje najlepszą wydajność, ponieważ pojedyncze zapytanie wysyłane do bazy danych jest zazwyczaj bardziej wydajne niż oddzielne zapytania dla każdej pobranej jednostki.</span><span class="sxs-lookup"><span data-stu-id="a3267-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="a3267-142">Załóżmy na przykład, że każdy dział ma dziesięć powiązanych kursów.</span><span class="sxs-lookup"><span data-stu-id="a3267-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="a3267-143">Wczesne ładowanie wszystkich powiązanych danych spowodowałoby tylko jedno zapytanie (sprzężenie) i pojedynczą podróż w obie strony do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a3267-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="a3267-144">Oddzielne zapytanie dla kursów dla każdego działu spowodowałoby jedenaście rund do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a3267-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="a3267-145">Dodatkowe rundy do bazy danych są szczególnie szkodliwe dla wydajności, gdy opóźnienie jest wysokie.</span><span class="sxs-lookup"><span data-stu-id="a3267-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="a3267-146">Z drugiej strony w niektórych scenariuszach oddzielne zapytania jest bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="a3267-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="a3267-147">Wczesne ładowanie wszystkich powiązanych danych w jednej kwerendzie może spowodować wygenerowanie bardzo złożonego sprzężenia, którego program SQL Server nie może wydajnie przetwarzać.</span><span class="sxs-lookup"><span data-stu-id="a3267-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="a3267-148">Lub jeśli chcesz uzyskać dostęp do właściwości nawigacji jednostki tylko dla podzbioru zestawu jednostek, które przetwarzasz, oddzielne zapytania mogą działać lepiej, ponieważ wczesne ładowanie wszystkiego z góry spowoduje pobranie większej ilości danych niż jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="a3267-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="a3267-149">Jeśli wydajność jest krytyczna, najlepiej przetestować wydajność w obie strony, aby dokonać najlepszego wyboru.</span><span class="sxs-lookup"><span data-stu-id="a3267-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="a3267-150">Tworzenie strony Kursy</span><span class="sxs-lookup"><span data-stu-id="a3267-150">Create a Courses page</span></span>

<span data-ttu-id="a3267-151">Course Jednostki zawiera właściwość nawigacji, która zawiera Dział jednostki działu, do której kurs jest przypisany.</span><span class="sxs-lookup"><span data-stu-id="a3267-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="a3267-152">Aby wyświetlić nazwę przypisanego działu na liście kursów, należy uzyskać Name właściwości z Department `Course.Department` jednostki, która znajduje się we właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a3267-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="a3267-153">Utwórz kontroler o nazwie CoursesController dla typu jednostki Course, używając tych samych opcji dla **kontrolera MVC z widokami, używając** szkieletu entity framework, który wykonano wcześniej dla kontrolera Studenci, jak pokazano na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="a3267-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Dodaj kontroler kursów](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="a3267-155">Otwórz *CoursesController.cs* i zbadaj `Index` metodę.</span><span class="sxs-lookup"><span data-stu-id="a3267-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="a3267-156">Automatyczne szkielety określono wczesne ładowanie dla `Department` właściwości nawigacji `Include` przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="a3267-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="a3267-157">`Index` Zastąp metodę następującym kodem, który `IQueryable` używa bardziej odpowiedniej`courses` nazwy dla `schoolContext`jednostek kursu , która zwraca ( zamiast):</span><span class="sxs-lookup"><span data-stu-id="a3267-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="a3267-158">Otwórz *widoki/kursy/index.cshtml* i zastąp kod szablonu następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="a3267-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="a3267-159">Zmiany są wyróżnione:</span><span class="sxs-lookup"><span data-stu-id="a3267-159">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="a3267-160">Wprowadzono następujące zmiany w kodzie szkieletu:</span><span class="sxs-lookup"><span data-stu-id="a3267-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="a3267-161">Zmieniono nagłówek z Indeks na Kursy.</span><span class="sxs-lookup"><span data-stu-id="a3267-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="a3267-162">Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a3267-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="a3267-163">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="a3267-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="a3267-164">Jednak w tym przypadku klucz podstawowy ma znaczenie i chcesz go pokazać.</span><span class="sxs-lookup"><span data-stu-id="a3267-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="a3267-165">Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="a3267-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="a3267-166">Kod wyświetla `Name` właściwość department jednostki, która `Department` jest ładowana do właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="a3267-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="a3267-167">Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="a3267-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="a3267-169">Tworzenie strony Instruktorzy</span><span class="sxs-lookup"><span data-stu-id="a3267-169">Create an Instructors page</span></span>

<span data-ttu-id="a3267-170">W tej sekcji utworzysz kontroler i widok dla instructor jednostki w celu wyświetlenia instruktorów strony:</span><span class="sxs-lookup"><span data-stu-id="a3267-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

<span data-ttu-id="a3267-172">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a3267-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="a3267-173">Lista instruktorów wyświetla powiązane dane z officeAssignment jednostki.</span><span class="sxs-lookup"><span data-stu-id="a3267-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="a3267-174">Jednostki Instructor i OfficeAssignment znajdują się w relacji jeden do zera lub jeden.</span><span class="sxs-lookup"><span data-stu-id="a3267-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="a3267-175">Użyjesz zaauwania chętnie dla officeAssignment jednostek.</span><span class="sxs-lookup"><span data-stu-id="a3267-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="a3267-176">Jak wyjaśniono wcześniej, wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy potrzebujesz powiązanych danych dla wszystkich pobranych wierszy tabeli podstawowej.</span><span class="sxs-lookup"><span data-stu-id="a3267-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="a3267-177">W takim przypadku chcesz wyświetlić przypisania biura dla wszystkich wyświetlanych instruktorów.</span><span class="sxs-lookup"><span data-stu-id="a3267-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="a3267-178">Gdy użytkownik wybierze instruktora, wyświetlane są powiązane jednostki kursu.</span><span class="sxs-lookup"><span data-stu-id="a3267-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="a3267-179">Instruktor i kurs jednostek są w relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="a3267-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="a3267-180">Użyjesz wczesnego ładowania dla jednostek Kursu i powiązanych z nimi jednostek Działów.</span><span class="sxs-lookup"><span data-stu-id="a3267-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="a3267-181">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są kursy tylko dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="a3267-182">Jednak w tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w obrębie jednostek, które są same we właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a3267-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="a3267-183">Gdy użytkownik wybierze kurs, wyświetlane są powiązane dane z zestawu jednostek Rejestracje.</span><span class="sxs-lookup"><span data-stu-id="a3267-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="a3267-184">Course i Enrollment jednostki są w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="a3267-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="a3267-185">Użyjesz oddzielnych zapytań dla jednostek rejestracji i powiązanych jednostek studenta.</span><span class="sxs-lookup"><span data-stu-id="a3267-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="a3267-186">Tworzenie modelu widoku dla widoku Indeks instruktora</span><span class="sxs-lookup"><span data-stu-id="a3267-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="a3267-187">Instruktorzy strona zawiera dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="a3267-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="a3267-188">W związku z tym utworzysz model widoku, który zawiera trzy właściwości, z których każda przechowuje dane dla jednej z tabel.</span><span class="sxs-lookup"><span data-stu-id="a3267-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="a3267-189">W folderze *SchoolViewModels* utwórz *InstructorIndexData.cs* i zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="a3267-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="a3267-190">Tworzenie kontrolera instruktora i widoków</span><span class="sxs-lookup"><span data-stu-id="a3267-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="a3267-191">Utwórz kontroler instruktorów z akcjami odczytu/zapisu EF, jak pokazano na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="a3267-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Dodawanie kontrolera instruktorów](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="a3267-193">Otwórz *InstructorsController.cs* i dodaj instrukcję using dla obszaru nazw ViewModels:</span><span class="sxs-lookup"><span data-stu-id="a3267-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="a3267-194">Zastąp Index metody z następującym kodem, aby wykonać wczesne ładowanie powiązanych danych i umieścić go w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="a3267-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="a3267-195">Metoda akceptuje opcjonalne dane`id`trasy ( )`courseID`i parametru ciągu zapytania ( ), które zawierają wartości identyfikatorów wybranego instruktora i wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="a3267-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="a3267-196">Parametry są dostarczane przez **wybierz** hiperłącza na stronie.</span><span class="sxs-lookup"><span data-stu-id="a3267-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="a3267-197">Kod rozpoczyna się od utworzenia wystąpienia modelu widoku i umieszczenia w nim listy instruktorów.</span><span class="sxs-lookup"><span data-stu-id="a3267-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="a3267-198">Kod określa wczesne ładowanie `Instructor.OfficeAssignment` dla `Instructor.CourseAssignments` i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a3267-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="a3267-199">W `CourseAssignments` obrębie właściwości `Course` właściwość jest ładowana, `Department` a w tym, `Enrollment` `Enrollments` i `Student` właściwości są ładowane, a w ramach każdej jednostki właściwość jest ładowany.</span><span class="sxs-lookup"><span data-stu-id="a3267-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="a3267-200">Ponieważ widok zawsze wymaga OfficeAssignment jednostki, jest bardziej efektywne, aby pobrać, że w tej samej kwerendy.</span><span class="sxs-lookup"><span data-stu-id="a3267-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="a3267-201">Jednostki kursu są wymagane, gdy instruktor jest wybrany na stronie sieci web, więc pojedyncze zapytanie jest lepsze niż wiele zapytań tylko wtedy, gdy strona jest wyświetlana częściej z wybranym kursem niż bez.</span><span class="sxs-lookup"><span data-stu-id="a3267-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="a3267-202">Kod powtarza `CourseAssignments` się `Course` i dlatego, że `Course`potrzebne są dwie właściwości z .</span><span class="sxs-lookup"><span data-stu-id="a3267-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="a3267-203">Pierwszy ciąg `ThenInclude` wywołań `CourseAssignment.Course` `Course.Enrollments`dostaje `Enrollment.Student`, i .</span><span class="sxs-lookup"><span data-stu-id="a3267-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="a3267-204">W tym momencie w `ThenInclude` kodzie, inny `Student`będzie dla właściwości nawigacji , które nie są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="a3267-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="a3267-205">Ale `Include` wywołanie zaczyna `Instructor` się od początku z właściwości, więc trzeba przejść `Course.Department` przez `Course.Enrollments`łańcuch ponownie, tym razem określając zamiast .</span><span class="sxs-lookup"><span data-stu-id="a3267-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="a3267-206">Poniższy kod jest wykonywany, gdy wybrano instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="a3267-207">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="a3267-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="a3267-208">`Courses` Właściwość modelu widoku jest następnie ładowany z Course `CourseAssignments` jednostek z właściwości nawigacji tego instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="a3267-209">Metoda `Where` zwraca kolekcję, ale w tym przypadku kryteria przekazane do tej metody spowodować tylko jednej instructor jednostki zwracane.</span><span class="sxs-lookup"><span data-stu-id="a3267-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="a3267-210">Metoda `Single` konwertuje kolekcję na jedną jednostkę Instructor, która `CourseAssignments` daje dostęp do właściwości tej jednostki.</span><span class="sxs-lookup"><span data-stu-id="a3267-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="a3267-211">Właściwość `CourseAssignments` `CourseAssignment` zawiera jednostki, z których `Course` mają być tylko powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="a3267-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="a3267-212">Metoda jest `Single` używana w kolekcji, gdy wiesz, że kolekcja będzie miała tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="a3267-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="a3267-213">Single Metoda zgłasza wyjątek, jeśli kolekcja przekazana do jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="a3267-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="a3267-214">Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="a3267-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="a3267-215">Jednak w tym przypadku, które nadal spowodować wyjątek `Courses` (od próby znalezienia właściwości na odwołanie null), a komunikat wyjątku będzie mniej wyraźnie wskazać przyczynę problemu.</span><span class="sxs-lookup"><span data-stu-id="a3267-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="a3267-216">Po wywołaniu `Single` metody, można również przekazać w Where warunek `Where` zamiast wywoływania metody oddzielnie:</span><span class="sxs-lookup"><span data-stu-id="a3267-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="a3267-217">Zamiast:</span><span class="sxs-lookup"><span data-stu-id="a3267-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="a3267-218">Następnie, jeśli wybrano kurs, wybrany kurs jest pobierany z listy kursów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="a3267-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="a3267-219">Następnie `Enrollments` właściwość modelu widoku jest ładowana z Enrollment `Enrollments` jednostek z właściwości nawigacji tego kursu.</span><span class="sxs-lookup"><span data-stu-id="a3267-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="a3267-220">Modyfikowanie widoku indeksu instruktora</span><span class="sxs-lookup"><span data-stu-id="a3267-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="a3267-221">W *views/instructors/index.cshtml*, zastąp kod szablonu następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="a3267-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="a3267-222">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="a3267-222">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="a3267-223">Wprowadzono następujące zmiany w istniejącym kodzie:</span><span class="sxs-lookup"><span data-stu-id="a3267-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="a3267-224">Zmieniono klasę `InstructorIndexData`modelu na .</span><span class="sxs-lookup"><span data-stu-id="a3267-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="a3267-225">Zmieniono tytuł strony z **Indeks** na **Instruktorzy**.</span><span class="sxs-lookup"><span data-stu-id="a3267-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="a3267-226">Dodano kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="a3267-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="a3267-227">(Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment).</span><span class="sxs-lookup"><span data-stu-id="a3267-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="a3267-228">Dodano kolumnę **Kursy,** w której wyświetlane są kursy prowadzone przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="a3267-229">Aby uzyskać więcej informacji, zobacz [sekcję Jawne przejście wiersza](xref:mvc/views/razor#explicit-line-transition) w artykule składni Razor.</span><span class="sxs-lookup"><span data-stu-id="a3267-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="a3267-230">Dodano kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="a3267-231">Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="a3267-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="a3267-232">Dodano nowe hiperłącze oznaczone **Select** bezpośrednio przed innymi łączami w każdym wierszu, co `Index` powoduje, że identyfikator wybranego instruktora ma zostać wysłany do metody.</span><span class="sxs-lookup"><span data-stu-id="a3267-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="a3267-233">Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla Właściwość Lokalizacja powiązanych encji OfficeAssignment i pustą komórkę tabeli, gdy nie ma powiązanej encji OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="a3267-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Strona Indeks instruktorów nic nie zaznaczona](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="a3267-235">W *pliku Views/Instructors/Index.cshtml* po elemencie tabeli zamknięcia (na końcu pliku) dodaj następujący kod.</span><span class="sxs-lookup"><span data-stu-id="a3267-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="a3267-236">Ten kod wyświetla listę kursów związanych z instruktorem, gdy zostanie wybrany instruktor.</span><span class="sxs-lookup"><span data-stu-id="a3267-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="a3267-237">Ten kod `Courses` odczytuje właściwość modelu widoku, aby wyświetlić listę kursów.</span><span class="sxs-lookup"><span data-stu-id="a3267-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="a3267-238">Zawiera również **wybierz** hiperłącze, które wysyła identyfikator wybranego `Index` kursu do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3267-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="a3267-239">Odśwież stronę i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="a3267-240">Teraz zostanie wyświetlona siatka, która wyświetla kursy przypisane do wybranego instruktora, a dla każdego kursu zostanie wyświetlona nazwa przypisanego działu.</span><span class="sxs-lookup"><span data-stu-id="a3267-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Wybrano instruktora strony Indeks instruktorów](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="a3267-242">Po dodaniu bloku kodu dodaj następujący kod.</span><span class="sxs-lookup"><span data-stu-id="a3267-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="a3267-243">Spowoduje to wyświetlenie listy uczniów, którzy są zapisani na kurs po wybraniu tego kursu.</span><span class="sxs-lookup"><span data-stu-id="a3267-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="a3267-244">Ten kod odczytuje Enrollments właściwość modelu widoku w celu wyświetlenia listy studentów zarejestrowanych w kursie.</span><span class="sxs-lookup"><span data-stu-id="a3267-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="a3267-245">Odśwież stronę ponownie i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="a3267-246">Następnie wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.</span><span class="sxs-lookup"><span data-stu-id="a3267-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="a3267-248">Informacje o jawnym ładowaniu</span><span class="sxs-lookup"><span data-stu-id="a3267-248">About explicit loading</span></span>

<span data-ttu-id="a3267-249">Po pobraniu listy instruktorów w *InstructorsController.cs,* określono wczesne ładowanie `CourseAssignments` dla właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a3267-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="a3267-250">Załóżmy, że użytkownicy rzadko chcą widzieć rejestracje w wybranym instruktorze i kursie.</span><span class="sxs-lookup"><span data-stu-id="a3267-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="a3267-251">W takim przypadku można załadować dane rejestracji tylko wtedy, gdy jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="a3267-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="a3267-252">Aby zobaczyć przykład jak zrobić jawne `Index` ładowanie, zastąp metodę następującym kodem, który usuwa wczesne ładowanie dla rejestracji i ładuje tę właściwość jawnie.</span><span class="sxs-lookup"><span data-stu-id="a3267-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="a3267-253">Zmiany kodu są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="a3267-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="a3267-254">Nowy kod porzuca *ThenInclude* metoda wywołuje dane rejestracji z kodu, który pobiera jednostki instruktora.</span><span class="sxs-lookup"><span data-stu-id="a3267-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="a3267-255">To również `AsNoTracking`spada .</span><span class="sxs-lookup"><span data-stu-id="a3267-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="a3267-256">Jeśli wybrano instruktora i kurs, wyróżniony kod pobiera jednostki rejestracji dla wybranego kursu i jednostki Student dla każdej rejestracji.</span><span class="sxs-lookup"><span data-stu-id="a3267-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="a3267-257">Uruchom aplikację, przejdź do strony Indeks instruktorów teraz i nie zobaczysz żadnej różnicy w tym, co jest wyświetlane na stronie, chociaż zmieniono sposób pobierania danych.</span><span class="sxs-lookup"><span data-stu-id="a3267-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="a3267-258">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="a3267-258">Get the code</span></span>

[<span data-ttu-id="a3267-259">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="a3267-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="a3267-260">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="a3267-260">Next steps</span></span>

<span data-ttu-id="a3267-261">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a3267-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a3267-262">Dowiedz się, jak załadować powiązane dane</span><span class="sxs-lookup"><span data-stu-id="a3267-262">Learned how to load related data</span></span>
> * <span data-ttu-id="a3267-263">Utworzono stronę Kursy</span><span class="sxs-lookup"><span data-stu-id="a3267-263">Created a Courses page</span></span>
> * <span data-ttu-id="a3267-264">Utworzono stronę Instruktorzy</span><span class="sxs-lookup"><span data-stu-id="a3267-264">Created an Instructors page</span></span>
> * <span data-ttu-id="a3267-265">Dowiedziałem się o jawnym załadunku</span><span class="sxs-lookup"><span data-stu-id="a3267-265">Learned about explicit loading</span></span>

<span data-ttu-id="a3267-266">Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="a3267-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a3267-267">Aktualizowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="a3267-267">Update related data</span></span>](update-related-data.md)
