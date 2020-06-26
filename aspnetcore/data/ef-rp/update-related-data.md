---
title: Część 7 Razor strony z EF Core w programie ASP.NET Core — aktualizacja powiązanych danych
author: rick-anderson
description: Część 7 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b084404a7fdd8b2fc18c407bd11705ccd1c496c1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406306"
---
# <a name="part-7-razor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="1525e-103">Część 7 Razor strony z EF Core w programie ASP.NET Core — aktualizacja powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="1525e-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="1525e-104">Przez [Tomasz Dykstra](https://github.com/tdykstra)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1525e-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1525e-105">W tym samouczku pokazano, jak zaktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1525e-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="1525e-106">Na poniższych ilustracjach przedstawiono niektóre z ukończonych stron.</span><span class="sxs-lookup"><span data-stu-id="1525e-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="1525e-107">![Strona edycji kursów — Edytuj ](update-related-data/_static/course-edit30.png)
 ![ stronę](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="1525e-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="1525e-108">Aktualizowanie stron tworzenie i edytowanie kursu</span><span class="sxs-lookup"><span data-stu-id="1525e-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="1525e-109">Kod szkieletowy dla stron tworzenie i edytowanie danych zawiera listę rozwijaną dział, która zawiera identyfikator działu (liczba całkowita).</span><span class="sxs-lookup"><span data-stu-id="1525e-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="1525e-110">Lista rozwijana powinna zawierać nazwę działu, więc obie te strony muszą mieć listę nazw działów.</span><span class="sxs-lookup"><span data-stu-id="1525e-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="1525e-111">Aby zapewnić tę listę, użyj klasy bazowej dla stron tworzenia i edytowania.</span><span class="sxs-lookup"><span data-stu-id="1525e-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="1525e-112">Tworzenie klasy bazowej do tworzenia i edytowania kursu</span><span class="sxs-lookup"><span data-stu-id="1525e-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="1525e-113">Utwórz plik *Pages/kurss/DepartmentNamePageModel. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="1525e-114">Poprzedni kod tworzy [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , aby zawierał listę nazw działów.</span><span class="sxs-lookup"><span data-stu-id="1525e-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="1525e-115">Jeśli `selectedDepartment` jest określony, ten dział jest wybierany w `SelectList` .</span><span class="sxs-lookup"><span data-stu-id="1525e-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="1525e-116">Klasy Utwórz i edytuj model strony będą pochodzić od `DepartmentNamePageModel` .</span><span class="sxs-lookup"><span data-stu-id="1525e-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="1525e-117">Aktualizuj model tworzenia strony kursu</span><span class="sxs-lookup"><span data-stu-id="1525e-117">Update the Course Create page model</span></span>

<span data-ttu-id="1525e-118">Kurs jest przypisywany do działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="1525e-119">Klasa bazowa dla stron tworzenia i edytowania umożliwia `SelectList` wybranie działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="1525e-120">Lista rozwijana korzystająca z `SelectList` `Course.DepartmentID` właściwości ustawia klucz obcy (FK).</span><span class="sxs-lookup"><span data-stu-id="1525e-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="1525e-121">EF Core używa `Course.DepartmentID` klucza obcego do załadowania `Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Utwórz kurs](update-related-data/_static/ddl30.png)

<span data-ttu-id="1525e-123">Zaktualizuj *strony/kursy/Utwórz. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1525e-124">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="1525e-124">The preceding code:</span></span>

* <span data-ttu-id="1525e-125">Pochodzi od `DepartmentNamePageModel` .</span><span class="sxs-lookup"><span data-stu-id="1525e-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="1525e-126">Używa `TryUpdateModelAsync` do zapobiegania [overposting](xref:data/ef-rp/crud#overposting)przepisywaniu.</span><span class="sxs-lookup"><span data-stu-id="1525e-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="1525e-127">Usuwa `ViewData["DepartmentID"]` .</span><span class="sxs-lookup"><span data-stu-id="1525e-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="1525e-128">`DepartmentNameSL`z klasy podstawowej jest jednoznacznie określonym modelem i będzie używany przez Razor stronę.</span><span class="sxs-lookup"><span data-stu-id="1525e-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="1525e-129">Modele silnie wpisane są preferowane za pośrednictwem słabo wpisanych.</span><span class="sxs-lookup"><span data-stu-id="1525e-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="1525e-130">Aby uzyskać więcej informacji, zobacz [słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="1525e-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="1525e-131">Aktualizowanie strony tworzenia kursu Razor</span><span class="sxs-lookup"><span data-stu-id="1525e-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="1525e-132">Zaktualizuj *strony/kursy/Utwórz. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="1525e-133">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1525e-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1525e-134">Zmienia podpis z **DepartmentID** na **dział**.</span><span class="sxs-lookup"><span data-stu-id="1525e-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="1525e-135">Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).</span><span class="sxs-lookup"><span data-stu-id="1525e-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="1525e-136">Dodaje opcję "Wybierz dział".</span><span class="sxs-lookup"><span data-stu-id="1525e-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="1525e-137">Ta zmiana renderuje "Select Department" na liście rozwijanej, gdy nie wybrano jeszcze żadnego działu, a nie pierwszego działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="1525e-138">Dodaje komunikat weryfikacyjny, gdy nie wybrano działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="1525e-139">RazorStrona używa [pomocnika wybierania tagu](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="1525e-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="1525e-140">Przetestuj stronę tworzenie.</span><span class="sxs-lookup"><span data-stu-id="1525e-140">Test the Create page.</span></span> <span data-ttu-id="1525e-141">Na stronie Tworzenie zostanie wyświetlona nazwa działu, a nie identyfikator działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="1525e-142">Aktualizowanie modelu strony edytowania kursu</span><span class="sxs-lookup"><span data-stu-id="1525e-142">Update the Course Edit page model</span></span>

<span data-ttu-id="1525e-143">Zaktualizuj *strony/kursy/Edytuj. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="1525e-144">Zmiany są podobne do tych, które zostały wprowadzone w modelu tworzenia strony.</span><span class="sxs-lookup"><span data-stu-id="1525e-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="1525e-145">W poprzednim kodzie program `PopulateDepartmentsDropDownList` przekazuje identyfikator działu, który wybiera ten dział z listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="1525e-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="1525e-146">Aktualizowanie strony edytowania kursu Razor</span><span class="sxs-lookup"><span data-stu-id="1525e-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="1525e-147">Aktualizowanie *stron/kursów/Edit. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="1525e-148">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1525e-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1525e-149">Wyświetla identyfikator kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-149">Displays the course ID.</span></span> <span data-ttu-id="1525e-150">Zazwyczaj klucz podstawowy (PK) jednostki nie jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="1525e-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="1525e-151">PKs są zwykle oznaczane przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1525e-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="1525e-152">W tym przypadku klucz podstawowy jest numerem kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="1525e-153">Zmienia podpis dla listy rozwijanej działu od **DepartmentID** do **działu**.</span><span class="sxs-lookup"><span data-stu-id="1525e-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="1525e-154">Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).</span><span class="sxs-lookup"><span data-stu-id="1525e-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="1525e-155">Ta strona zawiera ukryte pole ( `<input type="hidden">` ) dla numeru kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="1525e-156">Dodanie `<label>` pomocnika tagów z `asp-for="Course.CourseID"` nie eliminuje potrzeby pola ukrytego.</span><span class="sxs-lookup"><span data-stu-id="1525e-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="1525e-157">`<input type="hidden">`jest wymagana do uwzględnienia numeru kursu w opublikowanych danych, gdy użytkownik kliknie przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1525e-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="1525e-158">Aktualizowanie szczegółów kursu i stron usuwania</span><span class="sxs-lookup"><span data-stu-id="1525e-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="1525e-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="1525e-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="1525e-160">Aktualizowanie modeli stron kursu</span><span class="sxs-lookup"><span data-stu-id="1525e-160">Update the Course page models</span></span>

<span data-ttu-id="1525e-161">Zaktualizuj *strony/kursy/Delete. cshtml. cs* przy użyciu następującego kodu do dodania `AsNoTracking` :</span><span class="sxs-lookup"><span data-stu-id="1525e-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="1525e-162">Wprowadź tę samą zmianę w pliku *Pages/kurss/details. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="1525e-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="1525e-163">Aktualizowanie stron kursu Razor</span><span class="sxs-lookup"><span data-stu-id="1525e-163">Update the Course Razor pages</span></span>

<span data-ttu-id="1525e-164">Zaktualizuj *strony/kursy/Delete. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="1525e-165">Wprowadź te same zmiany na stronie Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="1525e-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="1525e-166">Testowanie stron kursów</span><span class="sxs-lookup"><span data-stu-id="1525e-166">Test the Course pages</span></span>

<span data-ttu-id="1525e-167">Przetestuj strony tworzenie, edytowanie, szczegóły i usuwanie.</span><span class="sxs-lookup"><span data-stu-id="1525e-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="1525e-168">Aktualizowanie strony instruktora tworzenie i edytowanie stron</span><span class="sxs-lookup"><span data-stu-id="1525e-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="1525e-169">Instruktorzy mogą uczyć się dowolnej liczby kursów.</span><span class="sxs-lookup"><span data-stu-id="1525e-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="1525e-170">Na poniższej ilustracji przedstawiono stronę Edytowanie instruktora z tablicą pól wyboru kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Instruktor strony edytowania za pomocą kursów](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="1525e-172">Pola wyboru umożliwiają zmianę kursów, do których zostanie przypisany instruktor.</span><span class="sxs-lookup"><span data-stu-id="1525e-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="1525e-173">Pole wyboru jest wyświetlane dla każdego kursu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="1525e-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="1525e-174">Wybrane są kursy, do których przypisano instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="1525e-175">Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="1525e-176">Jeśli liczba kursów była znacznie większa, może to poprawić inny interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1525e-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="1525e-177">Jednak metoda zarządzania relacją wiele-do-wielu pokazana tutaj nie zmieniła się.</span><span class="sxs-lookup"><span data-stu-id="1525e-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="1525e-178">Aby utworzyć lub usunąć relacje, można manipulować jednostką sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="1525e-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="1525e-179">Utwórz klasę dla danych przypisanych kursów</span><span class="sxs-lookup"><span data-stu-id="1525e-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="1525e-180">Utwórz *SchoolViewModels/AssignedCourseData. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="1525e-181">`AssignedCourseData`Klasa zawiera dane, aby utworzyć pola wyboru dla kursów przypisanych do instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="1525e-182">Utwórz klasę bazową modelu strony instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="1525e-183">Utwórz klasę bazową *stron/instruktorów/InstructorCoursesPageModel. cs* :</span><span class="sxs-lookup"><span data-stu-id="1525e-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="1525e-184">`InstructorCoursesPageModel`Jest klasą bazową, która będzie używana dla modeli stron Edycja i tworzenie.</span><span class="sxs-lookup"><span data-stu-id="1525e-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="1525e-185">`PopulateAssignedCourseData`odczytuje wszystkie `Course` jednostki do wypełnienia `AssignedCourseDataList` .</span><span class="sxs-lookup"><span data-stu-id="1525e-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="1525e-186">Dla każdego kursu kod ustawia `CourseID` , tytuł i określa, czy instruktor jest przypisany do kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="1525e-187">[HashSet —](/dotnet/api/system.collections.generic.hashset-1) jest używany do wydajnego wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1525e-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="1525e-188">Ponieważ Razor Strona nie zawiera kolekcji jednostek kursu, spinacz modelu nie może automatycznie zaktualizować `CourseAssignments` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="1525e-189">Zamiast używać spinacza modelu do aktualizowania `CourseAssignments` właściwości nawigacji, należy to zrobić w nowej `UpdateInstructorCourses` metodzie.</span><span class="sxs-lookup"><span data-stu-id="1525e-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="1525e-190">W związku z tym należy wykluczyć `CourseAssignments` Właściwość z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="1525e-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="1525e-191">Nie wymaga żadnych zmian w kodzie, który wywołuje się, `TryUpdateModel` ponieważ jest używane Przeciążenie listy dozwolonych i `CourseAssignments` nie znajduje się na liście dołączania.</span><span class="sxs-lookup"><span data-stu-id="1525e-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="1525e-192">Jeśli nie wybrano żadnych pól wyboru, kod w `UpdateInstructorCourses` inicjuje `CourseAssignments` Właściwość nawigacji z pustą kolekcją i zwraca:</span><span class="sxs-lookup"><span data-stu-id="1525e-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="1525e-193">Kod następnie przechodzi między wszystkimi kursami w bazie danych i sprawdza każdy kurs w odniesieniu do tych, które są aktualnie przypisane do instruktora, a także do tych, które zostały wybrane na stronie.</span><span class="sxs-lookup"><span data-stu-id="1525e-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="1525e-194">Aby ułatwić efektywne wyszukiwanie, te dwie kolekcje są przechowywane w `HashSet` obiektach.</span><span class="sxs-lookup"><span data-stu-id="1525e-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="1525e-195">Jeśli pole wyboru dla kursu zostało zaznaczone, ale kurs nie jest we `Instructor.CourseAssignments` właściwości nawigacji, kurs zostanie dodany do kolekcji we właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="1525e-196">Jeśli nie wybrano pola wyboru dla kursu, ale kurs jest we `Instructor.CourseAssignments` właściwości nawigacji, kurs jest usuwany z właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="1525e-197">Obsługa lokalizacji biura</span><span class="sxs-lookup"><span data-stu-id="1525e-197">Handle office location</span></span>

<span data-ttu-id="1525e-198">Inna relacja, którą Strona Edytuj musi obsłużyć, jest relacją "jeden do zera" lub "jeden", którą jednostka instruktora ma z `OfficeAssignment` jednostką.</span><span class="sxs-lookup"><span data-stu-id="1525e-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="1525e-199">Program instruktora edytuje kod musi obsługiwać następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="1525e-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="1525e-200">Jeśli użytkownik wyczyści przypisanie pakietu Office, Usuń `OfficeAssignment` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1525e-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="1525e-201">Jeśli użytkownik wprowadzi przypisanie do pakietu Office i jest puste, należy utworzyć nową `OfficeAssignment` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1525e-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="1525e-202">Jeśli użytkownik zmieni przypisanie pakietu Office, zaktualizuj `OfficeAssignment` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1525e-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="1525e-203">Aktualizowanie modelu strony przez instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="1525e-204">Aktualizowanie *stron/instruktorów/Edit. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="1525e-205">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="1525e-205">The preceding code:</span></span>

* <span data-ttu-id="1525e-206">Pobiera bieżącą `Instructor` jednostkę z bazy danych przy użyciu eager ładowania dla `OfficeAssignment` `CourseAssignment` właściwości nawigacji, i `CourseAssignment.Course` .</span><span class="sxs-lookup"><span data-stu-id="1525e-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="1525e-207">Aktualizuje pobraną `Instructor` jednostkę z wartościami ze spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="1525e-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="1525e-208">`TryUpdateModel`zapobiega [overposting](xref:data/ef-rp/crud#overposting)zastępowaniu.</span><span class="sxs-lookup"><span data-stu-id="1525e-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="1525e-209">Jeśli lokalizacja biura jest pusta, ustawia `Instructor.OfficeAssignment` wartość na null.</span><span class="sxs-lookup"><span data-stu-id="1525e-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="1525e-210">Gdy `Instructor.OfficeAssignment` ma wartość null, powiązany wiersz w `OfficeAssignment` tabeli jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="1525e-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="1525e-211">Wywołuje `PopulateAssignedCourseData` w `OnGetAsync` celu podania informacji dla pól wyboru przy użyciu `AssignedCourseData` klasy model widoku.</span><span class="sxs-lookup"><span data-stu-id="1525e-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="1525e-212">Wywołuje `UpdateInstructorCourses` program `OnPostAsync` , aby zastosować informacje z pól wyboru do edytowanej jednostki instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="1525e-213">Wywołania `PopulateAssignedCourseData` i `UpdateInstructorCourses` w `OnPostAsync` przypadku `TryUpdateModel` niepowodzenia.</span><span class="sxs-lookup"><span data-stu-id="1525e-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="1525e-214">Te wywołania metody powodują przywrócenie przypisanych danych kursu wprowadzonych na stronie, gdy jest on ponownie wyświetlany z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="1525e-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="1525e-215">Aktualizowanie strony edytowania instruktora Razor</span><span class="sxs-lookup"><span data-stu-id="1525e-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="1525e-216">Aktualizowanie *stron/instruktorów/Edit. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="1525e-217">Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny.</span><span class="sxs-lookup"><span data-stu-id="1525e-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="1525e-218">Każda kolumna ma pole wyboru i podpis zawierający numer i tytuł kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="1525e-219">Wszystkie pola wyboru mają taką samą nazwę ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="1525e-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="1525e-220">Użycie tej samej nazwy informuje spinacz modelu, aby traktować go jako grupę.</span><span class="sxs-lookup"><span data-stu-id="1525e-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="1525e-221">Atrybut value każdego pola wyboru jest ustawiony na `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="1525e-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="1525e-222">Po opublikowaniu strony spinacz modelu przekaże tablicę, która składa się z `CourseID` wartości tylko wybranych pól wyboru.</span><span class="sxs-lookup"><span data-stu-id="1525e-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="1525e-223">Gdy pola wyboru są początkowo renderowane, wybierane są kursy przypisane do instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="1525e-224">Uwaga: podejście podjęte tutaj do edytowania danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów.</span><span class="sxs-lookup"><span data-stu-id="1525e-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="1525e-225">W przypadku kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłyby bardziej użyteczny i wydajny.</span><span class="sxs-lookup"><span data-stu-id="1525e-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="1525e-226">Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1525e-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="1525e-227">Zmień niektóre przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-227">Change some course assignments.</span></span> <span data-ttu-id="1525e-228">Zmiany zostaną odzwierciedlone na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="1525e-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="1525e-229">Aktualizowanie strony tworzenia instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-229">Update the Instructor Create page</span></span>

<span data-ttu-id="1525e-230">Zaktualizuj program instruktora Utwórz model strony i Razor stronę z kodem podobnym do strony edytowania:</span><span class="sxs-lookup"><span data-stu-id="1525e-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="1525e-231">Przetestuj stronę tworzenie instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="1525e-232">Aktualizuj stronę usuwania instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="1525e-233">Zaktualizuj *strony/instruktorów/Delete. cshtml. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="1525e-234">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1525e-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1525e-235">Używa ładowania eager dla `CourseAssignments` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="1525e-236">`CourseAssignments`musi być dołączony lub nie jest usuwany po usunięciu instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="1525e-237">Aby uniknąć konieczności ich odczytywania, skonfiguruj kaskadowe usuwanie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="1525e-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="1525e-238">Jeśli instruktor zostanie usunięty, zostanie przypisany jako administrator jakichkolwiek działów, program usunie przypisanie instruktora z tych urzędów.</span><span class="sxs-lookup"><span data-stu-id="1525e-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="1525e-239">Uruchom aplikację i Przetestuj stronę usuwania.</span><span class="sxs-lookup"><span data-stu-id="1525e-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1525e-240">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="1525e-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1525e-241">[Poprzedni samouczek](xref:data/ef-rp/read-related-data) 
>  [Następny samouczek](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="1525e-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1525e-242">W tym samouczku pokazano, jak aktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="1525e-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="1525e-243">Jeśli występują problemy, których nie można rozwiązać, [Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="1525e-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="1525e-244">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1525e-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="1525e-245">Na poniższych ilustracjach przedstawiono niektóre z ukończonych stron.</span><span class="sxs-lookup"><span data-stu-id="1525e-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="1525e-246">![Strona edycji kursów — Edytuj ](update-related-data/_static/course-edit.png)
 ![ stronę](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="1525e-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="1525e-247">Sprawdź i przetestuj strony Tworzenie i edytowanie kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="1525e-248">Utwórz nowy kurs.</span><span class="sxs-lookup"><span data-stu-id="1525e-248">Create a new course.</span></span> <span data-ttu-id="1525e-249">Dział jest wybierany przez jego klucz podstawowy (liczba całkowita), a nie jego nazwę.</span><span class="sxs-lookup"><span data-stu-id="1525e-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="1525e-250">Edytuj nowy kurs.</span><span class="sxs-lookup"><span data-stu-id="1525e-250">Edit the new course.</span></span> <span data-ttu-id="1525e-251">Po zakończeniu testowania Usuń nowy kurs.</span><span class="sxs-lookup"><span data-stu-id="1525e-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="1525e-252">Utwórz klasę bazową, aby udostępnić wspólny kod</span><span class="sxs-lookup"><span data-stu-id="1525e-252">Create a base class to share common code</span></span>

<span data-ttu-id="1525e-253">Wszystkie strony kursy/tworzenie i kursy/Edycja muszą mieć listę nazw działów.</span><span class="sxs-lookup"><span data-stu-id="1525e-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="1525e-254">Utwórz klasę bazową *stron/kursów/DepartmentNamePageModel. cshtml. cs* dla stron tworzenia i edytowania:</span><span class="sxs-lookup"><span data-stu-id="1525e-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="1525e-255">Poprzedni kod tworzy [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , aby zawierał listę nazw działów.</span><span class="sxs-lookup"><span data-stu-id="1525e-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="1525e-256">Jeśli `selectedDepartment` jest określony, ten dział jest wybierany w `SelectList` .</span><span class="sxs-lookup"><span data-stu-id="1525e-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="1525e-257">Klasy Utwórz i edytuj model strony będą pochodzić od `DepartmentNamePageModel` .</span><span class="sxs-lookup"><span data-stu-id="1525e-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="1525e-258">Dostosowywanie stron kursów</span><span class="sxs-lookup"><span data-stu-id="1525e-258">Customize the Courses Pages</span></span>

<span data-ttu-id="1525e-259">Po utworzeniu nowej jednostki kursu musi ona mieć relację z istniejącym działem.</span><span class="sxs-lookup"><span data-stu-id="1525e-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="1525e-260">Aby dodać dział podczas tworzenia kursu, Klasa bazowa do tworzenia i edycji zawiera listę rozwijaną umożliwiającą wybranie działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="1525e-261">Lista rozwijana ustawia `Course.DepartmentID` Właściwość klucz obcy (FK).</span><span class="sxs-lookup"><span data-stu-id="1525e-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="1525e-262">EF Core używa `Course.DepartmentID` klucza obcego do załadowania `Department` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Utwórz kurs](update-related-data/_static/ddl.png)

<span data-ttu-id="1525e-264">Zaktualizuj model tworzenia strony przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="1525e-265">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="1525e-265">The preceding code:</span></span>

* <span data-ttu-id="1525e-266">Pochodzi od `DepartmentNamePageModel` .</span><span class="sxs-lookup"><span data-stu-id="1525e-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="1525e-267">Używa `TryUpdateModelAsync` do zapobiegania [overposting](xref:data/ef-rp/crud#overposting)przepisywaniu.</span><span class="sxs-lookup"><span data-stu-id="1525e-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="1525e-268">Zamienia wartość `ViewData["DepartmentID"]` na `DepartmentNameSL` (z klasy bazowej).</span><span class="sxs-lookup"><span data-stu-id="1525e-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="1525e-269">`ViewData["DepartmentID"]`jest zastępowany silną typem `DepartmentNameSL` .</span><span class="sxs-lookup"><span data-stu-id="1525e-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="1525e-270">Modele silnie wpisane są preferowane za pośrednictwem słabo wpisanych.</span><span class="sxs-lookup"><span data-stu-id="1525e-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="1525e-271">Aby uzyskać więcej informacji, zobacz [słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="1525e-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="1525e-272">Aktualizowanie strony Tworzenie kursów</span><span class="sxs-lookup"><span data-stu-id="1525e-272">Update the Courses Create page</span></span>

<span data-ttu-id="1525e-273">Zaktualizuj *strony/kursy/Utwórz. cshtml* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="1525e-274">Poprzedzające znaczniki wprowadzają następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1525e-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="1525e-275">Zmienia podpis z **DepartmentID** na **dział**.</span><span class="sxs-lookup"><span data-stu-id="1525e-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="1525e-276">Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).</span><span class="sxs-lookup"><span data-stu-id="1525e-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="1525e-277">Dodaje opcję "Wybierz dział".</span><span class="sxs-lookup"><span data-stu-id="1525e-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="1525e-278">Ta zmiana renderuje "Select Department" zamiast pierwszego działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="1525e-279">Dodaje komunikat weryfikacyjny, gdy nie wybrano działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="1525e-280">RazorStrona używa [pomocnika wybierania tagu](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="1525e-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="1525e-281">Przetestuj stronę tworzenie.</span><span class="sxs-lookup"><span data-stu-id="1525e-281">Test the Create page.</span></span> <span data-ttu-id="1525e-282">Na stronie Tworzenie zostanie wyświetlona nazwa działu, a nie identyfikator działu.</span><span class="sxs-lookup"><span data-stu-id="1525e-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="1525e-283">Zaktualizuj stronę Edytowanie kursów.</span><span class="sxs-lookup"><span data-stu-id="1525e-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="1525e-284">Zastąp kod w obszarze *Pages/kursys/Edit. cshtml. cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1525e-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="1525e-285">Zmiany są podobne do tych, które zostały wprowadzone w modelu tworzenia strony.</span><span class="sxs-lookup"><span data-stu-id="1525e-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="1525e-286">W poprzednim kodzie program `PopulateDepartmentsDropDownList` przekazuje identyfikator działu, który wybierze dział określony na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="1525e-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="1525e-287">Aktualizowanie *stron/kursów/Edit. cshtml* przy użyciu następującego znacznika:</span><span class="sxs-lookup"><span data-stu-id="1525e-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="1525e-288">Poprzedzające znaczniki wprowadzają następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1525e-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="1525e-289">Wyświetla identyfikator kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-289">Displays the course ID.</span></span> <span data-ttu-id="1525e-290">Zazwyczaj klucz podstawowy (PK) jednostki nie jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="1525e-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="1525e-291">PKs są zwykle oznaczane przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1525e-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="1525e-292">W tym przypadku klucz podstawowy jest numerem kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="1525e-293">Zmienia podpis z **DepartmentID** na **dział**.</span><span class="sxs-lookup"><span data-stu-id="1525e-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="1525e-294">Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).</span><span class="sxs-lookup"><span data-stu-id="1525e-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="1525e-295">Ta strona zawiera ukryte pole ( `<input type="hidden">` ) dla numeru kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="1525e-296">Dodanie `<label>` pomocnika tagów z `asp-for="Course.CourseID"` nie eliminuje potrzeby pola ukrytego.</span><span class="sxs-lookup"><span data-stu-id="1525e-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="1525e-297">`<input type="hidden">`jest wymagana do uwzględnienia numeru kursu w opublikowanych danych, gdy użytkownik kliknie przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1525e-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="1525e-298">Przetestuj zaktualizowany kod.</span><span class="sxs-lookup"><span data-stu-id="1525e-298">Test the updated code.</span></span> <span data-ttu-id="1525e-299">Tworzenie, edytowanie i usuwanie kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="1525e-300">Dodawanie AsNoTracking do modeli szczegółów i stron usuwania</span><span class="sxs-lookup"><span data-stu-id="1525e-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="1525e-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="1525e-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="1525e-302">Dodaj `AsNoTracking` do modelu strony usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="1525e-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="1525e-303">Poniższy kod przedstawia zaktualizowany model strony usuwania:</span><span class="sxs-lookup"><span data-stu-id="1525e-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="1525e-304">Zaktualizuj `OnGetAsync` metodę w pliku *Pages/kursów/details. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="1525e-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="1525e-305">Modyfikowanie stron usuwania i szczegółów</span><span class="sxs-lookup"><span data-stu-id="1525e-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="1525e-306">Zaktualizuj stronę usuwania Razor przy użyciu następującej adjustacji:</span><span class="sxs-lookup"><span data-stu-id="1525e-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="1525e-307">Wprowadź te same zmiany na stronie Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="1525e-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="1525e-308">Testowanie stron kursów</span><span class="sxs-lookup"><span data-stu-id="1525e-308">Test the Course pages</span></span>

<span data-ttu-id="1525e-309">Testowanie tworzenia, edytowania, szczegółów i usuwania.</span><span class="sxs-lookup"><span data-stu-id="1525e-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="1525e-310">Aktualizowanie stron instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-310">Update the instructor pages</span></span>

<span data-ttu-id="1525e-311">W poniższych sekcjach zostały zaktualizowane strony instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="1525e-312">Dodaj lokalizację biura</span><span class="sxs-lookup"><span data-stu-id="1525e-312">Add office location</span></span>

<span data-ttu-id="1525e-313">Podczas edytowania rekordu instruktora warto zaktualizować przypisanie biura instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="1525e-314">`Instructor`Jednostka ma relację jeden do zera lub jeden z `OfficeAssignment` jednostką.</span><span class="sxs-lookup"><span data-stu-id="1525e-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="1525e-315">Kod instruktora musi obsłużyć:</span><span class="sxs-lookup"><span data-stu-id="1525e-315">The instructor code must handle:</span></span>

* <span data-ttu-id="1525e-316">Jeśli użytkownik wyczyści przypisanie pakietu Office, Usuń `OfficeAssignment` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1525e-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="1525e-317">Jeśli użytkownik wprowadzi przypisanie do pakietu Office i jest puste, należy utworzyć nową `OfficeAssignment` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1525e-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="1525e-318">Jeśli użytkownik zmieni przypisanie pakietu Office, zaktualizuj `OfficeAssignment` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="1525e-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="1525e-319">Zaktualizuj program instruktors Edytuj model strony przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="1525e-320">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="1525e-320">The preceding code:</span></span>

* <span data-ttu-id="1525e-321">Pobiera bieżącą `Instructor` jednostkę z bazy danych przy użyciu eager ładowania dla `OfficeAssignment` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="1525e-322">Aktualizuje pobraną `Instructor` jednostkę z wartościami ze spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="1525e-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="1525e-323">`TryUpdateModel`zapobiega [overposting](xref:data/ef-rp/crud#overposting)zastępowaniu.</span><span class="sxs-lookup"><span data-stu-id="1525e-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="1525e-324">Jeśli lokalizacja biura jest pusta, ustawia `Instructor.OfficeAssignment` wartość na null.</span><span class="sxs-lookup"><span data-stu-id="1525e-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="1525e-325">Gdy `Instructor.OfficeAssignment` ma wartość null, powiązany wiersz w `OfficeAssignment` tabeli jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="1525e-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="1525e-326">Aktualizowanie strony edytowania instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-326">Update the instructor Edit page</span></span>

<span data-ttu-id="1525e-327">Aktualizowanie *stron/instruktorów/Edit. cshtml* w lokalizacji biura:</span><span class="sxs-lookup"><span data-stu-id="1525e-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="1525e-328">Sprawdź, czy można zmienić lokalizację biura instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1525e-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="1525e-329">Dodawanie przypisań kursu do strony edytowania instruktora</span><span class="sxs-lookup"><span data-stu-id="1525e-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="1525e-330">Instruktorzy mogą uczyć się dowolnej liczby kursów.</span><span class="sxs-lookup"><span data-stu-id="1525e-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="1525e-331">W tej sekcji dodasz możliwość zmiany przypisań kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="1525e-332">Na poniższej ilustracji przedstawiono zaktualizowaną stronę edycji instruktora:</span><span class="sxs-lookup"><span data-stu-id="1525e-332">The following image shows the updated instructor Edit page:</span></span>

![Instruktor strony edytowania za pomocą kursów](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="1525e-334">`Course`i `Instructor` ma relację wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="1525e-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="1525e-335">Aby dodać i usunąć relacje, należy dodać i usunąć jednostki z `CourseAssignments` zestawu jednostek sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="1525e-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="1525e-336">Pola wyboru umożliwiają zmianę kursów, do których zostanie przypisany instruktor.</span><span class="sxs-lookup"><span data-stu-id="1525e-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="1525e-337">Pole wyboru jest wyświetlane dla każdego kursu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="1525e-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="1525e-338">Kursy, do których jest przypisany instruktor, są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="1525e-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="1525e-339">Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="1525e-340">Jeśli liczba kursów była znacznie większa:</span><span class="sxs-lookup"><span data-stu-id="1525e-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="1525e-341">Prawdopodobnie używasz innego interfejsu użytkownika do wyświetlania kursów.</span><span class="sxs-lookup"><span data-stu-id="1525e-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="1525e-342">Metoda manipulowania jednostką sprzężenia w celu tworzenia lub usuwania relacji nie ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="1525e-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="1525e-343">Dodaj klasy do obsługi tworzenia i edytowania stron instruktorów</span><span class="sxs-lookup"><span data-stu-id="1525e-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="1525e-344">Utwórz *SchoolViewModels/AssignedCourseData. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="1525e-345">`AssignedCourseData`Klasa zawiera dane, aby utworzyć pola wyboru dla przypisanych kursów przez instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="1525e-346">Utwórz klasę bazową *stron/instruktorów/InstructorCoursesPageModel. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="1525e-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="1525e-347">`InstructorCoursesPageModel`Jest klasą bazową, która będzie używana dla modeli stron Edycja i tworzenie.</span><span class="sxs-lookup"><span data-stu-id="1525e-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="1525e-348">`PopulateAssignedCourseData`odczytuje wszystkie `Course` jednostki do wypełnienia `AssignedCourseDataList` .</span><span class="sxs-lookup"><span data-stu-id="1525e-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="1525e-349">Dla każdego kursu kod ustawia `CourseID` , tytuł i określa, czy instruktor jest przypisany do kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="1525e-350">[HashSet —](/dotnet/api/system.collections.generic.hashset-1) jest używany do tworzenia wydajnych wyszukiwań.</span><span class="sxs-lookup"><span data-stu-id="1525e-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="1525e-351">Instruktorzy edytują model strony</span><span class="sxs-lookup"><span data-stu-id="1525e-351">Instructors Edit page model</span></span>

<span data-ttu-id="1525e-352">Zaktualizuj model strony instruktora do edycji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="1525e-353">Poprzedni kod obsługuje zmiany przypisania pakietu Office.</span><span class="sxs-lookup"><span data-stu-id="1525e-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="1525e-354">Aktualizuj widok instruktora Razor :</span><span class="sxs-lookup"><span data-stu-id="1525e-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="1525e-355">Gdy wkleisz kod w programie Visual Studio, podziały wierszy są zmieniane w sposób, który przerywa kod.</span><span class="sxs-lookup"><span data-stu-id="1525e-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="1525e-356">Naciśnij klawisze Ctrl + Z po raz, aby cofnąć automatyczne formatowanie.</span><span class="sxs-lookup"><span data-stu-id="1525e-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="1525e-357">Kombinacja klawiszy Ctrl + Z naprawia podziały wierszy, aby wyglądały tak, jak widać w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="1525e-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="1525e-358">Wcięcie nie musi być doskonałe, ale `@:</tr><tr>` linie,, `@:<td>` `@:</td>` i `@:</tr>` muszą znajdować się w jednym wierszu, jak pokazano.</span><span class="sxs-lookup"><span data-stu-id="1525e-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="1525e-359">Po wybraniu bloku nowego kodu naciśnij klawisz Tab trzy razy, aby wyrównać nowy kod z istniejącym kodem.</span><span class="sxs-lookup"><span data-stu-id="1525e-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="1525e-360">Zagłosuj lub Sprawdź stan tej usterki [za pomocą tego linku](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="1525e-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="1525e-361">Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny.</span><span class="sxs-lookup"><span data-stu-id="1525e-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="1525e-362">Każda kolumna ma pole wyboru i podpis zawierający numer i tytuł kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="1525e-363">Wszystkie pola wyboru mają tę samą nazwę ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="1525e-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="1525e-364">Użycie tej samej nazwy informuje spinacz modelu, aby traktować go jako grupę.</span><span class="sxs-lookup"><span data-stu-id="1525e-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="1525e-365">Atrybut value każdego pola wyboru jest ustawiony na `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="1525e-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="1525e-366">Po opublikowaniu strony spinacz modelu przekaże tablicę, która składa się z `CourseID` wartości tylko wybranych pól wyboru.</span><span class="sxs-lookup"><span data-stu-id="1525e-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="1525e-367">Gdy pola wyboru są początkowo renderowane, kursy przypisane do instruktora mają zaznaczone atrybuty.</span><span class="sxs-lookup"><span data-stu-id="1525e-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="1525e-368">Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów.</span><span class="sxs-lookup"><span data-stu-id="1525e-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="1525e-369">Zmień niektóre przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="1525e-369">Change some course assignments.</span></span> <span data-ttu-id="1525e-370">Zmiany zostaną odzwierciedlone na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="1525e-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="1525e-371">Uwaga: podejście podjęte tutaj do edytowania danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów.</span><span class="sxs-lookup"><span data-stu-id="1525e-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="1525e-372">W przypadku kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłyby bardziej użyteczny i wydajny.</span><span class="sxs-lookup"><span data-stu-id="1525e-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="1525e-373">Aktualizuj stronę tworzenia instruktorów</span><span class="sxs-lookup"><span data-stu-id="1525e-373">Update the instructors Create page</span></span>

<span data-ttu-id="1525e-374">Zaktualizuj program instruktora Tworzenie modelu strony przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="1525e-375">Poprzedni kod jest podobny do kodu *stron/instruktorów/Edit. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="1525e-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="1525e-376">Zaktualizuj stronę tworzenia instruktora Razor , korzystając z następującej adjustacji:</span><span class="sxs-lookup"><span data-stu-id="1525e-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="1525e-377">Przetestuj stronę tworzenie instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="1525e-378">Aktualizowanie strony usuwania</span><span class="sxs-lookup"><span data-stu-id="1525e-378">Update the Delete page</span></span>

<span data-ttu-id="1525e-379">Zaktualizuj model usuwania stron przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1525e-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="1525e-380">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="1525e-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1525e-381">Używa ładowania eager dla `CourseAssignments` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1525e-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="1525e-382">`CourseAssignments`musi być dołączony lub nie jest usuwany po usunięciu instruktora.</span><span class="sxs-lookup"><span data-stu-id="1525e-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="1525e-383">Aby uniknąć konieczności ich odczytywania, skonfiguruj kaskadowe usuwanie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="1525e-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="1525e-384">Jeśli instruktor zostanie usunięty, zostanie przypisany jako administrator jakichkolwiek działów, program usunie przypisanie instruktora z tych urzędów.</span><span class="sxs-lookup"><span data-stu-id="1525e-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1525e-385">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1525e-385">Additional resources</span></span>

* [<span data-ttu-id="1525e-386">Wersja usługi YouTube w tym samouczku (część 1)</span><span class="sxs-lookup"><span data-stu-id="1525e-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="1525e-387">Wersja usługi YouTube w tym samouczku (część 2)</span><span class="sxs-lookup"><span data-stu-id="1525e-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="1525e-388">[Poprzedni](xref:data/ef-rp/read-related-data) 
>  [Dalej](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="1525e-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
