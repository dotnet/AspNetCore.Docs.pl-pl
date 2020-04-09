---
title: Strony brzytwy z EF Core w ASP.NET Core - Aktualizacja powiązanych danych - 7 z 8
author: rick-anderson
description: W tym samouczku można zaktualizować powiązane dane, aktualizując pola klucza obcego i właściwości nawigacji.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656423"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="83a9d-103">Strony brzytwy z EF Core w ASP.NET Core - Aktualizacja powiązanych danych - 7 z 8</span><span class="sxs-lookup"><span data-stu-id="83a9d-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="83a9d-104">Przez [Tom Dykstra](https://github.com/tdykstra)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83a9d-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83a9d-105">W tym samouczku pokazano, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="83a9d-106">Na poniższych ilustracjach przedstawiono niektóre ukończone strony.</span><span class="sxs-lookup"><span data-stu-id="83a9d-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="83a9d-107">![Strona edycji kursu](update-related-data/_static/course-edit30.png)
![Edycja instruktora](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="83a9d-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="83a9d-108">Aktualizowanie stron Tworzenie i edytowanie kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="83a9d-109">Szkieletowy kod dla stron Utwórz i Edytuj kurs ma listę rozwijaną Dział, która pokazuje identyfikator działu (liczba całkowita).</span><span class="sxs-lookup"><span data-stu-id="83a9d-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="83a9d-110">Lista rozwijana powinna być wyświetlana nazwę działu, więc obie te strony wymagają listy nazw działów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="83a9d-111">Aby udostępnić tę listę, należy użyć klasy podstawowej dla stron Tworzenie i edytowanie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="83a9d-112">Tworzenie klasy podstawowej dla tworzenia i edytowania kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="83a9d-113">Utwórz plik *Pages/Courses/DepartmentNamePageModel.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="83a9d-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="83a9d-114">Poprzedni kod tworzy [SelectList,](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) aby zawierać listę nazw działów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="83a9d-115">Jeśli `selectedDepartment` jest określony, ten dział `SelectList`jest zaznaczony w .</span><span class="sxs-lookup"><span data-stu-id="83a9d-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="83a9d-116">Klasy modelu strony Utwórz i `DepartmentNamePageModel`edytuj będą pochodzić z .</span><span class="sxs-lookup"><span data-stu-id="83a9d-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="83a9d-117">Aktualizowanie modelu strony Tworzenie kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-117">Update the Course Create page model</span></span>

<span data-ttu-id="83a9d-118">Kurs jest przypisany do działu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="83a9d-119">Klasa podstawowa stron Tworzenie i `SelectList` edytowanie umożliwia wybranie działu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="83a9d-120">Lista rozwijana, która używa `SelectList` właściwości `Course.DepartmentID` ustawia klucz obcy (FK).</span><span class="sxs-lookup"><span data-stu-id="83a9d-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="83a9d-121">EF Core używa `Course.DepartmentID` FK do `Department` załadowania właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Tworzenie kursu](update-related-data/_static/ddl30.png)

<span data-ttu-id="83a9d-123">Aktualizuj *strony/kursy/Create.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="83a9d-124">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="83a9d-124">The preceding code:</span></span>

* <span data-ttu-id="83a9d-125">Pochodzi z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="83a9d-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="83a9d-126">Służy `TryUpdateModelAsync` do zapobiegania [nadpostowaniu](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="83a9d-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="83a9d-127">Usuwa `ViewData["DepartmentID"]`plik .</span><span class="sxs-lookup"><span data-stu-id="83a9d-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="83a9d-128">`DepartmentNameSL`z klasy podstawowej jest modelem silnie typiwanym i będzie używany przez stronę Razor.</span><span class="sxs-lookup"><span data-stu-id="83a9d-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="83a9d-129">Silnie typizowane modele są preferowane niż słabo wpisane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="83a9d-130">Aby uzyskać więcej informacji, zobacz [Słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="83a9d-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="83a9d-131">Aktualizowanie strony Tworzenie brzytwy kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="83a9d-132">Zaktualizuj *strony/kursy/Create.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="83a9d-133">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83a9d-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="83a9d-134">Zmienia podpis z **DepartmentID** na **Dział**.</span><span class="sxs-lookup"><span data-stu-id="83a9d-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="83a9d-135">`"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).</span><span class="sxs-lookup"><span data-stu-id="83a9d-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="83a9d-136">Dodaje opcję "Wybierz dział".</span><span class="sxs-lookup"><span data-stu-id="83a9d-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="83a9d-137">Ta zmiana powoduje, że "Wybierz dział" w rozwijaniu, gdy nie wybrano jeszcze żadnego działu, a nie pierwszego działu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="83a9d-138">Dodaje komunikat sprawdzania poprawności, gdy dział nie jest zaznaczony.</span><span class="sxs-lookup"><span data-stu-id="83a9d-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="83a9d-139">Strona Razor używa [pomocnika wyboru znacznika:](xref:mvc/views/working-with-forms#the-select-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="83a9d-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="83a9d-140">Przetestuj stronę Utwórz.</span><span class="sxs-lookup"><span data-stu-id="83a9d-140">Test the Create page.</span></span> <span data-ttu-id="83a9d-141">Na stronie Utwórz wyświetlana jest nazwa działu, a nie identyfikator działu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="83a9d-142">Aktualizowanie modelu strony Edycji kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-142">Update the Course Edit page model</span></span>

<span data-ttu-id="83a9d-143">Zaktualizuj *strony/kursy/Edit.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="83a9d-144">Zmiany są podobne do tych wprowadzonych w modelu strony Tworzenie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="83a9d-145">W poprzednim kodzie `PopulateDepartmentsDropDownList` przekazuje identyfikator działu, który wybiera ten dział na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="83a9d-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="83a9d-146">Aktualizowanie strony Edycji Razora kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="83a9d-147">Zaktualizuj *strony/kursy/edit.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="83a9d-148">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83a9d-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="83a9d-149">Wyświetla identyfikator kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-149">Displays the course ID.</span></span> <span data-ttu-id="83a9d-150">Ogólnie rzecz biorąc klucz podstawowy (PK) jednostki nie jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="83a9d-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="83a9d-151">PKs są zwykle bez znaczenia dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83a9d-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="83a9d-152">W tym przypadku PK jest numerem kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="83a9d-153">Zmienia podpis listy rozwijanej Departament **z DepartmentID** na **Department**.</span><span class="sxs-lookup"><span data-stu-id="83a9d-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="83a9d-154">`"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).</span><span class="sxs-lookup"><span data-stu-id="83a9d-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="83a9d-155">Strona zawiera ukryte pole`<input type="hidden">`( ) dla numeru kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="83a9d-156">Dodanie `<label>` pomocnika tagu `asp-for="Course.CourseID"` nie eliminuje potrzeby korzystania z ukrytego pola.</span><span class="sxs-lookup"><span data-stu-id="83a9d-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="83a9d-157">`<input type="hidden">`jest wymagane, aby numer kursu został uwzględniony w opublikowanych danych, gdy użytkownik kliknie **przycisk Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="83a9d-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="83a9d-158">Aktualizowanie szczegółów kursu i usuwanie stron</span><span class="sxs-lookup"><span data-stu-id="83a9d-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="83a9d-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="83a9d-160">Aktualizowanie modeli stron kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-160">Update the Course page models</span></span>

<span data-ttu-id="83a9d-161">Aktualizuj *strony/kursy/usuń.cshtml.cs* z następującym `AsNoTracking`kodem do dodania:</span><span class="sxs-lookup"><span data-stu-id="83a9d-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="83a9d-162">Wprowadzać tę samą zmianę w pliku *Pages/Courses/Details.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="83a9d-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="83a9d-163">Aktualizowanie stron Razor kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-163">Update the Course Razor pages</span></span>

<span data-ttu-id="83a9d-164">Zaktualizuj *strony/kursy/usuń.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="83a9d-165">Wprowadzać te same zmiany na stronie Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="83a9d-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="83a9d-166">Testowanie stron kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-166">Test the Course pages</span></span>

<span data-ttu-id="83a9d-167">Przetestuj strony tworzenia, edytowania, szczegółów i usuwania.</span><span class="sxs-lookup"><span data-stu-id="83a9d-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="83a9d-168">Aktualizowanie stron tworzenia i edytowania instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="83a9d-169">Instruktorzy mogą nauczać dowolną liczbę kursów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="83a9d-170">Na poniższej ilustracji przedstawiono stronę edycji instruktora z tablicą pól wyboru kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="83a9d-172">Pola wyboru umożliwiają zmiany kursów, do które jest przypisany instruktor.</span><span class="sxs-lookup"><span data-stu-id="83a9d-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="83a9d-173">Pole wyboru jest wyświetlane dla każdego kursu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83a9d-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="83a9d-174">Wybrane są kursy przypisane do instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="83a9d-175">Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="83a9d-176">Jeśli liczba kursów była znacznie większa, inny interfejs użytkownika może działać lepiej.</span><span class="sxs-lookup"><span data-stu-id="83a9d-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="83a9d-177">Ale metoda zarządzania relacją wiele do wielu pokazana tutaj nie zmieni.</span><span class="sxs-lookup"><span data-stu-id="83a9d-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="83a9d-178">Aby utworzyć lub usunąć relacje, należy manipulować encją sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="83a9d-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="83a9d-179">Tworzenie klasy dla przypisanych danych kursów</span><span class="sxs-lookup"><span data-stu-id="83a9d-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="83a9d-180">Utwórz *schoolviewmodels/AssignedCourseData.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="83a9d-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="83a9d-181">Klasa `AssignedCourseData` zawiera dane do tworzenia pól wyboru dla kursów przypisanych do instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="83a9d-182">Tworzenie klasy podstawowej modelu strony instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="83a9d-183">Utwórz klasę podstawową *Pages/Instructors/InstructorCoursesPageModel.cs:*</span><span class="sxs-lookup"><span data-stu-id="83a9d-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="83a9d-184">Jest `InstructorCoursesPageModel` to klasa podstawowa, której użyjesz w modelach stron Edycja i Tworzenie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="83a9d-185">`PopulateAssignedCourseData`odczytuje `Course` wszystkie jednostki `AssignedCourseDataList`do zapełnienia .</span><span class="sxs-lookup"><span data-stu-id="83a9d-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="83a9d-186">Dla każdego kursu kod `CourseID`ustawia , tytuł i czy instruktor jest przypisany do kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="83a9d-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) jest używany do efektywnych odnośów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="83a9d-188">Ponieważ Razor strona nie ma kolekcji Course jednostek, spinacza modelu nie `CourseAssignments` można automatycznie zaktualizować właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="83a9d-189">Zamiast używać spinacza modelu, `CourseAssignments` aby zaktualizować właściwość nawigacji, `UpdateInstructorCourses` można to zrobić w nowej metodzie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="83a9d-190">W związku z tym `CourseAssignments` należy wykluczyć właściwość z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="83a9d-191">Nie wymaga to żadnych zmian w `TryUpdateModel` kodzie, który wywołuje, ponieważ używasz przeciążenia białej listy i `CourseAssignments` nie znajduje się na liście dołączania.</span><span class="sxs-lookup"><span data-stu-id="83a9d-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="83a9d-192">Jeśli nie wybrano żadnych `UpdateInstructorCourses` pól wyboru, kod inicjuje właściwość `CourseAssignments` nawigacji z pustą kolekcją i zwraca:</span><span class="sxs-lookup"><span data-stu-id="83a9d-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="83a9d-193">Kod następnie pętli przez wszystkie kursy w bazie danych i sprawdza każdy kurs względem tych aktualnie przypisanych do instruktora w porównaniu z tymi, które zostały wybrane na stronie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="83a9d-194">Aby ułatwić efektywne wyszukiwanie, dwie ostatnie `HashSet` kolekcje są przechowywane w obiektach.</span><span class="sxs-lookup"><span data-stu-id="83a9d-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="83a9d-195">Jeśli pole wyboru dla kursu zostało zaznaczone, ale `Instructor.CourseAssignments` kurs nie znajduje się we właściwości nawigacji, kurs jest dodawany do kolekcji we właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="83a9d-196">Jeśli pole wyboru kursu nie zostało zaznaczone, ale `Instructor.CourseAssignments` kurs znajduje się we właściwości nawigacji, kurs zostanie usunięty z właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="83a9d-197">Obsługa lokalizacji biura</span><span class="sxs-lookup"><span data-stu-id="83a9d-197">Handle office location</span></span>

<span data-ttu-id="83a9d-198">Inną relacją, którą strona edycji ma do obsługi, jest relacja jeden do `OfficeAssignment` zera lub jeden, która ma jednostkę Instructor z encją.</span><span class="sxs-lookup"><span data-stu-id="83a9d-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="83a9d-199">Kod edycji instruktora musi obsługiwać następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="83a9d-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="83a9d-200">Jeśli użytkownik wyczyści przypisanie biura, `OfficeAssignment` usuń encję.</span><span class="sxs-lookup"><span data-stu-id="83a9d-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="83a9d-201">Jeśli użytkownik wprowadzi przypisanie biura i jest puste, utwórz nową `OfficeAssignment` encję.</span><span class="sxs-lookup"><span data-stu-id="83a9d-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="83a9d-202">Jeśli użytkownik zmieni przypisanie biura, zaktualizuj encję. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="83a9d-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="83a9d-203">Aktualizowanie modelu strony Edycja instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="83a9d-204">Aktualizuj *strony/instruktorzy/edit.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="83a9d-205">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="83a9d-205">The preceding code:</span></span>

* <span data-ttu-id="83a9d-206">Pobiera `Instructor` bieżącą jednostkę z bazy `OfficeAssignment`danych `CourseAssignment`przy `CourseAssignment.Course` użyciu ładowania chętnych dla , i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="83a9d-207">Aktualizuje pobraną encję `Instructor` wartościami z spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="83a9d-208">`TryUpdateModel`zapobiega [nadpostowaniu](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="83a9d-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="83a9d-209">Jeśli lokalizacja biura jest `Instructor.OfficeAssignment` pusta, ustawia wartość null.</span><span class="sxs-lookup"><span data-stu-id="83a9d-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="83a9d-210">Gdy `Instructor.OfficeAssignment` wartość null jest zerowa, pokrewny wiersz w `OfficeAssignment` tabeli jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="83a9d-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="83a9d-211">`PopulateAssignedCourseData` Wywołania `OnGetAsync` w celu zapewnienia informacji o `AssignedCourseData` polach wyboru przy użyciu klasy modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="83a9d-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="83a9d-212">`UpdateInstructorCourses` Wywołania `OnPostAsync` w celu zastosowania informacji z pól wyboru do instructor jednostki edytowane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="83a9d-213">Połączenia `PopulateAssignedCourseData` `UpdateInstructorCourses` i `OnPostAsync` `TryUpdateModel` w przypadku niepowodzenia.</span><span class="sxs-lookup"><span data-stu-id="83a9d-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="83a9d-214">Te wywołania metody przywrócić przypisane dane kursu wprowadzone na stronie, gdy jest ponownie wyświetlany z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="83a9d-215">Aktualizowanie strony Edycja razora instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="83a9d-216">Aktualizuj *strony/instruktorzy/edit.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="83a9d-217">Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny.</span><span class="sxs-lookup"><span data-stu-id="83a9d-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="83a9d-218">Każda kolumna ma pole wyboru i podpis zawierający numer kursu i tytuł.</span><span class="sxs-lookup"><span data-stu-id="83a9d-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="83a9d-219">Wszystkie pola wyboru mają tę samą nazwę ("wybrane Cieki").</span><span class="sxs-lookup"><span data-stu-id="83a9d-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="83a9d-220">Przy użyciu tej samej nazwy informuje spinacza modelu, aby traktować je jako grupę.</span><span class="sxs-lookup"><span data-stu-id="83a9d-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="83a9d-221">Atrybut wartości każdego pola wyboru jest `CourseID`ustawiony na .</span><span class="sxs-lookup"><span data-stu-id="83a9d-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="83a9d-222">Gdy strona jest księgowane, spinacz modelu przekazuje `CourseID` tablicy, która składa się z wartości tylko dla pól wyboru, które są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="83a9d-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="83a9d-223">Gdy pola wyboru są początkowo renderowane, kursy przypisane do instruktora są wybierane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="83a9d-224">Uwaga: Podejście przyjęte tutaj w celu edycji danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="83a9d-225">Dla kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłoby bardziej użyteczne i wydajne.</span><span class="sxs-lookup"><span data-stu-id="83a9d-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="83a9d-226">Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="83a9d-227">Zmień niektóre zadania kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-227">Change some course assignments.</span></span> <span data-ttu-id="83a9d-228">Zmiany zostaną odzwierciedlone na stronie Indeks.</span><span class="sxs-lookup"><span data-stu-id="83a9d-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="83a9d-229">Aktualizowanie strony Tworzenie instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-229">Update the Instructor Create page</span></span>

<span data-ttu-id="83a9d-230">Zaktualizuj model strony Tworzenie instruktora i stronę Razor za pomocą kodu podobnego do strony Edytuj:</span><span class="sxs-lookup"><span data-stu-id="83a9d-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="83a9d-231">Przetestuj instruktora Utwórz stronę.</span><span class="sxs-lookup"><span data-stu-id="83a9d-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="83a9d-232">Aktualizowanie strony Usuwanie instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="83a9d-233">Aktualizuj *strony/instruktorzy/delete.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="83a9d-234">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83a9d-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="83a9d-235">Używa zaakusu dla właściwości `CourseAssignments` nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="83a9d-236">`CourseAssignments`muszą zostać uwzględnione lub nie zostaną usunięte po usunięciu instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="83a9d-237">Aby uniknąć konieczności ich odczytywania, należy skonfigurować usuwanie kaskadowe w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83a9d-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="83a9d-238">Jeśli instruktor do usunięcia jest przypisany jako administrator wszystkich działów, usuwa przypisanie instruktora z tych działów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="83a9d-239">Uruchom aplikację i przetestuj stronę Usuń.</span><span class="sxs-lookup"><span data-stu-id="83a9d-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="83a9d-240">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="83a9d-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="83a9d-241">[Poprzedni samouczek](xref:data/ef-rp/read-related-data)
> [Następny samouczek](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="83a9d-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83a9d-242">W tym samouczku pokazano aktualizowanie powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="83a9d-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="83a9d-243">Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="83a9d-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="83a9d-244">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="83a9d-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="83a9d-245">Na poniższych ilustracjach przedstawiono niektóre z ukończonych stron.</span><span class="sxs-lookup"><span data-stu-id="83a9d-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="83a9d-246">![Strona edycji kursu](update-related-data/_static/course-edit.png)
![Edycja instruktora](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="83a9d-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="83a9d-247">Umożliwia badanie i testowanie stron kursu Tworzenie i edytowanie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="83a9d-248">Utwórz nowy kurs.</span><span class="sxs-lookup"><span data-stu-id="83a9d-248">Create a new course.</span></span> <span data-ttu-id="83a9d-249">Dział jest wybierany przez jego klucz podstawowy (liczba całkowita), a nie jego nazwę.</span><span class="sxs-lookup"><span data-stu-id="83a9d-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="83a9d-250">Edytuj nowy kurs.</span><span class="sxs-lookup"><span data-stu-id="83a9d-250">Edit the new course.</span></span> <span data-ttu-id="83a9d-251">Po zakończeniu testowania usuń nowy kurs.</span><span class="sxs-lookup"><span data-stu-id="83a9d-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="83a9d-252">Tworzenie klasy podstawowej w celu udostępniania wspólnego kodu</span><span class="sxs-lookup"><span data-stu-id="83a9d-252">Create a base class to share common code</span></span>

<span data-ttu-id="83a9d-253">Kursy/Tworzenie i kursy/Edytuj strony wymagają listy nazw działów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="83a9d-254">Utwórz klasę podstawową *Pages/Courses/DepartmentNamePageModel.cs.cs* dla stron Tworzenie i edytowanie:</span><span class="sxs-lookup"><span data-stu-id="83a9d-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="83a9d-255">Poprzedni kod tworzy [SelectList,](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) aby zawierać listę nazw działów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="83a9d-256">Jeśli `selectedDepartment` jest określony, ten dział `SelectList`jest zaznaczony w .</span><span class="sxs-lookup"><span data-stu-id="83a9d-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="83a9d-257">Klasy modelu strony Utwórz i `DepartmentNamePageModel`edytuj będą pochodzić z .</span><span class="sxs-lookup"><span data-stu-id="83a9d-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="83a9d-258">Dostosowywanie stron kursów</span><span class="sxs-lookup"><span data-stu-id="83a9d-258">Customize the Courses Pages</span></span>

<span data-ttu-id="83a9d-259">Po utworzeniu nowej jednostki kursu musi mieć relację z istniejącym działem.</span><span class="sxs-lookup"><span data-stu-id="83a9d-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="83a9d-260">Aby dodać dział podczas tworzenia kursu, klasa podstawowa dla tworzenia i edytowania zawiera listę rozwijaną do wybierania działu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="83a9d-261">Lista rozwijana ustawia `Course.DepartmentID` właściwość klucza obcego (FK).</span><span class="sxs-lookup"><span data-stu-id="83a9d-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="83a9d-262">EF Core używa `Course.DepartmentID` FK do `Department` załadowania właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Tworzenie kursu](update-related-data/_static/ddl.png)

<span data-ttu-id="83a9d-264">Zaktualizuj model strony Utwórz za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="83a9d-265">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="83a9d-265">The preceding code:</span></span>

* <span data-ttu-id="83a9d-266">Pochodzi z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="83a9d-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="83a9d-267">Służy `TryUpdateModelAsync` do zapobiegania [nadpostowaniu](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="83a9d-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="83a9d-268">`ViewData["DepartmentID"]` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).</span><span class="sxs-lookup"><span data-stu-id="83a9d-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="83a9d-269">`ViewData["DepartmentID"]`zastępuje się silnie wpisanym `DepartmentNameSL`.</span><span class="sxs-lookup"><span data-stu-id="83a9d-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="83a9d-270">Silnie typizowane modele są preferowane niż słabo wpisane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="83a9d-271">Aby uzyskać więcej informacji, zobacz [Słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="83a9d-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="83a9d-272">Aktualizowanie strony Tworzenie kursów</span><span class="sxs-lookup"><span data-stu-id="83a9d-272">Update the Courses Create page</span></span>

<span data-ttu-id="83a9d-273">Zaktualizuj *strony/kursy/Create.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83a9d-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="83a9d-274">Poprzedni znacznik wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83a9d-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="83a9d-275">Zmienia podpis z **DepartmentID** na **Dział**.</span><span class="sxs-lookup"><span data-stu-id="83a9d-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="83a9d-276">`"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).</span><span class="sxs-lookup"><span data-stu-id="83a9d-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="83a9d-277">Dodaje opcję "Wybierz dział".</span><span class="sxs-lookup"><span data-stu-id="83a9d-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="83a9d-278">Ta zmiana powoduje, że "Wybierz dział", a nie pierwszy dział.</span><span class="sxs-lookup"><span data-stu-id="83a9d-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="83a9d-279">Dodaje komunikat sprawdzania poprawności, gdy dział nie jest zaznaczony.</span><span class="sxs-lookup"><span data-stu-id="83a9d-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="83a9d-280">Strona Razor używa [pomocnika wyboru znacznika:](xref:mvc/views/working-with-forms#the-select-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="83a9d-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="83a9d-281">Przetestuj stronę Utwórz.</span><span class="sxs-lookup"><span data-stu-id="83a9d-281">Test the Create page.</span></span> <span data-ttu-id="83a9d-282">Na stronie Utwórz wyświetlana jest nazwa działu, a nie identyfikator działu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="83a9d-283">Zaktualizuj stronę Edycja kursów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="83a9d-284">Zastąp kod w *pages/courses/edit.cshtml.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="83a9d-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="83a9d-285">Zmiany są podobne do tych wprowadzonych w modelu strony Tworzenie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="83a9d-286">W poprzednim kodzie `PopulateDepartmentsDropDownList` przekazuje w identyfikatorze działu, który wybiera dział określony na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="83a9d-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="83a9d-287">Aktualizuj *strony/kursy/edit.cshtml* za pomocą następujących znaczników:</span><span class="sxs-lookup"><span data-stu-id="83a9d-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="83a9d-288">Poprzedni znacznik wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83a9d-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="83a9d-289">Wyświetla identyfikator kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-289">Displays the course ID.</span></span> <span data-ttu-id="83a9d-290">Ogólnie rzecz biorąc klucz podstawowy (PK) jednostki nie jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="83a9d-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="83a9d-291">PKs są zwykle bez znaczenia dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83a9d-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="83a9d-292">W tym przypadku PK jest numerem kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="83a9d-293">Zmienia podpis z **DepartmentID** na **Dział**.</span><span class="sxs-lookup"><span data-stu-id="83a9d-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="83a9d-294">`"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).</span><span class="sxs-lookup"><span data-stu-id="83a9d-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="83a9d-295">Strona zawiera ukryte pole`<input type="hidden">`( ) dla numeru kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="83a9d-296">Dodanie `<label>` pomocnika tagu `asp-for="Course.CourseID"` nie eliminuje potrzeby korzystania z ukrytego pola.</span><span class="sxs-lookup"><span data-stu-id="83a9d-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="83a9d-297">`<input type="hidden">`jest wymagane, aby numer kursu został uwzględniony w opublikowanych danych, gdy użytkownik kliknie **przycisk Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="83a9d-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="83a9d-298">Przetestuj zaktualizowany kod.</span><span class="sxs-lookup"><span data-stu-id="83a9d-298">Test the updated code.</span></span> <span data-ttu-id="83a9d-299">Tworzenie, edytowanie i usuwanie kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="83a9d-300">Dodawanie AsNoTracking do modeli stron Szczegóły i Usuwanie</span><span class="sxs-lookup"><span data-stu-id="83a9d-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="83a9d-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="83a9d-302">Dodaj `AsNoTracking` do modelu strony Usuń i Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="83a9d-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="83a9d-303">Poniższy kod przedstawia zaktualizowany model strony Usuwania:</span><span class="sxs-lookup"><span data-stu-id="83a9d-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="83a9d-304">Zaktualizuj `OnGetAsync` metodę w pliku *Pages/Courses/Details.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="83a9d-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="83a9d-305">Modyfikowanie stron Usuwanie i Szczegóły</span><span class="sxs-lookup"><span data-stu-id="83a9d-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="83a9d-306">Zaktualizuj stronę Usuń maszynkę do golenia następującymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="83a9d-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="83a9d-307">Wprowadzać te same zmiany na stronie Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="83a9d-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="83a9d-308">Testowanie stron kursu</span><span class="sxs-lookup"><span data-stu-id="83a9d-308">Test the Course pages</span></span>

<span data-ttu-id="83a9d-309">Przetestuj tworzenie, edytowanie, szczegóły i usuwanie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="83a9d-310">Aktualizowanie stron instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-310">Update the instructor pages</span></span>

<span data-ttu-id="83a9d-311">W poniższych sekcjach zaktualizowano strony instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="83a9d-312">Dodawanie lokalizacji biura</span><span class="sxs-lookup"><span data-stu-id="83a9d-312">Add office location</span></span>

<span data-ttu-id="83a9d-313">Podczas edytowania rekordu instruktora można zaktualizować przypisanie biura instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="83a9d-314">Jednostka `Instructor` ma relację jeden do zera lub jeden `OfficeAssignment` z jednostką.</span><span class="sxs-lookup"><span data-stu-id="83a9d-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="83a9d-315">Kod instruktora musi obsługiwać:</span><span class="sxs-lookup"><span data-stu-id="83a9d-315">The instructor code must handle:</span></span>

* <span data-ttu-id="83a9d-316">Jeśli użytkownik wyczyści przypisanie biura, `OfficeAssignment` usuń encję.</span><span class="sxs-lookup"><span data-stu-id="83a9d-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="83a9d-317">Jeśli użytkownik wprowadzi przypisanie biura i jest puste, utwórz nową `OfficeAssignment` encję.</span><span class="sxs-lookup"><span data-stu-id="83a9d-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="83a9d-318">Jeśli użytkownik zmieni przypisanie biura, zaktualizuj encję. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="83a9d-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="83a9d-319">Zaktualizuj instruktorów Edytuj model strony o następujący kod:</span><span class="sxs-lookup"><span data-stu-id="83a9d-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="83a9d-320">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="83a9d-320">The preceding code:</span></span>

* <span data-ttu-id="83a9d-321">Pobiera bieżącej `Instructor` jednostki z bazy `OfficeAssignment` danych przy użyciu ładowania eager dla właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="83a9d-322">Aktualizuje pobraną encję `Instructor` wartościami z spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="83a9d-323">`TryUpdateModel`zapobiega [nadpostowaniu](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="83a9d-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="83a9d-324">Jeśli lokalizacja biura jest `Instructor.OfficeAssignment` pusta, ustawia wartość null.</span><span class="sxs-lookup"><span data-stu-id="83a9d-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="83a9d-325">Gdy `Instructor.OfficeAssignment` wartość null jest zerowa, pokrewny wiersz w `OfficeAssignment` tabeli jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="83a9d-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="83a9d-326">Aktualizowanie strony edycji instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-326">Update the instructor Edit page</span></span>

<span data-ttu-id="83a9d-327">Aktualizuj *strony/instruktorzy/edit.cshtml* z lokalizacją biura:</span><span class="sxs-lookup"><span data-stu-id="83a9d-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="83a9d-328">Sprawdź, czy możesz zmienić lokalizację biura instruktorów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="83a9d-329">Dodawanie przypisań kursu do strony Edytowanie instruktora</span><span class="sxs-lookup"><span data-stu-id="83a9d-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="83a9d-330">Instruktorzy mogą nauczać dowolną liczbę kursów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="83a9d-331">W tej sekcji można dodać możliwość zmiany przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="83a9d-332">Na poniższej ilustracji przedstawiono zaktualizowaną stronę edycji instruktora:</span><span class="sxs-lookup"><span data-stu-id="83a9d-332">The following image shows the updated instructor Edit page:</span></span>

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="83a9d-334">`Course`i `Instructor` ma wiele do wielu relacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="83a9d-335">Aby dodać i usunąć relacje, należy dodać `CourseAssignments` i usunąć encje z zestawu encji sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="83a9d-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="83a9d-336">Pola wyboru umożliwiają zmiany kursów przypisanych przez instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="83a9d-337">Pole wyboru jest wyświetlane dla każdego kursu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83a9d-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="83a9d-338">Kursy, do których jest przypisany instruktor, są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="83a9d-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="83a9d-339">Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="83a9d-340">Jeśli liczba kursów była znacznie większa:</span><span class="sxs-lookup"><span data-stu-id="83a9d-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="83a9d-341">Prawdopodobnie użyjesz innego interfejsu użytkownika, aby wyświetlić kursy.</span><span class="sxs-lookup"><span data-stu-id="83a9d-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="83a9d-342">Metoda manipulowania jednostką sprzężenia w celu tworzenia lub usuwania relacji nie ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="83a9d-343">Dodawanie klas do obsługi stron instruktora tworzenia i edytowania</span><span class="sxs-lookup"><span data-stu-id="83a9d-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="83a9d-344">Utwórz *schoolviewmodels/AssignedCourseData.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="83a9d-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="83a9d-345">Klasa `AssignedCourseData` zawiera dane do tworzenia pól wyboru dla przypisanych kursów przez instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="83a9d-346">Utwórz klasę podstawową *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="83a9d-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="83a9d-347">Jest `InstructorCoursesPageModel` to klasa podstawowa, której użyjesz w modelach stron Edycja i Tworzenie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="83a9d-348">`PopulateAssignedCourseData`odczytuje `Course` wszystkie jednostki `AssignedCourseDataList`do zapełnienia .</span><span class="sxs-lookup"><span data-stu-id="83a9d-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="83a9d-349">Dla każdego kursu kod `CourseID`ustawia , tytuł i czy instruktor jest przypisany do kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="83a9d-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) służy do tworzenia efektywnych odnośów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="83a9d-351">Instruktorzy Edytuj model strony</span><span class="sxs-lookup"><span data-stu-id="83a9d-351">Instructors Edit page model</span></span>

<span data-ttu-id="83a9d-352">Zaktualizuj model strony edycji instruktora o następujący kod:</span><span class="sxs-lookup"><span data-stu-id="83a9d-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="83a9d-353">Poprzedni kod obsługuje zmiany przypisania biura.</span><span class="sxs-lookup"><span data-stu-id="83a9d-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="83a9d-354">Zaktualizuj instruktora Razor View:</span><span class="sxs-lookup"><span data-stu-id="83a9d-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="83a9d-355">Po wklejeniu kodu w programie Visual Studio podziały wierszy są zmieniane w sposób, który przerywa kod.</span><span class="sxs-lookup"><span data-stu-id="83a9d-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="83a9d-356">Naciśnij jednokrotnie klawisze Ctrl+Z, aby cofnąć automatyczne formatowanie.</span><span class="sxs-lookup"><span data-stu-id="83a9d-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="83a9d-357">Ctrl+Z naprawia podziały wierszy tak, aby wyglądały jak to, co widzisz tutaj.</span><span class="sxs-lookup"><span data-stu-id="83a9d-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="83a9d-358">Wcięcie nie musi być doskonałe, ale `@:</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` i linie muszą być w jednej linii, jak pokazano.</span><span class="sxs-lookup"><span data-stu-id="83a9d-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="83a9d-359">Po wybraniu bloku nowego kodu naciśnij trzykrotnie klawisz Tab, aby wyrównać nowy kod z istniejącym kodem.</span><span class="sxs-lookup"><span data-stu-id="83a9d-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="83a9d-360">Zagłosuj lub przejrzyj stan tego błędu [za pomocą tego linku](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="83a9d-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="83a9d-361">Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny.</span><span class="sxs-lookup"><span data-stu-id="83a9d-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="83a9d-362">Każda kolumna ma pole wyboru i podpis zawierający numer kursu i tytuł.</span><span class="sxs-lookup"><span data-stu-id="83a9d-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="83a9d-363">Wszystkie pola wyboru mają tę samą nazwę ("wybrane Cieki").</span><span class="sxs-lookup"><span data-stu-id="83a9d-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="83a9d-364">Przy użyciu tej samej nazwy informuje spinacza modelu, aby traktować je jako grupę.</span><span class="sxs-lookup"><span data-stu-id="83a9d-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="83a9d-365">Atrybut wartości każdego pola wyboru jest `CourseID`ustawiony na .</span><span class="sxs-lookup"><span data-stu-id="83a9d-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="83a9d-366">Gdy strona jest księgowane, spinacz modelu przekazuje `CourseID` tablicy, która składa się z wartości tylko dla pól wyboru, które są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="83a9d-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="83a9d-367">Gdy pola wyboru są początkowo renderowane, kursy przypisane do instruktora mają sprawdzone atrybuty.</span><span class="sxs-lookup"><span data-stu-id="83a9d-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="83a9d-368">Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="83a9d-369">Zmień niektóre zadania kursu.</span><span class="sxs-lookup"><span data-stu-id="83a9d-369">Change some course assignments.</span></span> <span data-ttu-id="83a9d-370">Zmiany zostaną odzwierciedlone na stronie Indeks.</span><span class="sxs-lookup"><span data-stu-id="83a9d-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="83a9d-371">Uwaga: Podejście przyjęte tutaj w celu edycji danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="83a9d-372">Dla kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłoby bardziej użyteczne i wydajne.</span><span class="sxs-lookup"><span data-stu-id="83a9d-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="83a9d-373">Aktualizowanie strony Tworzenie instruktorów</span><span class="sxs-lookup"><span data-stu-id="83a9d-373">Update the instructors Create page</span></span>

<span data-ttu-id="83a9d-374">Zaktualizuj instruktora Utwórz model strony o następujący kod:</span><span class="sxs-lookup"><span data-stu-id="83a9d-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="83a9d-375">Powyższy kod jest podobny do kodu *Pages/Instructors/Edit.cshtml.cs.*</span><span class="sxs-lookup"><span data-stu-id="83a9d-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="83a9d-376">Zaktualizuj stronę utwórz maszynkę do golenia instruktorem za pomocą następujących znaczników:</span><span class="sxs-lookup"><span data-stu-id="83a9d-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="83a9d-377">Przetestuj instruktora Utwórz stronę.</span><span class="sxs-lookup"><span data-stu-id="83a9d-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="83a9d-378">Aktualizowanie strony Usuwanie</span><span class="sxs-lookup"><span data-stu-id="83a9d-378">Update the Delete page</span></span>

<span data-ttu-id="83a9d-379">Zaktualizuj model strony Usuń o następujący kod:</span><span class="sxs-lookup"><span data-stu-id="83a9d-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="83a9d-380">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83a9d-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="83a9d-381">Używa zaakusu dla właściwości `CourseAssignments` nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83a9d-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="83a9d-382">`CourseAssignments`muszą zostać uwzględnione lub nie zostaną usunięte po usunięciu instruktora.</span><span class="sxs-lookup"><span data-stu-id="83a9d-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="83a9d-383">Aby uniknąć konieczności ich odczytywania, należy skonfigurować usuwanie kaskadowe w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83a9d-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="83a9d-384">Jeśli instruktor do usunięcia jest przypisany jako administrator wszystkich działów, usuwa przypisanie instruktora z tych działów.</span><span class="sxs-lookup"><span data-stu-id="83a9d-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83a9d-385">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="83a9d-385">Additional resources</span></span>

* [<span data-ttu-id="83a9d-386">Wersja YouTube tego samouczka (część 1)</span><span class="sxs-lookup"><span data-stu-id="83a9d-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="83a9d-387">Wersja YouTube tego samouczka (część 2)</span><span class="sxs-lookup"><span data-stu-id="83a9d-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="83a9d-388">[Poprzedni](xref:data/ef-rp/read-related-data)
> [następny](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="83a9d-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
