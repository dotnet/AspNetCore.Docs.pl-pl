---
title: Strony brzytwy z EF Core w ASP.NET Core - Odczyt powiązanych danych - 6 z 8
author: rick-anderson
description: W tym samouczku można odczytać i wyświetlić powiązane dane - czyli dane, które entity framework ładuje do właściwości nawigacji.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 0835dbf6b5434e4224d9f56ea3335df4b5d9e119
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277330"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="5106b-103">Strony brzytwy z EF Core w ASP.NET Core - Odczyt powiązanych danych - 6 z 8</span><span class="sxs-lookup"><span data-stu-id="5106b-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="5106b-104">Przez [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)i Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5106b-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5106b-105">W tym samouczku pokazano, jak odczytywać i wyświetlać powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="5106b-106">Powiązane dane to dane, które EF Core ładuje do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="5106b-107">Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="5106b-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index30.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="5106b-110">Zapalony, jawny i leniwy ładowanie</span><span class="sxs-lookup"><span data-stu-id="5106b-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="5106b-111">Istnieje kilka sposobów, że EF Core można załadować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="5106b-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="5106b-112">[Gorliwy załadunek](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="5106b-113">Wczesne ładowanie jest, gdy kwerenda dla jednego typu jednostki ładuje również powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="5106b-114">Podczas odczytywania jednostki pobierane są powiązane z nią dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="5106b-115">Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="5106b-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="5106b-116">EF Core wyda wiele zapytań dla niektórych typów ładowania chętnych.</span><span class="sxs-lookup"><span data-stu-id="5106b-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="5106b-117">Wydawanie wielu zapytań może być bardziej wydajne niż gigantyczne pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="5106b-118">Wczesne ładowanie jest `Include` określone `ThenInclude` za pomocą i metod.</span><span class="sxs-lookup"><span data-stu-id="5106b-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="5106b-120">Wczesne ładowanie wysyła wiele zapytań, gdy nawigacja kolekcji jest uwzględniona:</span><span class="sxs-lookup"><span data-stu-id="5106b-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="5106b-121">Jedno zapytanie dla kwerendy głównej</span><span class="sxs-lookup"><span data-stu-id="5106b-121">One query for the main query</span></span> 
  * <span data-ttu-id="5106b-122">Jedna kwerenda dla każdej kolekcji "krawędzi" w drzewie obciążenia.</span><span class="sxs-lookup"><span data-stu-id="5106b-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="5106b-123">Oddzielne zapytania `Load`z: Dane mogą być pobierane w oddzielnych kwerend, a EF Core "rozwiązuje" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="5106b-124">"Poprawki w górę" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="5106b-125">Oddzielne zapytania `Load` z jest bardziej jak jawne ładowanie niż wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="5106b-127">Uwaga: EF Core automatycznie naprawia właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5106b-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5106b-128">Nawet jeśli dane dla właściwości nawigacji *nie* jest jawnie uwzględnione, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="5106b-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="5106b-129">[Jawne ładowanie](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="5106b-130">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="5106b-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5106b-131">Kod musi być zapisany, aby pobrać powiązane dane, gdy jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="5106b-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="5106b-132">Jawne ładowanie z oddzielnymi zapytaniami powoduje wiele zapytań wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5106b-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="5106b-133">Przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane.</span><span class="sxs-lookup"><span data-stu-id="5106b-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="5106b-134">Użyj `Load` metody, aby zrobić jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="5106b-135">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5106b-135">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="5106b-137">[Z opóźnieniem ładowania](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5106b-138">[Z opóźnieniem dodano do EF Core w wersji 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5106b-139">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="5106b-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5106b-140">Przy pierwszym dostępie do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="5106b-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="5106b-141">Kwerenda jest wysyłana do bazy danych za każdym razem, gdy właściwość nawigacji jest dostępna po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="5106b-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="5106b-142">Tworzenie stron kursu</span><span class="sxs-lookup"><span data-stu-id="5106b-142">Create Course pages</span></span>

<span data-ttu-id="5106b-143">Jednostka `Course` zawiera właściwość nawigacji, `Department` która zawiera jednostkę pokrewną.</span><span class="sxs-lookup"><span data-stu-id="5106b-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="5106b-145">Aby wyświetlić nazwę przypisanego działu kursu:</span><span class="sxs-lookup"><span data-stu-id="5106b-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="5106b-146">Załaduj `Department` `Course.Department` powiązaną encję do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="5106b-147">Pobierz nazwę z `Department` właściwości `Name` jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="5106b-148">Strony kursu rusztowania</span><span class="sxs-lookup"><span data-stu-id="5106b-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5106b-149">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5106b-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5106b-150">Postępuj zgodnie z instrukcjami na [stronach Scaffold Student](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="5106b-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="5106b-151">Utwórz folder *Strony/Kursy.*</span><span class="sxs-lookup"><span data-stu-id="5106b-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="5106b-152">Użyj `Course` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="5106b-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="5106b-153">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="5106b-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5106b-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5106b-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5106b-155">Utwórz folder *Strony/Kursy.*</span><span class="sxs-lookup"><span data-stu-id="5106b-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="5106b-156">Uruchom następujące polecenie, aby s rusztować strony course.</span><span class="sxs-lookup"><span data-stu-id="5106b-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="5106b-157">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="5106b-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="5106b-158">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="5106b-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="5106b-159">Otwórz *strony/kursy/index.cshtml.cs* i `OnGetAsync` sprawdź metodę.</span><span class="sxs-lookup"><span data-stu-id="5106b-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="5106b-160">Aparat rusztowań określone gorliwe ładowanie dla właściwości `Department` nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="5106b-161">Metoda `Include` określa wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="5106b-162">Uruchom aplikację i wybierz **łącze Kursy.**</span><span class="sxs-lookup"><span data-stu-id="5106b-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="5106b-163">W kolumnie `DepartmentID`działu jest wyświetlany program , który nie jest przydatny.</span><span class="sxs-lookup"><span data-stu-id="5106b-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="5106b-164">Wyświetlanie nazwy działu</span><span class="sxs-lookup"><span data-stu-id="5106b-164">Display the department name</span></span>

<span data-ttu-id="5106b-165">Aktualizuj strony/kursy/index.cshtml.cs za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5106b-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="5106b-166">Powyższy kod zmienia `Course` właściwość `Courses` `AsNoTracking`i dodaje .</span><span class="sxs-lookup"><span data-stu-id="5106b-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="5106b-167">`AsNoTracking`zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone.</span><span class="sxs-lookup"><span data-stu-id="5106b-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="5106b-168">Jednostki nie muszą być śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.</span><span class="sxs-lookup"><span data-stu-id="5106b-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="5106b-169">Zaktualizuj *strony/kursy/index.cshtml* za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="5106b-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="5106b-170">W kodzie rusztowania wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5106b-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="5106b-171">Zmieniono `Course` nazwę `Courses`właściwości na .</span><span class="sxs-lookup"><span data-stu-id="5106b-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="5106b-172">Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości.</span><span class="sxs-lookup"><span data-stu-id="5106b-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="5106b-173">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="5106b-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="5106b-174">Jednak w tym przypadku klucz podstawowy ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="5106b-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="5106b-175">Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="5106b-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="5106b-176">Kod wyświetla `Name` właściwość `Department` jednostki, która jest `Department` ładowana do właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="5106b-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="5106b-177">Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="5106b-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="5106b-179">Ładowanie powiązanych danych za pomocą opcji Wybierz</span><span class="sxs-lookup"><span data-stu-id="5106b-179">Loading related data with Select</span></span>

<span data-ttu-id="5106b-180">Metoda `OnGetAsync` ładuje powiązane dane `Include` za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="5106b-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="5106b-181">Metoda `Select` jest alternatywą, która ładuje tylko powiązane dane potrzebne.</span><span class="sxs-lookup"><span data-stu-id="5106b-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="5106b-182">Dla pojedynczych elementów, takich `Department.Name` jak używa SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="5106b-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="5106b-183">Dla kolekcji używa innego dostępu do bazy `Include` danych, ale tak samo operator w kolekcjach.</span><span class="sxs-lookup"><span data-stu-id="5106b-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="5106b-184">Następujący kod ładuje powiązane `Select` dane za pomocą metody:</span><span class="sxs-lookup"><span data-stu-id="5106b-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="5106b-185">Poprzedni kod nie zwraca żadnych typów jednostek, w związku z tym nie śledzenie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="5106b-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="5106b-186">Aby uzyskać więcej informacji na temat śledzenia EF, zobacz [Śledzenie a nie śledzenie zapytań](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="5106b-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="5106b-187">W: `CourseViewModel`</span><span class="sxs-lookup"><span data-stu-id="5106b-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="5106b-188">Zobacz [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="5106b-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="5106b-189">Tworzenie stron instruktora</span><span class="sxs-lookup"><span data-stu-id="5106b-189">Create Instructor pages</span></span>

<span data-ttu-id="5106b-190">W tej sekcji szkielety instructor stron i dodaje powiązanych kursów i rejestracji do instruktorów indeks strony.</span><span class="sxs-lookup"><span data-stu-id="5106b-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="5106b-191">![Strona Indeks instruktorów](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="5106b-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="5106b-192">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5106b-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="5106b-193">Lista instruktorów wyświetla powiązane dane `OfficeAssignment` z jednostki (Office w poprzednim obrazie).</span><span class="sxs-lookup"><span data-stu-id="5106b-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="5106b-194">I `Instructor` `OfficeAssignment` jednostki są w relacji jeden do zera lub jeden.</span><span class="sxs-lookup"><span data-stu-id="5106b-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="5106b-195">Wczesne ładowanie jest `OfficeAssignment` używane dla jednostek.</span><span class="sxs-lookup"><span data-stu-id="5106b-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="5106b-196">Wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy powiązane dane muszą być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="5106b-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="5106b-197">W takim przypadku są wyświetlane zadania biurowe dla instruktorów.</span><span class="sxs-lookup"><span data-stu-id="5106b-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="5106b-198">Gdy użytkownik wybierze instruktora, są wyświetlane powiązane `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="5106b-199">I `Instructor` `Course` jednostki są w relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="5106b-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="5106b-200">Wczesne ładowanie jest `Course` używane dla jednostek i ich jednostek powiązanych. `Department`</span><span class="sxs-lookup"><span data-stu-id="5106b-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="5106b-201">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są tylko kursy dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="5106b-202">W tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w jednostkach, które znajdują się we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="5106b-203">Gdy użytkownik wybierze kurs, wyświetlane `Enrollments` są powiązane dane z encji.</span><span class="sxs-lookup"><span data-stu-id="5106b-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="5106b-204">Na poprzednim obrazie wyświetlana jest nazwa ucznia i ocena.</span><span class="sxs-lookup"><span data-stu-id="5106b-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="5106b-205">I `Course` `Enrollment` jednostki są w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="5106b-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="5106b-206">Tworzenie modelu widoku</span><span class="sxs-lookup"><span data-stu-id="5106b-206">Create a view model</span></span>

<span data-ttu-id="5106b-207">Strona instruktorów zawiera dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="5106b-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="5106b-208">Potrzebny jest model widoku, który zawiera trzy właściwości reprezentujące trzy tabele.</span><span class="sxs-lookup"><span data-stu-id="5106b-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="5106b-209">Utwórz *SchoolViewModels/InstructorIndexData.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5106b-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="5106b-210">Strony instruktora rusztowania</span><span class="sxs-lookup"><span data-stu-id="5106b-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5106b-211">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5106b-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5106b-212">Postępuj zgodnie z instrukcjami w [rusztowania stron uczniów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="5106b-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="5106b-213">Utwórz folder *Strony/Instruktorzy.*</span><span class="sxs-lookup"><span data-stu-id="5106b-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="5106b-214">Użyj `Instructor` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="5106b-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="5106b-215">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="5106b-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5106b-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5106b-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5106b-217">Utwórz folder *Strony/Instruktorzy.*</span><span class="sxs-lookup"><span data-stu-id="5106b-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="5106b-218">Uruchom następujące polecenie, aby smisowywać strony instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="5106b-219">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="5106b-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="5106b-220">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="5106b-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="5106b-221">Aby zobaczyć, jak wygląda strona szkieletu przed jej zaktualizowaniem, uruchom aplikację i przejdź do strony Instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="5106b-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="5106b-222">Aktualizuj *strony/instruktorzy/index.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5106b-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="5106b-223">Metoda `OnGetAsync` akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="5106b-224">Sprawdź kwerendę w pliku *Pages/Instructors/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="5106b-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="5106b-225">Kod określa wczesne ładowanie dla następujących właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="5106b-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="5106b-226">Zwróć uwagę na `Include` powtarzanie i `ThenInclude` metody dla `CourseAssignments` i `Course`.</span><span class="sxs-lookup"><span data-stu-id="5106b-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="5106b-227">To powtórzenie jest konieczne, aby określić wczesne `Course` ładowanie dla dwóch właściwości nawigacji jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="5106b-228">Poniższy kod jest wykonywany po`id != null`wybraniu instruktora ( ).</span><span class="sxs-lookup"><span data-stu-id="5106b-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="5106b-229">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="5106b-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="5106b-230">`Courses` Właściwość modelu widoku jest ładowana z `Course` jednostek z właściwości nawigacji tego instruktora. `CourseAssignments`</span><span class="sxs-lookup"><span data-stu-id="5106b-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="5106b-231">Metoda `Where` zwraca kolekcję.</span><span class="sxs-lookup"><span data-stu-id="5106b-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="5106b-232">Ale w takim przypadku filtr wybierze jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="5106b-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="5106b-233">więc `Single` metoda jest wywoływana do konwersji kolekcji do pojedynczej `Instructor` jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="5106b-234">Jednostka `Instructor` zapewnia dostęp `CourseAssignments` do właściwości.</span><span class="sxs-lookup"><span data-stu-id="5106b-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="5106b-235">`CourseAssignments`zapewnia dostęp do `Course` powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5106b-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5106b-237">Metoda `Single` jest używana w kolekcji, gdy kolekcja ma tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="5106b-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="5106b-238">Metoda `Single` zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="5106b-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="5106b-239">Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="5106b-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="5106b-240">Następujący kod wypełnia `Enrollments` właściwość modelu widoku po wybraniu kursu:</span><span class="sxs-lookup"><span data-stu-id="5106b-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="5106b-241">Aktualizowanie strony indeksu instruktorów</span><span class="sxs-lookup"><span data-stu-id="5106b-241">Update the instructors Index page</span></span>

<span data-ttu-id="5106b-242">Aktualizuj *strony/instruktorzy/index.cshtml* za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="5106b-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="5106b-243">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5106b-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="5106b-244">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="5106b-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="5106b-245">`"{id:int?}"`jest szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="5106b-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="5106b-246">Szablon trasy zmienia ciągi zapytania liczby całkowitej w adresie URL, aby rozsyłać dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="5106b-247">Na przykład kliknięcie łącza **Wybierz** dla instruktora `@page` tylko z dyrektywą powoduje uzyskanie następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="5106b-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="5106b-248">Gdy dyrektywa strony `@page "{id:int?}"`jest , adres URL jest:</span><span class="sxs-lookup"><span data-stu-id="5106b-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="5106b-249">Dodaje kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="5106b-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="5106b-250">Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="5106b-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="5106b-251">Dodaje kolumnę **Kursy,** w której są wyświetlane kursy prowadzone przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="5106b-252">Zobacz [Jawne przejście wiersza,](xref:mvc/views/razor#explicit-line-transition) aby uzyskać więcej informacji na temat tej składni maszynki do golenia.</span><span class="sxs-lookup"><span data-stu-id="5106b-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="5106b-253">Dodaje kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora i kursu.</span><span class="sxs-lookup"><span data-stu-id="5106b-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="5106b-254">Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="5106b-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="5106b-255">Dodaje nowe hiperłącze z etykietą **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="5106b-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="5106b-256">To łącze wysyła identyfikator wybranego `Index` instruktora do metody i ustawia kolor tła.</span><span class="sxs-lookup"><span data-stu-id="5106b-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="5106b-257">Dodaje tabelę kursów dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="5106b-258">Dodaje tabelę rejestracji uczniów dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="5106b-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="5106b-259">Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla `Location` (biuro) z `OfficeAssignment` jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="5106b-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="5106b-260">Jeśli `OfficeAssignment` wartość null ma wartość null, wyświetlana jest pusta komórka tabeli.</span><span class="sxs-lookup"><span data-stu-id="5106b-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="5106b-261">Kliknij łącze **Wybierz** dla instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="5106b-262">Zmiany stylu wiersza i kursy przypisane do tego instruktora są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="5106b-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="5106b-263">Wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.</span><span class="sxs-lookup"><span data-stu-id="5106b-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="5106b-265">Korzystanie z pojedynczego</span><span class="sxs-lookup"><span data-stu-id="5106b-265">Using Single</span></span>

<span data-ttu-id="5106b-266">Metoda `Single` może przejść `Where` w warunku `Where` zamiast wywoływania metody oddzielnie:</span><span class="sxs-lookup"><span data-stu-id="5106b-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="5106b-267">Korzystanie `Single` z Gdzie warunek jest kwestią osobistych preferencji.</span><span class="sxs-lookup"><span data-stu-id="5106b-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="5106b-268">Zapewnia żadnych korzyści za `Where` pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="5106b-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="5106b-269">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="5106b-269">Explicit loading</span></span>

<span data-ttu-id="5106b-270">Bieżący kod określa wczesne `Enrollments` `Students`ładowanie i:</span><span class="sxs-lookup"><span data-stu-id="5106b-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="5106b-271">Załóżmy, że użytkownicy rzadko chcą zobaczyć rejestracje w trakcie.</span><span class="sxs-lookup"><span data-stu-id="5106b-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="5106b-272">W takim przypadku optymalizacja będzie tylko załadować dane rejestracji, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="5106b-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="5106b-273">W tej sekcji `OnGetAsync` jest aktualizowana w `Enrollments` `Students`celu użycia jawnego ładowania i .</span><span class="sxs-lookup"><span data-stu-id="5106b-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="5106b-274">Aktualizuj *strony/instruktorzy/index.cshtml.cs* za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="5106b-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="5106b-275">Poprzedni kod porzuca *ThenInclude* metoda wywołuje rejestracji i danych studenta.</span><span class="sxs-lookup"><span data-stu-id="5106b-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="5106b-276">Jeśli kurs jest zaznaczony, jawny kod ładowania pobiera:</span><span class="sxs-lookup"><span data-stu-id="5106b-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="5106b-277">Encje `Enrollment` dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="5106b-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="5106b-278">Jednostki `Student` dla `Enrollment`każdego .</span><span class="sxs-lookup"><span data-stu-id="5106b-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="5106b-279">Należy zauważyć, że poprzedni `.AsNoTracking()`kod komentuje .</span><span class="sxs-lookup"><span data-stu-id="5106b-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="5106b-280">Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5106b-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="5106b-281">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-281">Test the app.</span></span> <span data-ttu-id="5106b-282">Z punktu widzenia użytkownika aplikacja zachowuje się identycznie jak poprzednia wersja.</span><span class="sxs-lookup"><span data-stu-id="5106b-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5106b-283">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="5106b-283">Next steps</span></span>

<span data-ttu-id="5106b-284">Następny samouczek pokazuje, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="5106b-285">[Poprzedni samouczek](xref:data/ef-rp/complex-data-model)
>[Następny samouczek](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="5106b-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5106b-286">W tym samouczku powiązane dane są odczytywane i wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="5106b-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="5106b-287">Powiązane dane to dane, które EF Core ładuje do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="5106b-288">Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="5106b-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="5106b-289">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5106b-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5106b-290">Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="5106b-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="5106b-293">Chętne, wyraźne i leniwe ładowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="5106b-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="5106b-294">Istnieje kilka sposobów, że EF Core można załadować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="5106b-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="5106b-295">[Gorliwy załadunek](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="5106b-296">Wczesne ładowanie jest, gdy kwerenda dla jednego typu jednostki ładuje również powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="5106b-297">Po odczytaniu jednostki pobierane są powiązane z nią dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="5106b-298">Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="5106b-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="5106b-299">EF Core wyda wiele zapytań dla niektórych typów ładowania chętnych.</span><span class="sxs-lookup"><span data-stu-id="5106b-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="5106b-300">Wydawanie wielu zapytań może być bardziej wydajne niż w przypadku niektórych zapytań w EF6, gdzie było pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="5106b-301">Wczesne ładowanie jest `Include` określone `ThenInclude` za pomocą i metod.</span><span class="sxs-lookup"><span data-stu-id="5106b-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="5106b-303">Wczesne ładowanie wysyła wiele zapytań, gdy nawigacja kolekcji jest uwzględniona:</span><span class="sxs-lookup"><span data-stu-id="5106b-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="5106b-304">Jedno zapytanie dla kwerendy głównej</span><span class="sxs-lookup"><span data-stu-id="5106b-304">One query for the main query</span></span> 
  * <span data-ttu-id="5106b-305">Jedna kwerenda dla każdej kolekcji "krawędzi" w drzewie obciążenia.</span><span class="sxs-lookup"><span data-stu-id="5106b-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="5106b-306">Oddzielne zapytania `Load`z: Dane mogą być pobierane w oddzielnych kwerend, a EF Core "rozwiązuje" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="5106b-307">"rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="5106b-308">Oddzielne zapytania `Load` z jest bardziej jak jawne ładowanie niż wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="5106b-310">Uwaga: EF Core automatycznie naprawia właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5106b-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5106b-311">Nawet jeśli dane dla właściwości nawigacji *nie* jest jawnie uwzględnione, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="5106b-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="5106b-312">[Jawne ładowanie](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="5106b-313">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="5106b-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5106b-314">Kod musi być zapisany, aby pobrać powiązane dane, gdy jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="5106b-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="5106b-315">Jawne ładowanie z oddzielnymi zapytaniami powoduje wiele zapytań wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5106b-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="5106b-316">Przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane.</span><span class="sxs-lookup"><span data-stu-id="5106b-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="5106b-317">Użyj `Load` metody, aby zrobić jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="5106b-318">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5106b-318">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="5106b-320">[Z opóźnieniem ładowania](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5106b-321">[Z opóźnieniem dodano do EF Core w wersji 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5106b-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5106b-322">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="5106b-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5106b-323">Przy pierwszym dostępie do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="5106b-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="5106b-324">Kwerenda jest wysyłana do bazy danych za każdym razem, gdy właściwość nawigacji jest dostępna po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="5106b-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="5106b-325">Operator `Select` ładuje tylko potrzebne powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="5106b-326">Tworzenie strony kursu z nazwą działu</span><span class="sxs-lookup"><span data-stu-id="5106b-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="5106b-327">Course Jednostki zawiera właściwość `Department` nawigacji, która zawiera jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="5106b-328">Jednostka `Department` zawiera dział, do której jest przypisany kurs.</span><span class="sxs-lookup"><span data-stu-id="5106b-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="5106b-329">Aby wyświetlić nazwę przypisanego działu na liście kursów:</span><span class="sxs-lookup"><span data-stu-id="5106b-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="5106b-330">Pobierz `Name` właściwość `Department` z jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="5106b-331">Jednostka `Department` pochodzi z `Course.Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="5106b-333">Rusztowanie modelu kursu</span><span class="sxs-lookup"><span data-stu-id="5106b-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5106b-334">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5106b-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5106b-335">Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i używać `Course` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="5106b-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5106b-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5106b-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="5106b-337">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5106b-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="5106b-338">Poprzednie polecenie scaffolds `Course` modelu.</span><span class="sxs-lookup"><span data-stu-id="5106b-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="5106b-339">Otwórz projekt w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5106b-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="5106b-340">Otwórz *strony/kursy/index.cshtml.cs* i `OnGetAsync` sprawdź metodę.</span><span class="sxs-lookup"><span data-stu-id="5106b-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="5106b-341">Aparat rusztowań określone gorliwe ładowanie dla właściwości `Department` nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="5106b-342">Metoda `Include` określa wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="5106b-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="5106b-343">Uruchom aplikację i wybierz **łącze Kursy.**</span><span class="sxs-lookup"><span data-stu-id="5106b-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="5106b-344">W kolumnie `DepartmentID`działu jest wyświetlany program , który nie jest przydatny.</span><span class="sxs-lookup"><span data-stu-id="5106b-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="5106b-345">Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5106b-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="5106b-346">Poprzedni kod dodaje `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="5106b-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="5106b-347">`AsNoTracking`zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone.</span><span class="sxs-lookup"><span data-stu-id="5106b-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="5106b-348">Jednostki nie są śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.</span><span class="sxs-lookup"><span data-stu-id="5106b-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="5106b-349">Aktualizuj *strony/kursy/index.cshtml* z następującymi wyróżnionymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="5106b-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="5106b-350">W kodzie rusztowania wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5106b-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="5106b-351">Zmieniono nagłówek z Indeks na Kursy.</span><span class="sxs-lookup"><span data-stu-id="5106b-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="5106b-352">Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości.</span><span class="sxs-lookup"><span data-stu-id="5106b-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="5106b-353">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="5106b-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="5106b-354">Jednak w tym przypadku klucz podstawowy ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="5106b-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="5106b-355">Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="5106b-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="5106b-356">Kod wyświetla `Name` właściwość `Department` jednostki, która jest `Department` ładowana do właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="5106b-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="5106b-357">Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="5106b-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="5106b-359">Ładowanie powiązanych danych za pomocą opcji Wybierz</span><span class="sxs-lookup"><span data-stu-id="5106b-359">Loading related data with Select</span></span>

<span data-ttu-id="5106b-360">Metoda `OnGetAsync` ładuje powiązane dane `Include` za pomocą metody:</span><span class="sxs-lookup"><span data-stu-id="5106b-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="5106b-361">Operator `Select` ładuje tylko potrzebne powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="5106b-362">Dla pojedynczych elementów, takich `Department.Name` jak używa SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="5106b-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="5106b-363">Dla kolekcji używa innego dostępu do bazy `Include` danych, ale tak samo operator w kolekcjach.</span><span class="sxs-lookup"><span data-stu-id="5106b-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="5106b-364">Następujący kod ładuje powiązane `Select` dane za pomocą metody:</span><span class="sxs-lookup"><span data-stu-id="5106b-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="5106b-365">W: `CourseViewModel`</span><span class="sxs-lookup"><span data-stu-id="5106b-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="5106b-366">Zobacz [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="5106b-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="5106b-367">Tworzenie strony Instruktorzy z kursami i rejestracjami</span><span class="sxs-lookup"><span data-stu-id="5106b-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="5106b-368">W tej sekcji zostanie utworzona strona Instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="5106b-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="5106b-369">![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="5106b-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="5106b-370">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5106b-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="5106b-371">Lista instruktorów wyświetla powiązane dane `OfficeAssignment` z jednostki (Office w poprzednim obrazie).</span><span class="sxs-lookup"><span data-stu-id="5106b-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="5106b-372">I `Instructor` `OfficeAssignment` jednostki są w relacji jeden do zera lub jeden.</span><span class="sxs-lookup"><span data-stu-id="5106b-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="5106b-373">Wczesne ładowanie jest `OfficeAssignment` używane dla jednostek.</span><span class="sxs-lookup"><span data-stu-id="5106b-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="5106b-374">Wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy powiązane dane muszą być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="5106b-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="5106b-375">W takim przypadku są wyświetlane zadania biurowe dla instruktorów.</span><span class="sxs-lookup"><span data-stu-id="5106b-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="5106b-376">Gdy użytkownik wybierze instruktora (Harui w poprzednim `Course` obrazie), wyświetlane są powiązane encje.</span><span class="sxs-lookup"><span data-stu-id="5106b-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="5106b-377">I `Instructor` `Course` jednostki są w relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="5106b-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="5106b-378">Wczesne ładowanie jest `Course` używane dla jednostek i ich jednostek powiązanych. `Department`</span><span class="sxs-lookup"><span data-stu-id="5106b-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="5106b-379">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są tylko kursy dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="5106b-380">W tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w jednostkach, które znajdują się we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="5106b-381">Gdy użytkownik wybierze kurs (Chemia na poprzednim obrazie), wyświetlane są powiązane dane z `Enrollments` encji.</span><span class="sxs-lookup"><span data-stu-id="5106b-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="5106b-382">Na poprzednim obrazie wyświetlana jest nazwa ucznia i ocena.</span><span class="sxs-lookup"><span data-stu-id="5106b-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="5106b-383">I `Course` `Enrollment` jednostki są w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="5106b-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="5106b-384">Tworzenie modelu widoku dla widoku Indeks instruktora</span><span class="sxs-lookup"><span data-stu-id="5106b-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="5106b-385">Strona instruktorów zawiera dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="5106b-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="5106b-386">Tworzony jest model widoku, który zawiera trzy jednostki reprezentujące trzy tabele.</span><span class="sxs-lookup"><span data-stu-id="5106b-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="5106b-387">W folderze *SchoolViewModels* utwórz *InstructorIndexData.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5106b-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="5106b-388">Rusztowanie modelu instructor</span><span class="sxs-lookup"><span data-stu-id="5106b-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5106b-389">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5106b-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5106b-390">Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i używać `Instructor` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="5106b-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5106b-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5106b-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="5106b-392">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5106b-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="5106b-393">Poprzednie polecenie scaffolds `Instructor` modelu.</span><span class="sxs-lookup"><span data-stu-id="5106b-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="5106b-394">Uruchom aplikację i przejdź do strony instruktorów.</span><span class="sxs-lookup"><span data-stu-id="5106b-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="5106b-395">Zastąp *strony/instruktorzy/index.cshtml.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5106b-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="5106b-396">Metoda `OnGetAsync` akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="5106b-397">Sprawdź kwerendę w pliku *Pages/Instructors/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="5106b-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="5106b-398">Kwerenda zawiera dwa:</span><span class="sxs-lookup"><span data-stu-id="5106b-398">The query has two includes:</span></span>

* <span data-ttu-id="5106b-399">`OfficeAssignment`: Wyświetlane w [widoku instruktorów](#IP).</span><span class="sxs-lookup"><span data-stu-id="5106b-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="5106b-400">`CourseAssignments`: Co przynosi w kursach nauczanych.</span><span class="sxs-lookup"><span data-stu-id="5106b-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="5106b-401">Aktualizowanie strony indeksu instruktorów</span><span class="sxs-lookup"><span data-stu-id="5106b-401">Update the instructors Index page</span></span>

<span data-ttu-id="5106b-402">Aktualizuj *strony/instruktorów/index.cshtml* za pomocą następujących znaczników:</span><span class="sxs-lookup"><span data-stu-id="5106b-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="5106b-403">Poprzedni znacznik wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5106b-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="5106b-404">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="5106b-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="5106b-405">`"{id:int?}"`jest szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="5106b-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="5106b-406">Szablon trasy zmienia ciągi zapytania liczby całkowitej w adresie URL, aby rozsyłać dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="5106b-407">Na przykład kliknięcie łącza **Wybierz** dla instruktora `@page` tylko z dyrektywą powoduje uzyskanie następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="5106b-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="5106b-408">Gdy dyrektywa strony `@page "{id:int?}"`jest , poprzedni adres URL jest:</span><span class="sxs-lookup"><span data-stu-id="5106b-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="5106b-409">Tytuł strony to **Instruktorzy**.</span><span class="sxs-lookup"><span data-stu-id="5106b-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="5106b-410">Dodano kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="5106b-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="5106b-411">Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="5106b-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="5106b-412">Dodano kolumnę **Kursy,** w której wyświetlane są kursy prowadzone przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="5106b-413">Zobacz [Jawne przejście wiersza,](xref:mvc/views/razor#explicit-line-transition) aby uzyskać więcej informacji na temat tej składni maszynki do golenia.</span><span class="sxs-lookup"><span data-stu-id="5106b-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="5106b-414">Dodano kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="5106b-415">Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="5106b-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="5106b-416">Dodano nowe hiperłącze z etykietą **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="5106b-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="5106b-417">To łącze wysyła identyfikator wybranego `Index` instruktora do metody i ustawia kolor tła.</span><span class="sxs-lookup"><span data-stu-id="5106b-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="5106b-418">Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla `Location` (biuro) z `OfficeAssignment` jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="5106b-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="5106b-419">Jeśli officeassignment' ma wartość null, wyświetlana jest pusta komórka tabeli.</span><span class="sxs-lookup"><span data-stu-id="5106b-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="5106b-420">Kliknij łącze **Wybierz.**</span><span class="sxs-lookup"><span data-stu-id="5106b-420">Click on the **Select** link.</span></span> <span data-ttu-id="5106b-421">Zmienia się styl wiersza.</span><span class="sxs-lookup"><span data-stu-id="5106b-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="5106b-422">Dodawanie kursów prowadzonych przez wybranego instruktora</span><span class="sxs-lookup"><span data-stu-id="5106b-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="5106b-423">Zaktualizuj `OnGetAsync` metodę w *pages/instructors/index.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5106b-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="5106b-424">Dodaj`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="5106b-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="5106b-425">Sprawdź zaktualizowaną kwerendę:</span><span class="sxs-lookup"><span data-stu-id="5106b-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="5106b-426">Poprzednia kwerenda `Department` dodaje jednostki.</span><span class="sxs-lookup"><span data-stu-id="5106b-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="5106b-427">Poniższy kod jest wykonywany po`id != null`wybraniu instruktora ( ).</span><span class="sxs-lookup"><span data-stu-id="5106b-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="5106b-428">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="5106b-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="5106b-429">`Courses` Właściwość modelu widoku jest ładowana z `Course` jednostek z właściwości nawigacji tego instruktora. `CourseAssignments`</span><span class="sxs-lookup"><span data-stu-id="5106b-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="5106b-430">Metoda `Where` zwraca kolekcję.</span><span class="sxs-lookup"><span data-stu-id="5106b-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="5106b-431">W poprzedniej `Where` metodzie zwracana `Instructor` jest tylko pojedyncza jednostka.</span><span class="sxs-lookup"><span data-stu-id="5106b-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="5106b-432">Metoda `Single` konwertuje kolekcję `Instructor` na jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="5106b-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="5106b-433">Jednostka `Instructor` zapewnia dostęp `CourseAssignments` do właściwości.</span><span class="sxs-lookup"><span data-stu-id="5106b-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="5106b-434">`CourseAssignments`zapewnia dostęp do `Course` powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5106b-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5106b-436">Metoda `Single` jest używana w kolekcji, gdy kolekcja ma tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="5106b-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="5106b-437">Metoda `Single` zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="5106b-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="5106b-438">Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="5106b-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="5106b-439">Korzystanie `SingleOrDefault` z pustej kolekcji:</span><span class="sxs-lookup"><span data-stu-id="5106b-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="5106b-440">Powoduje wyjątek (od próby `Courses` znalezienia właściwości na odwołanie null).</span><span class="sxs-lookup"><span data-stu-id="5106b-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="5106b-441">Komunikat o wyjątku będzie mniej wyraźnie wskazać przyczynę problemu.</span><span class="sxs-lookup"><span data-stu-id="5106b-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="5106b-442">Następujący kod wypełnia `Enrollments` właściwość modelu widoku po wybraniu kursu:</span><span class="sxs-lookup"><span data-stu-id="5106b-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="5106b-443">Dodaj następujące znaczniki na końcu *strony Pages/Instructors/Index.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="5106b-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="5106b-444">Poprzedni znaczników wyświetla listę kursów związanych z instruktorem, gdy instruktor jest wybrany.</span><span class="sxs-lookup"><span data-stu-id="5106b-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="5106b-445">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-445">Test the app.</span></span> <span data-ttu-id="5106b-446">Kliknij łącze **Wybierz** na stronie instruktorów.</span><span class="sxs-lookup"><span data-stu-id="5106b-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="5106b-447">Pokaż dane uczniów</span><span class="sxs-lookup"><span data-stu-id="5106b-447">Show student data</span></span>

<span data-ttu-id="5106b-448">W tej sekcji aplikacja jest aktualizowana, aby wyświetlić dane uczniów dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="5106b-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="5106b-449">Zaktualizuj `OnGetAsync` kwerendę w metodzie *Pages/Instructors/Index.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5106b-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="5106b-450">Aktualizuj *strony/instruktorzy/index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5106b-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="5106b-451">Dodaj następujące znaczniki na końcu pliku:</span><span class="sxs-lookup"><span data-stu-id="5106b-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="5106b-452">W poprzednim znaczniku wyświetlana jest lista uczniów, którzy zostali zapisani na wybrany kurs.</span><span class="sxs-lookup"><span data-stu-id="5106b-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="5106b-453">Odśwież stronę i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="5106b-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="5106b-454">Wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.</span><span class="sxs-lookup"><span data-stu-id="5106b-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="5106b-456">Korzystanie z pojedynczego</span><span class="sxs-lookup"><span data-stu-id="5106b-456">Using Single</span></span>

<span data-ttu-id="5106b-457">Metoda `Single` może przejść `Where` w warunku `Where` zamiast wywoływania metody oddzielnie:</span><span class="sxs-lookup"><span data-stu-id="5106b-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="5106b-458">Powyższe `Single` podejście nie zapewnia `Where`żadnych korzyści w stosunku do korzystania .</span><span class="sxs-lookup"><span data-stu-id="5106b-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="5106b-459">Niektórzy deweloperzy `Single` preferują styl podejścia.</span><span class="sxs-lookup"><span data-stu-id="5106b-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="5106b-460">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="5106b-460">Explicit loading</span></span>

<span data-ttu-id="5106b-461">Bieżący kod określa wczesne `Enrollments` `Students`ładowanie i:</span><span class="sxs-lookup"><span data-stu-id="5106b-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="5106b-462">Załóżmy, że użytkownicy rzadko chcą zobaczyć rejestracje w trakcie.</span><span class="sxs-lookup"><span data-stu-id="5106b-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="5106b-463">W takim przypadku optymalizacja będzie tylko załadować dane rejestracji, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="5106b-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="5106b-464">W tej sekcji `OnGetAsync` jest aktualizowana w `Enrollments` `Students`celu użycia jawnego ładowania i .</span><span class="sxs-lookup"><span data-stu-id="5106b-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="5106b-465">Zaktualizuj `OnGetAsync` następujący kod:</span><span class="sxs-lookup"><span data-stu-id="5106b-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="5106b-466">Poprzedni kod porzuca *ThenInclude* metoda wywołuje rejestracji i danych studenta.</span><span class="sxs-lookup"><span data-stu-id="5106b-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="5106b-467">Jeśli kurs jest zaznaczony, wyróżniony kod pobiera:</span><span class="sxs-lookup"><span data-stu-id="5106b-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="5106b-468">Encje `Enrollment` dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="5106b-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="5106b-469">Jednostki `Student` dla `Enrollment`każdego .</span><span class="sxs-lookup"><span data-stu-id="5106b-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="5106b-470">Zwróć uwagę na powyższe komentarze do kodu `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="5106b-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="5106b-471">Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5106b-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="5106b-472">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5106b-472">Test the app.</span></span> <span data-ttu-id="5106b-473">Z punktu widzenia użytkowników aplikacja zachowuje się identycznie jak poprzednia wersja.</span><span class="sxs-lookup"><span data-stu-id="5106b-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="5106b-474">Następny samouczek pokazuje, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="5106b-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5106b-475">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5106b-475">Additional resources</span></span>

* [<span data-ttu-id="5106b-476">Wersja YouTube tego samouczka (część1)</span><span class="sxs-lookup"><span data-stu-id="5106b-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="5106b-477">Wersja YouTube tego samouczka (część2)</span><span class="sxs-lookup"><span data-stu-id="5106b-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="5106b-478">[Poprzedni](xref:data/ef-rp/complex-data-model)
>[następny](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="5106b-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
