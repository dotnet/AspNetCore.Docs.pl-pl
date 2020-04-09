---
title: Strony brzytwy z EF Core w ASP.NET Core - Odczyt powiązanych danych - 6 z 8
author: rick-anderson
description: W tym samouczku można odczytać i wyświetlić powiązane dane - czyli dane, które entity framework ładuje do właściwości nawigacji.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: d244ce1527486466bcbc6557ec35869aa206bc4f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656577"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="38d88-103">Strony brzytwy z EF Core w ASP.NET Core - Odczyt powiązanych danych - 6 z 8</span><span class="sxs-lookup"><span data-stu-id="38d88-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="38d88-104">Przez [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)i Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38d88-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38d88-105">W tym samouczku pokazano, jak odczytywać i wyświetlać powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="38d88-106">Powiązane dane to dane, które EF Core ładuje do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="38d88-107">Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="38d88-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index30.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="38d88-110">Zapalony, jawny i leniwy ładowanie</span><span class="sxs-lookup"><span data-stu-id="38d88-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="38d88-111">Istnieje kilka sposobów, że EF Core można załadować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="38d88-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="38d88-112">[Gorliwy załadunek](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="38d88-113">Wczesne ładowanie jest, gdy kwerenda dla jednego typu jednostki ładuje również powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="38d88-114">Podczas odczytywania jednostki pobierane są powiązane z nią dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="38d88-115">Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="38d88-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="38d88-116">EF Core wyda wiele zapytań dla niektórych typów ładowania chętnych.</span><span class="sxs-lookup"><span data-stu-id="38d88-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="38d88-117">Wydawanie wielu zapytań może być bardziej wydajne niż gigantyczne pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="38d88-118">Wczesne ładowanie jest `Include` określone `ThenInclude` za pomocą i metod.</span><span class="sxs-lookup"><span data-stu-id="38d88-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="38d88-120">Wczesne ładowanie wysyła wiele zapytań, gdy nawigacja kolekcji jest uwzględniona:</span><span class="sxs-lookup"><span data-stu-id="38d88-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="38d88-121">Jedno zapytanie dla kwerendy głównej</span><span class="sxs-lookup"><span data-stu-id="38d88-121">One query for the main query</span></span> 
  * <span data-ttu-id="38d88-122">Jedna kwerenda dla każdej kolekcji "krawędzi" w drzewie obciążenia.</span><span class="sxs-lookup"><span data-stu-id="38d88-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="38d88-123">Oddzielne zapytania `Load`z: Dane mogą być pobierane w oddzielnych kwerend, a EF Core "rozwiązuje" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="38d88-124">"Poprawki w górę" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="38d88-125">Oddzielne zapytania `Load` z jest bardziej jak jawne ładowanie niż wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="38d88-127">Uwaga: EF Core automatycznie naprawia właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="38d88-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="38d88-128">Nawet jeśli dane dla właściwości nawigacji *nie* jest jawnie uwzględnione, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="38d88-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="38d88-129">[Jawne ładowanie](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="38d88-130">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="38d88-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="38d88-131">Kod musi być zapisany, aby pobrać powiązane dane, gdy jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="38d88-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="38d88-132">Jawne ładowanie z oddzielnymi zapytaniami powoduje wiele zapytań wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="38d88-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="38d88-133">Przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane.</span><span class="sxs-lookup"><span data-stu-id="38d88-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="38d88-134">Użyj `Load` metody, aby zrobić jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="38d88-135">Przykład:</span><span class="sxs-lookup"><span data-stu-id="38d88-135">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="38d88-137">[Z opóźnieniem ładowania](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="38d88-138">[Z opóźnieniem dodano do EF Core w wersji 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="38d88-139">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="38d88-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="38d88-140">Przy pierwszym dostępie do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="38d88-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="38d88-141">Kwerenda jest wysyłana do bazy danych za każdym razem, gdy właściwość nawigacji jest dostępna po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="38d88-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="38d88-142">Tworzenie stron kursu</span><span class="sxs-lookup"><span data-stu-id="38d88-142">Create Course pages</span></span>

<span data-ttu-id="38d88-143">Jednostka `Course` zawiera właściwość nawigacji, `Department` która zawiera jednostkę pokrewną.</span><span class="sxs-lookup"><span data-stu-id="38d88-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="38d88-145">Aby wyświetlić nazwę przypisanego działu kursu:</span><span class="sxs-lookup"><span data-stu-id="38d88-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="38d88-146">Załaduj `Department` `Course.Department` powiązaną encję do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="38d88-147">Pobierz nazwę z `Department` właściwości `Name` jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="38d88-148">Strony kursu rusztowania</span><span class="sxs-lookup"><span data-stu-id="38d88-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38d88-149">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38d88-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="38d88-150">Postępuj zgodnie z instrukcjami na [stronach Scaffold Student](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="38d88-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="38d88-151">Utwórz folder *Strony/Kursy.*</span><span class="sxs-lookup"><span data-stu-id="38d88-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="38d88-152">Użyj `Course` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="38d88-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="38d88-153">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="38d88-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="38d88-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d88-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="38d88-155">Utwórz folder *Strony/Kursy.*</span><span class="sxs-lookup"><span data-stu-id="38d88-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="38d88-156">Uruchom następujące polecenie, aby s rusztować strony course.</span><span class="sxs-lookup"><span data-stu-id="38d88-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="38d88-157">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="38d88-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="38d88-158">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="38d88-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="38d88-159">Otwórz *strony/kursy/index.cshtml.cs* i `OnGetAsync` sprawdź metodę.</span><span class="sxs-lookup"><span data-stu-id="38d88-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="38d88-160">Aparat rusztowań określone gorliwe ładowanie dla właściwości `Department` nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="38d88-161">Metoda `Include` określa wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="38d88-162">Uruchom aplikację i wybierz **łącze Kursy.**</span><span class="sxs-lookup"><span data-stu-id="38d88-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="38d88-163">W kolumnie `DepartmentID`działu jest wyświetlany program , który nie jest przydatny.</span><span class="sxs-lookup"><span data-stu-id="38d88-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="38d88-164">Wyświetlanie nazwy działu</span><span class="sxs-lookup"><span data-stu-id="38d88-164">Display the department name</span></span>

<span data-ttu-id="38d88-165">Aktualizuj strony/kursy/index.cshtml.cs za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="38d88-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="38d88-166">Powyższy kod zmienia `Course` właściwość `Courses` `AsNoTracking`i dodaje .</span><span class="sxs-lookup"><span data-stu-id="38d88-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="38d88-167">`AsNoTracking`zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone.</span><span class="sxs-lookup"><span data-stu-id="38d88-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="38d88-168">Jednostki nie muszą być śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.</span><span class="sxs-lookup"><span data-stu-id="38d88-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="38d88-169">Zaktualizuj *strony/kursy/index.cshtml* za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="38d88-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="38d88-170">W kodzie rusztowania wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="38d88-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="38d88-171">Zmieniono `Course` nazwę `Courses`właściwości na .</span><span class="sxs-lookup"><span data-stu-id="38d88-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="38d88-172">Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości.</span><span class="sxs-lookup"><span data-stu-id="38d88-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="38d88-173">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="38d88-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="38d88-174">Jednak w tym przypadku klucz podstawowy ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="38d88-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="38d88-175">Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="38d88-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="38d88-176">Kod wyświetla `Name` właściwość `Department` jednostki, która jest `Department` ładowana do właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="38d88-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="38d88-177">Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="38d88-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="38d88-179">Ładowanie powiązanych danych za pomocą opcji Wybierz</span><span class="sxs-lookup"><span data-stu-id="38d88-179">Loading related data with Select</span></span>

<span data-ttu-id="38d88-180">Metoda `OnGetAsync` ładuje powiązane dane `Include` za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="38d88-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="38d88-181">Metoda `Select` jest alternatywą, która ładuje tylko powiązane dane potrzebne.</span><span class="sxs-lookup"><span data-stu-id="38d88-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="38d88-182">Dla pojedynczych elementów, takich `Department.Name` jak używa SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="38d88-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="38d88-183">Dla kolekcji używa innego dostępu do bazy `Include` danych, ale tak samo operator w kolekcjach.</span><span class="sxs-lookup"><span data-stu-id="38d88-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="38d88-184">Następujący kod ładuje powiązane `Select` dane za pomocą metody:</span><span class="sxs-lookup"><span data-stu-id="38d88-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="38d88-185">W: `CourseViewModel`</span><span class="sxs-lookup"><span data-stu-id="38d88-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="38d88-186">Zobacz [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="38d88-186">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="38d88-187">Tworzenie stron instruktora</span><span class="sxs-lookup"><span data-stu-id="38d88-187">Create Instructor pages</span></span>

<span data-ttu-id="38d88-188">W tej sekcji szkielety instructor stron i dodaje powiązanych kursów i rejestracji do instruktorów indeks strony.</span><span class="sxs-lookup"><span data-stu-id="38d88-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="38d88-189">![Strona Indeks instruktorów](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="38d88-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="38d88-190">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="38d88-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="38d88-191">Lista instruktorów wyświetla powiązane dane `OfficeAssignment` z jednostki (Office w poprzednim obrazie).</span><span class="sxs-lookup"><span data-stu-id="38d88-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="38d88-192">I `Instructor` `OfficeAssignment` jednostki są w relacji jeden do zera lub jeden.</span><span class="sxs-lookup"><span data-stu-id="38d88-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="38d88-193">Wczesne ładowanie jest `OfficeAssignment` używane dla jednostek.</span><span class="sxs-lookup"><span data-stu-id="38d88-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="38d88-194">Wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy powiązane dane muszą być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="38d88-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="38d88-195">W takim przypadku są wyświetlane zadania biurowe dla instruktorów.</span><span class="sxs-lookup"><span data-stu-id="38d88-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="38d88-196">Gdy użytkownik wybierze instruktora, są wyświetlane powiązane `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="38d88-197">I `Instructor` `Course` jednostki są w relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="38d88-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="38d88-198">Wczesne ładowanie jest `Course` używane dla jednostek i ich jednostek powiązanych. `Department`</span><span class="sxs-lookup"><span data-stu-id="38d88-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="38d88-199">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są tylko kursy dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="38d88-200">W tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w jednostkach, które znajdują się we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="38d88-201">Gdy użytkownik wybierze kurs, wyświetlane `Enrollments` są powiązane dane z encji.</span><span class="sxs-lookup"><span data-stu-id="38d88-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="38d88-202">Na poprzednim obrazie wyświetlana jest nazwa ucznia i ocena.</span><span class="sxs-lookup"><span data-stu-id="38d88-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="38d88-203">I `Course` `Enrollment` jednostki są w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="38d88-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="38d88-204">Tworzenie modelu widoku</span><span class="sxs-lookup"><span data-stu-id="38d88-204">Create a view model</span></span>

<span data-ttu-id="38d88-205">Strona instruktorów zawiera dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="38d88-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="38d88-206">Potrzebny jest model widoku, który zawiera trzy właściwości reprezentujące trzy tabele.</span><span class="sxs-lookup"><span data-stu-id="38d88-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="38d88-207">Utwórz *SchoolViewModels/InstructorIndexData.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="38d88-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="38d88-208">Strony instruktora rusztowania</span><span class="sxs-lookup"><span data-stu-id="38d88-208">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38d88-209">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38d88-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="38d88-210">Postępuj zgodnie z instrukcjami w [rusztowania stron uczniów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="38d88-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="38d88-211">Utwórz folder *Strony/Instruktorzy.*</span><span class="sxs-lookup"><span data-stu-id="38d88-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="38d88-212">Użyj `Instructor` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="38d88-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="38d88-213">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="38d88-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="38d88-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d88-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="38d88-215">Utwórz folder *Strony/Instruktorzy.*</span><span class="sxs-lookup"><span data-stu-id="38d88-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="38d88-216">Uruchom następujące polecenie, aby smisowywać strony instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="38d88-217">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="38d88-217">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="38d88-218">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="38d88-218">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="38d88-219">Aby zobaczyć, jak wygląda strona szkieletu przed jej zaktualizowaniem, uruchom aplikację i przejdź do strony Instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="38d88-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="38d88-220">Aktualizuj *strony/instruktorzy/index.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="38d88-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="38d88-221">Metoda `OnGetAsync` akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="38d88-222">Sprawdź kwerendę w pliku *Pages/Instructors/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="38d88-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="38d88-223">Kod określa wczesne ładowanie dla następujących właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="38d88-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="38d88-224">Zwróć uwagę na `Include` powtarzanie i `ThenInclude` metody dla `CourseAssignments` i `Course`.</span><span class="sxs-lookup"><span data-stu-id="38d88-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="38d88-225">To powtórzenie jest konieczne, aby określić wczesne `Course` ładowanie dla dwóch właściwości nawigacji jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="38d88-226">Poniższy kod jest wykonywany po`id != null`wybraniu instruktora ( ).</span><span class="sxs-lookup"><span data-stu-id="38d88-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="38d88-227">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="38d88-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="38d88-228">`Courses` Właściwość modelu widoku jest ładowana z `Course` jednostek z właściwości nawigacji tego instruktora. `CourseAssignments`</span><span class="sxs-lookup"><span data-stu-id="38d88-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="38d88-229">Metoda `Where` zwraca kolekcję.</span><span class="sxs-lookup"><span data-stu-id="38d88-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="38d88-230">Ale w takim przypadku filtr wybierze jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="38d88-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="38d88-231">więc `Single` metoda jest wywoływana do konwersji kolekcji do pojedynczej `Instructor` jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="38d88-232">Jednostka `Instructor` zapewnia dostęp `CourseAssignments` do właściwości.</span><span class="sxs-lookup"><span data-stu-id="38d88-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="38d88-233">`CourseAssignments`zapewnia dostęp do `Course` powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="38d88-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="38d88-235">Metoda `Single` jest używana w kolekcji, gdy kolekcja ma tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="38d88-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="38d88-236">Metoda `Single` zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="38d88-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="38d88-237">Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="38d88-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="38d88-238">Następujący kod wypełnia `Enrollments` właściwość modelu widoku po wybraniu kursu:</span><span class="sxs-lookup"><span data-stu-id="38d88-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="38d88-239">Aktualizowanie strony indeksu instruktorów</span><span class="sxs-lookup"><span data-stu-id="38d88-239">Update the instructors Index page</span></span>

<span data-ttu-id="38d88-240">Aktualizuj *strony/instruktorzy/index.cshtml* za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="38d88-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="38d88-241">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="38d88-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="38d88-242">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="38d88-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="38d88-243">`"{id:int?}"`jest szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="38d88-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="38d88-244">Szablon trasy zmienia ciągi zapytania liczby całkowitej w adresie URL, aby rozsyłać dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="38d88-245">Na przykład kliknięcie łącza **Wybierz** dla instruktora `@page` tylko z dyrektywą powoduje uzyskanie następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="38d88-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="38d88-246">Gdy dyrektywa strony `@page "{id:int?}"`jest , adres URL jest:</span><span class="sxs-lookup"><span data-stu-id="38d88-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="38d88-247">Dodaje kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="38d88-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="38d88-248">Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="38d88-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="38d88-249">Dodaje kolumnę **Kursy,** w której są wyświetlane kursy prowadzone przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="38d88-250">Zobacz [Jawne przejście wiersza,](xref:mvc/views/razor#explicit-line-transition) aby uzyskać więcej informacji na temat tej składni maszynki do golenia.</span><span class="sxs-lookup"><span data-stu-id="38d88-250">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="38d88-251">Dodaje kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora i kursu.</span><span class="sxs-lookup"><span data-stu-id="38d88-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="38d88-252">Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="38d88-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="38d88-253">Dodaje nowe hiperłącze z etykietą **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="38d88-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="38d88-254">To łącze wysyła identyfikator wybranego `Index` instruktora do metody i ustawia kolor tła.</span><span class="sxs-lookup"><span data-stu-id="38d88-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="38d88-255">Dodaje tabelę kursów dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="38d88-256">Dodaje tabelę rejestracji uczniów dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="38d88-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="38d88-257">Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla `Location` (biuro) z `OfficeAssignment` jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="38d88-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="38d88-258">Jeśli `OfficeAssignment` wartość null ma wartość null, wyświetlana jest pusta komórka tabeli.</span><span class="sxs-lookup"><span data-stu-id="38d88-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="38d88-259">Kliknij łącze **Wybierz** dla instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="38d88-260">Zmiany stylu wiersza i kursy przypisane do tego instruktora są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="38d88-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="38d88-261">Wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.</span><span class="sxs-lookup"><span data-stu-id="38d88-261">Select a course to see the list of enrolled students and their grades.</span></span>

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="38d88-263">Korzystanie z pojedynczego</span><span class="sxs-lookup"><span data-stu-id="38d88-263">Using Single</span></span>

<span data-ttu-id="38d88-264">Metoda `Single` może przejść `Where` w warunku `Where` zamiast wywoływania metody oddzielnie:</span><span class="sxs-lookup"><span data-stu-id="38d88-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="38d88-265">Korzystanie `Single` z Gdzie warunek jest kwestią osobistych preferencji.</span><span class="sxs-lookup"><span data-stu-id="38d88-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="38d88-266">Zapewnia żadnych korzyści za `Where` pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="38d88-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="38d88-267">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="38d88-267">Explicit loading</span></span>

<span data-ttu-id="38d88-268">Bieżący kod określa wczesne `Enrollments` `Students`ładowanie i:</span><span class="sxs-lookup"><span data-stu-id="38d88-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="38d88-269">Załóżmy, że użytkownicy rzadko chcą zobaczyć rejestracje w trakcie.</span><span class="sxs-lookup"><span data-stu-id="38d88-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="38d88-270">W takim przypadku optymalizacja będzie tylko załadować dane rejestracji, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="38d88-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="38d88-271">W tej sekcji `OnGetAsync` jest aktualizowana w `Enrollments` `Students`celu użycia jawnego ładowania i .</span><span class="sxs-lookup"><span data-stu-id="38d88-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="38d88-272">Aktualizuj *strony/instruktorzy/index.cshtml.cs* za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="38d88-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="38d88-273">Poprzedni kod porzuca *ThenInclude* metoda wywołuje rejestracji i danych studenta.</span><span class="sxs-lookup"><span data-stu-id="38d88-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="38d88-274">Jeśli kurs jest zaznaczony, jawny kod ładowania pobiera:</span><span class="sxs-lookup"><span data-stu-id="38d88-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="38d88-275">Encje `Enrollment` dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="38d88-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="38d88-276">Jednostki `Student` dla `Enrollment`każdego .</span><span class="sxs-lookup"><span data-stu-id="38d88-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="38d88-277">Należy zauważyć, że poprzedni `.AsNoTracking()`kod komentuje .</span><span class="sxs-lookup"><span data-stu-id="38d88-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="38d88-278">Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="38d88-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="38d88-279">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-279">Test the app.</span></span> <span data-ttu-id="38d88-280">Z punktu widzenia użytkownika aplikacja zachowuje się identycznie jak poprzednia wersja.</span><span class="sxs-lookup"><span data-stu-id="38d88-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="38d88-281">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="38d88-281">Next steps</span></span>

<span data-ttu-id="38d88-282">Następny samouczek pokazuje, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="38d88-283">[Poprzedni samouczek](xref:data/ef-rp/complex-data-model)
>[Następny samouczek](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="38d88-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38d88-284">W tym samouczku powiązane dane są odczytywane i wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="38d88-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="38d88-285">Powiązane dane to dane, które EF Core ładuje do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="38d88-286">Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="38d88-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="38d88-287">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="38d88-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="38d88-288">Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="38d88-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="38d88-291">Chętne, wyraźne i leniwe ładowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="38d88-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="38d88-292">Istnieje kilka sposobów, że EF Core można załadować powiązane dane do właściwości nawigacji jednostki:</span><span class="sxs-lookup"><span data-stu-id="38d88-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="38d88-293">[Gorliwy załadunek](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="38d88-294">Wczesne ładowanie jest, gdy kwerenda dla jednego typu jednostki ładuje również powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="38d88-295">Po odczytaniu jednostki pobierane są powiązane z nią dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="38d88-296">Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="38d88-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="38d88-297">EF Core wyda wiele zapytań dla niektórych typów ładowania chętnych.</span><span class="sxs-lookup"><span data-stu-id="38d88-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="38d88-298">Wydawanie wielu zapytań może być bardziej wydajne niż w przypadku niektórych zapytań w EF6, gdzie było pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="38d88-299">Wczesne ładowanie jest `Include` określone `ThenInclude` za pomocą i metod.</span><span class="sxs-lookup"><span data-stu-id="38d88-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="38d88-301">Wczesne ładowanie wysyła wiele zapytań, gdy nawigacja kolekcji jest uwzględniona:</span><span class="sxs-lookup"><span data-stu-id="38d88-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="38d88-302">Jedno zapytanie dla kwerendy głównej</span><span class="sxs-lookup"><span data-stu-id="38d88-302">One query for the main query</span></span> 
  * <span data-ttu-id="38d88-303">Jedna kwerenda dla każdej kolekcji "krawędzi" w drzewie obciążenia.</span><span class="sxs-lookup"><span data-stu-id="38d88-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="38d88-304">Oddzielne zapytania `Load`z: Dane mogą być pobierane w oddzielnych kwerend, a EF Core "rozwiązuje" właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="38d88-305">"rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="38d88-306">Oddzielne zapytania `Load` z jest bardziej jak jawne ładowanie niż wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="38d88-308">Uwaga: EF Core automatycznie naprawia właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="38d88-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="38d88-309">Nawet jeśli dane dla właściwości nawigacji *nie* jest jawnie uwzględnione, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="38d88-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="38d88-310">[Jawne ładowanie](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="38d88-311">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="38d88-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="38d88-312">Kod musi być zapisany, aby pobrać powiązane dane, gdy jest to potrzebne.</span><span class="sxs-lookup"><span data-stu-id="38d88-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="38d88-313">Jawne ładowanie z oddzielnymi zapytaniami powoduje wiele zapytań wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="38d88-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="38d88-314">Przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane.</span><span class="sxs-lookup"><span data-stu-id="38d88-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="38d88-315">Użyj `Load` metody, aby zrobić jawne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="38d88-316">Przykład:</span><span class="sxs-lookup"><span data-stu-id="38d88-316">For example:</span></span>

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="38d88-318">[Z opóźnieniem ładowania](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="38d88-319">[Z opóźnieniem dodano do EF Core w wersji 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="38d88-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="38d88-320">Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane.</span><span class="sxs-lookup"><span data-stu-id="38d88-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="38d88-321">Przy pierwszym dostępie do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="38d88-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="38d88-322">Kwerenda jest wysyłana do bazy danych za każdym razem, gdy właściwość nawigacji jest dostępna po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="38d88-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="38d88-323">Operator `Select` ładuje tylko potrzebne powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="38d88-324">Tworzenie strony kursu z nazwą działu</span><span class="sxs-lookup"><span data-stu-id="38d88-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="38d88-325">Course Jednostki zawiera właściwość `Department` nawigacji, która zawiera jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="38d88-326">Jednostka `Department` zawiera dział, do której jest przypisany kurs.</span><span class="sxs-lookup"><span data-stu-id="38d88-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="38d88-327">Aby wyświetlić nazwę przypisanego działu na liście kursów:</span><span class="sxs-lookup"><span data-stu-id="38d88-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="38d88-328">Pobierz `Name` właściwość `Department` z jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="38d88-329">Jednostka `Department` pochodzi z `Course.Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="38d88-331">Rusztowanie modelu kursu</span><span class="sxs-lookup"><span data-stu-id="38d88-331">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38d88-332">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38d88-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="38d88-333">Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i używać `Course` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="38d88-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="38d88-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d88-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="38d88-335">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="38d88-335">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="38d88-336">Poprzednie polecenie scaffolds `Course` modelu.</span><span class="sxs-lookup"><span data-stu-id="38d88-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="38d88-337">Otwórz projekt w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="38d88-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="38d88-338">Otwórz *strony/kursy/index.cshtml.cs* i `OnGetAsync` sprawdź metodę.</span><span class="sxs-lookup"><span data-stu-id="38d88-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="38d88-339">Aparat rusztowań określone gorliwe ładowanie dla właściwości `Department` nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="38d88-340">Metoda `Include` określa wczesne ładowanie.</span><span class="sxs-lookup"><span data-stu-id="38d88-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="38d88-341">Uruchom aplikację i wybierz **łącze Kursy.**</span><span class="sxs-lookup"><span data-stu-id="38d88-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="38d88-342">W kolumnie `DepartmentID`działu jest wyświetlany program , który nie jest przydatny.</span><span class="sxs-lookup"><span data-stu-id="38d88-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="38d88-343">Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="38d88-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="38d88-344">Poprzedni kod dodaje `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="38d88-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="38d88-345">`AsNoTracking`zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone.</span><span class="sxs-lookup"><span data-stu-id="38d88-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="38d88-346">Jednostki nie są śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.</span><span class="sxs-lookup"><span data-stu-id="38d88-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="38d88-347">Aktualizuj *strony/kursy/index.cshtml* z następującymi wyróżnionymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="38d88-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="38d88-348">W kodzie rusztowania wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="38d88-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="38d88-349">Zmieniono nagłówek z Indeks na Kursy.</span><span class="sxs-lookup"><span data-stu-id="38d88-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="38d88-350">Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości.</span><span class="sxs-lookup"><span data-stu-id="38d88-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="38d88-351">Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych.</span><span class="sxs-lookup"><span data-stu-id="38d88-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="38d88-352">Jednak w tym przypadku klucz podstawowy ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="38d88-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="38d88-353">Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu.</span><span class="sxs-lookup"><span data-stu-id="38d88-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="38d88-354">Kod wyświetla `Name` właściwość `Department` jednostki, która jest `Department` ładowana do właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="38d88-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="38d88-355">Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.</span><span class="sxs-lookup"><span data-stu-id="38d88-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="38d88-357">Ładowanie powiązanych danych za pomocą opcji Wybierz</span><span class="sxs-lookup"><span data-stu-id="38d88-357">Loading related data with Select</span></span>

<span data-ttu-id="38d88-358">Metoda `OnGetAsync` ładuje powiązane dane `Include` za pomocą metody:</span><span class="sxs-lookup"><span data-stu-id="38d88-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="38d88-359">Operator `Select` ładuje tylko potrzebne powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="38d88-360">Dla pojedynczych elementów, takich `Department.Name` jak używa SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="38d88-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="38d88-361">Dla kolekcji używa innego dostępu do bazy `Include` danych, ale tak samo operator w kolekcjach.</span><span class="sxs-lookup"><span data-stu-id="38d88-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="38d88-362">Następujący kod ładuje powiązane `Select` dane za pomocą metody:</span><span class="sxs-lookup"><span data-stu-id="38d88-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="38d88-363">W: `CourseViewModel`</span><span class="sxs-lookup"><span data-stu-id="38d88-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="38d88-364">Zobacz [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="38d88-364">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="38d88-365">Tworzenie strony Instruktorzy z kursami i rejestracjami</span><span class="sxs-lookup"><span data-stu-id="38d88-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="38d88-366">W tej sekcji zostanie utworzona strona Instruktorzy.</span><span class="sxs-lookup"><span data-stu-id="38d88-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="38d88-367">![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="38d88-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="38d88-368">Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="38d88-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="38d88-369">Lista instruktorów wyświetla powiązane dane `OfficeAssignment` z jednostki (Office w poprzednim obrazie).</span><span class="sxs-lookup"><span data-stu-id="38d88-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="38d88-370">I `Instructor` `OfficeAssignment` jednostki są w relacji jeden do zera lub jeden.</span><span class="sxs-lookup"><span data-stu-id="38d88-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="38d88-371">Wczesne ładowanie jest `OfficeAssignment` używane dla jednostek.</span><span class="sxs-lookup"><span data-stu-id="38d88-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="38d88-372">Wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy powiązane dane muszą być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="38d88-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="38d88-373">W takim przypadku są wyświetlane zadania biurowe dla instruktorów.</span><span class="sxs-lookup"><span data-stu-id="38d88-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="38d88-374">Gdy użytkownik wybierze instruktora (Harui w poprzednim `Course` obrazie), wyświetlane są powiązane encje.</span><span class="sxs-lookup"><span data-stu-id="38d88-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="38d88-375">I `Instructor` `Course` jednostki są w relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="38d88-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="38d88-376">Wczesne ładowanie jest `Course` używane dla jednostek i ich jednostek powiązanych. `Department`</span><span class="sxs-lookup"><span data-stu-id="38d88-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="38d88-377">W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są tylko kursy dla wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="38d88-378">W tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w jednostkach, które znajdują się we właściwościach nawigacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="38d88-379">Gdy użytkownik wybierze kurs (Chemia na poprzednim obrazie), wyświetlane są powiązane dane z `Enrollments` encji.</span><span class="sxs-lookup"><span data-stu-id="38d88-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="38d88-380">Na poprzednim obrazie wyświetlana jest nazwa ucznia i ocena.</span><span class="sxs-lookup"><span data-stu-id="38d88-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="38d88-381">I `Course` `Enrollment` jednostki są w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="38d88-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="38d88-382">Tworzenie modelu widoku dla widoku Indeks instruktora</span><span class="sxs-lookup"><span data-stu-id="38d88-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="38d88-383">Strona instruktorów zawiera dane z trzech różnych tabel.</span><span class="sxs-lookup"><span data-stu-id="38d88-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="38d88-384">Tworzony jest model widoku, który zawiera trzy jednostki reprezentujące trzy tabele.</span><span class="sxs-lookup"><span data-stu-id="38d88-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="38d88-385">W folderze *SchoolViewModels* utwórz *InstructorIndexData.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="38d88-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="38d88-386">Rusztowanie modelu instructor</span><span class="sxs-lookup"><span data-stu-id="38d88-386">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38d88-387">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38d88-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="38d88-388">Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i używać `Instructor` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="38d88-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="38d88-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d88-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="38d88-390">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="38d88-390">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="38d88-391">Poprzednie polecenie scaffolds `Instructor` modelu.</span><span class="sxs-lookup"><span data-stu-id="38d88-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="38d88-392">Uruchom aplikację i przejdź do strony instruktorów.</span><span class="sxs-lookup"><span data-stu-id="38d88-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="38d88-393">Zastąp *strony/instruktorzy/index.cshtml.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="38d88-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="38d88-394">Metoda `OnGetAsync` akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="38d88-395">Sprawdź kwerendę w pliku *Pages/Instructors/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="38d88-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="38d88-396">Kwerenda zawiera dwa:</span><span class="sxs-lookup"><span data-stu-id="38d88-396">The query has two includes:</span></span>

* <span data-ttu-id="38d88-397">`OfficeAssignment`: Wyświetlane w [widoku instruktorów](#IP).</span><span class="sxs-lookup"><span data-stu-id="38d88-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="38d88-398">`CourseAssignments`: Co przynosi w kursach nauczanych.</span><span class="sxs-lookup"><span data-stu-id="38d88-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="38d88-399">Aktualizowanie strony indeksu instruktorów</span><span class="sxs-lookup"><span data-stu-id="38d88-399">Update the instructors Index page</span></span>

<span data-ttu-id="38d88-400">Aktualizuj *strony/instruktorów/index.cshtml* za pomocą następujących znaczników:</span><span class="sxs-lookup"><span data-stu-id="38d88-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="38d88-401">Poprzedni znacznik wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="38d88-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="38d88-402">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="38d88-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="38d88-403">`"{id:int?}"`jest szablonem trasy.</span><span class="sxs-lookup"><span data-stu-id="38d88-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="38d88-404">Szablon trasy zmienia ciągi zapytania liczby całkowitej w adresie URL, aby rozsyłać dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="38d88-405">Na przykład kliknięcie łącza **Wybierz** dla instruktora `@page` tylko z dyrektywą powoduje uzyskanie następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="38d88-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="38d88-406">Gdy dyrektywa strony `@page "{id:int?}"`jest , poprzedni adres URL jest:</span><span class="sxs-lookup"><span data-stu-id="38d88-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="38d88-407">Tytuł strony to **Instruktorzy**.</span><span class="sxs-lookup"><span data-stu-id="38d88-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="38d88-408">Dodano kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="38d88-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="38d88-409">Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="38d88-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="38d88-410">Dodano kolumnę **Kursy,** w której wyświetlane są kursy prowadzone przez każdego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="38d88-411">Zobacz [Jawne przejście wiersza,](xref:mvc/views/razor#explicit-line-transition) aby uzyskać więcej informacji na temat tej składni maszynki do golenia.</span><span class="sxs-lookup"><span data-stu-id="38d88-411">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="38d88-412">Dodano kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="38d88-413">Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="38d88-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="38d88-414">Dodano nowe hiperłącze z etykietą **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="38d88-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="38d88-415">To łącze wysyła identyfikator wybranego `Index` instruktora do metody i ustawia kolor tła.</span><span class="sxs-lookup"><span data-stu-id="38d88-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="38d88-416">Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla `Location` (biuro) z `OfficeAssignment` jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="38d88-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="38d88-417">Jeśli officeassignment' ma wartość null, wyświetlana jest pusta komórka tabeli.</span><span class="sxs-lookup"><span data-stu-id="38d88-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="38d88-418">Kliknij łącze **Wybierz.**</span><span class="sxs-lookup"><span data-stu-id="38d88-418">Click on the **Select** link.</span></span> <span data-ttu-id="38d88-419">Zmienia się styl wiersza.</span><span class="sxs-lookup"><span data-stu-id="38d88-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="38d88-420">Dodawanie kursów prowadzonych przez wybranego instruktora</span><span class="sxs-lookup"><span data-stu-id="38d88-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="38d88-421">Zaktualizuj `OnGetAsync` metodę w *pages/instructors/index.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="38d88-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="38d88-422">Dodaj`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="38d88-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="38d88-423">Sprawdź zaktualizowaną kwerendę:</span><span class="sxs-lookup"><span data-stu-id="38d88-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="38d88-424">Poprzednia kwerenda `Department` dodaje jednostki.</span><span class="sxs-lookup"><span data-stu-id="38d88-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="38d88-425">Poniższy kod jest wykonywany po`id != null`wybraniu instruktora ( ).</span><span class="sxs-lookup"><span data-stu-id="38d88-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="38d88-426">Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="38d88-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="38d88-427">`Courses` Właściwość modelu widoku jest ładowana z `Course` jednostek z właściwości nawigacji tego instruktora. `CourseAssignments`</span><span class="sxs-lookup"><span data-stu-id="38d88-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="38d88-428">Metoda `Where` zwraca kolekcję.</span><span class="sxs-lookup"><span data-stu-id="38d88-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="38d88-429">W poprzedniej `Where` metodzie zwracana `Instructor` jest tylko pojedyncza jednostka.</span><span class="sxs-lookup"><span data-stu-id="38d88-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="38d88-430">Metoda `Single` konwertuje kolekcję `Instructor` na jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="38d88-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="38d88-431">Jednostka `Instructor` zapewnia dostęp `CourseAssignments` do właściwości.</span><span class="sxs-lookup"><span data-stu-id="38d88-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="38d88-432">`CourseAssignments`zapewnia dostęp do `Course` powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="38d88-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="38d88-434">Metoda `Single` jest używana w kolekcji, gdy kolekcja ma tylko jeden element.</span><span class="sxs-lookup"><span data-stu-id="38d88-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="38d88-435">Metoda `Single` zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element.</span><span class="sxs-lookup"><span data-stu-id="38d88-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="38d88-436">Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.</span><span class="sxs-lookup"><span data-stu-id="38d88-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="38d88-437">Korzystanie `SingleOrDefault` z pustej kolekcji:</span><span class="sxs-lookup"><span data-stu-id="38d88-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="38d88-438">Powoduje wyjątek (od próby `Courses` znalezienia właściwości na odwołanie null).</span><span class="sxs-lookup"><span data-stu-id="38d88-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="38d88-439">Komunikat o wyjątku będzie mniej wyraźnie wskazać przyczynę problemu.</span><span class="sxs-lookup"><span data-stu-id="38d88-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="38d88-440">Następujący kod wypełnia `Enrollments` właściwość modelu widoku po wybraniu kursu:</span><span class="sxs-lookup"><span data-stu-id="38d88-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="38d88-441">Dodaj następujące znaczniki na końcu *strony Pages/Instructors/Index.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="38d88-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="38d88-442">Poprzedni znaczników wyświetla listę kursów związanych z instruktorem, gdy instruktor jest wybrany.</span><span class="sxs-lookup"><span data-stu-id="38d88-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="38d88-443">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-443">Test the app.</span></span> <span data-ttu-id="38d88-444">Kliknij łącze **Wybierz** na stronie instruktorów.</span><span class="sxs-lookup"><span data-stu-id="38d88-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="38d88-445">Pokaż dane uczniów</span><span class="sxs-lookup"><span data-stu-id="38d88-445">Show student data</span></span>

<span data-ttu-id="38d88-446">W tej sekcji aplikacja jest aktualizowana, aby wyświetlić dane uczniów dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="38d88-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="38d88-447">Zaktualizuj `OnGetAsync` kwerendę w metodzie *Pages/Instructors/Index.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="38d88-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="38d88-448">Aktualizuj *strony/instruktorzy/index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="38d88-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="38d88-449">Dodaj następujące znaczniki na końcu pliku:</span><span class="sxs-lookup"><span data-stu-id="38d88-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="38d88-450">W poprzednim znaczniku wyświetlana jest lista uczniów, którzy zostali zapisani na wybrany kurs.</span><span class="sxs-lookup"><span data-stu-id="38d88-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="38d88-451">Odśwież stronę i wybierz instruktora.</span><span class="sxs-lookup"><span data-stu-id="38d88-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="38d88-452">Wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.</span><span class="sxs-lookup"><span data-stu-id="38d88-452">Select a course to see the list of enrolled students and their grades.</span></span>

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="38d88-454">Korzystanie z pojedynczego</span><span class="sxs-lookup"><span data-stu-id="38d88-454">Using Single</span></span>

<span data-ttu-id="38d88-455">Metoda `Single` może przejść `Where` w warunku `Where` zamiast wywoływania metody oddzielnie:</span><span class="sxs-lookup"><span data-stu-id="38d88-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="38d88-456">Powyższe `Single` podejście nie zapewnia `Where`żadnych korzyści w stosunku do korzystania .</span><span class="sxs-lookup"><span data-stu-id="38d88-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="38d88-457">Niektórzy deweloperzy `Single` preferują styl podejścia.</span><span class="sxs-lookup"><span data-stu-id="38d88-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="38d88-458">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="38d88-458">Explicit loading</span></span>

<span data-ttu-id="38d88-459">Bieżący kod określa wczesne `Enrollments` `Students`ładowanie i:</span><span class="sxs-lookup"><span data-stu-id="38d88-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="38d88-460">Załóżmy, że użytkownicy rzadko chcą zobaczyć rejestracje w trakcie.</span><span class="sxs-lookup"><span data-stu-id="38d88-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="38d88-461">W takim przypadku optymalizacja będzie tylko załadować dane rejestracji, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="38d88-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="38d88-462">W tej sekcji `OnGetAsync` jest aktualizowana w `Enrollments` `Students`celu użycia jawnego ładowania i .</span><span class="sxs-lookup"><span data-stu-id="38d88-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="38d88-463">Zaktualizuj `OnGetAsync` następujący kod:</span><span class="sxs-lookup"><span data-stu-id="38d88-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="38d88-464">Poprzedni kod porzuca *ThenInclude* metoda wywołuje rejestracji i danych studenta.</span><span class="sxs-lookup"><span data-stu-id="38d88-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="38d88-465">Jeśli kurs jest zaznaczony, wyróżniony kod pobiera:</span><span class="sxs-lookup"><span data-stu-id="38d88-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="38d88-466">Encje `Enrollment` dla wybranego kursu.</span><span class="sxs-lookup"><span data-stu-id="38d88-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="38d88-467">Jednostki `Student` dla `Enrollment`każdego .</span><span class="sxs-lookup"><span data-stu-id="38d88-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="38d88-468">Zwróć uwagę na powyższe komentarze do kodu `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="38d88-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="38d88-469">Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="38d88-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="38d88-470">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="38d88-470">Test the app.</span></span> <span data-ttu-id="38d88-471">Z punktu widzenia użytkowników aplikacja zachowuje się identycznie jak poprzednia wersja.</span><span class="sxs-lookup"><span data-stu-id="38d88-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="38d88-472">Następny samouczek pokazuje, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="38d88-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38d88-473">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="38d88-473">Additional resources</span></span>

* [<span data-ttu-id="38d88-474">Wersja YouTube tego samouczka (część1)</span><span class="sxs-lookup"><span data-stu-id="38d88-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="38d88-475">Wersja YouTube tego samouczka (część2)</span><span class="sxs-lookup"><span data-stu-id="38d88-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="38d88-476">[Poprzedni](xref:data/ef-rp/complex-data-model)
>[następny](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="38d88-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
