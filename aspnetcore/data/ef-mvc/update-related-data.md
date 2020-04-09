---
title: 'Samouczek: Aktualizacja powiązanych danych - ASP.NET MVC z EF Core'
description: W tym samouczku zaktualizujesz powiązane dane, aktualizując pola klucza obcego i właściwości nawigacji.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 83d662659fb4bc7a2867be563e4e36927d2adafe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657144"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f1898-103">Samouczek: Aktualizacja powiązanych danych - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="f1898-103">Tutorial: Update related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f1898-104">W poprzednim samouczku wyświetlane powiązane dane; w tym samouczku zaktualizujesz powiązane dane, aktualizując pola klucza obcego i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-104">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="f1898-105">Na poniższych ilustracjach przedstawiono niektóre strony, z którymi będziesz pracować.</span><span class="sxs-lookup"><span data-stu-id="f1898-105">The following illustrations show some of the pages that you'll work with.</span></span>

![Strona edycji kursu](update-related-data/_static/course-edit.png)

![Strona edycji instruktora](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f1898-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f1898-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f1898-109">Dostosowywanie stron kursów</span><span class="sxs-lookup"><span data-stu-id="f1898-109">Customize Courses pages</span></span>
> * <span data-ttu-id="f1898-110">Strona Edycja dodaj instruktorów</span><span class="sxs-lookup"><span data-stu-id="f1898-110">Add Instructors Edit page</span></span>
> * <span data-ttu-id="f1898-111">Dodawanie kursów do strony Edycja</span><span class="sxs-lookup"><span data-stu-id="f1898-111">Add courses to Edit page</span></span>
> * <span data-ttu-id="f1898-112">Aktualizuj stronę Usuwania</span><span class="sxs-lookup"><span data-stu-id="f1898-112">Update Delete page</span></span>
> * <span data-ttu-id="f1898-113">Dodawanie lokalizacji i kursów w biurze do strony Tworzenie</span><span class="sxs-lookup"><span data-stu-id="f1898-113">Add office location and courses to Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1898-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f1898-114">Prerequisites</span></span>

* [<span data-ttu-id="f1898-115">Odczytywanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="f1898-115">Read related data</span></span>](read-related-data.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="f1898-116">Dostosowywanie stron kursów</span><span class="sxs-lookup"><span data-stu-id="f1898-116">Customize Courses pages</span></span>

<span data-ttu-id="f1898-117">Po utworzeniu nowej jednostki kursu musi mieć relację z istniejącym działem.</span><span class="sxs-lookup"><span data-stu-id="f1898-117">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="f1898-118">Aby to ułatwić, szkieletowy kod zawiera metody kontrolera i Tworzenie i edytowanie widoków, które zawierają listę rozwijaną do wybierania działu.</span><span class="sxs-lookup"><span data-stu-id="f1898-118">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="f1898-119">Lista rozwijana ustawia `Course.DepartmentID` właściwość klucza obcego i to wszystko entity framework `Department` potrzebuje w celu załadowania właściwości nawigacji z odpowiednią jednostką działu.</span><span class="sxs-lookup"><span data-stu-id="f1898-119">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="f1898-120">Użyjesz kodu szkieletu, ale zmień go nieznacznie, aby dodać obsługę błędów i posortować listę rozwijaną.</span><span class="sxs-lookup"><span data-stu-id="f1898-120">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="f1898-121">W *CoursesController.cs*usuń cztery metody Tworzenia i edycji i zastąp je następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f1898-121">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="f1898-122">Po `Edit` HttpPost metody, utwórz nową metodę, która ładuje informacje o dziale dla listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="f1898-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="f1898-123">Metoda `PopulateDepartmentsDropDownList` pobiera listę wszystkich działów posortowane `SelectList` według nazwy, tworzy kolekcję dla listy rozwijanej i przekazuje kolekcję do widoku w `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="f1898-123">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="f1898-124">Metoda akceptuje parametr `selectedDepartment` opcjonalny, który umożliwia kod wywołujący, aby określić element, który zostanie wybrany podczas renderowania listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="f1898-124">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="f1898-125">Widok przekaże nazwę "DepartmentID" `<select>` do pomocnika tagu, a pomocnik następnie `ViewBag` wie, `SelectList` aby spojrzeć w obiekcie na nazwany "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="f1898-125">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="f1898-126">HttpGet `Create` Metoda wywołuje `PopulateDepartmentsDropDownList` metodę bez ustawiania wybranego elementu, ponieważ dla nowego kursu dział nie jest jeszcze ustanowiony:</span><span class="sxs-lookup"><span data-stu-id="f1898-126">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="f1898-127">HttpGet `Edit` Metoda ustawia wybrany element, na podstawie identyfikatora działu, który jest już przypisany do kursu edytowane:</span><span class="sxs-lookup"><span data-stu-id="f1898-127">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="f1898-128">Metody HttpPost dla `Create` `Edit` obu, a także kod, który ustawia wybrany element, gdy ponownie wyświetlić stronę po błędzie.</span><span class="sxs-lookup"><span data-stu-id="f1898-128">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="f1898-129">Gwarantuje to, że gdy strona zostanie ponownie wyświetlona, aby wyświetlić komunikat o błędzie, niezależnie od wybranego działu pozostaje zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="f1898-129">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="f1898-130">Dodaj. AsNoTracking do szczegółów i metody usuwania</span><span class="sxs-lookup"><span data-stu-id="f1898-130">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="f1898-131">Aby zoptymalizować wydajność szczegóły kursu i `AsNoTracking` usuń strony, dodaj wywołania w `Details` i HttpGet `Delete` metody.</span><span class="sxs-lookup"><span data-stu-id="f1898-131">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="f1898-132">Modyfikowanie widoków kursu</span><span class="sxs-lookup"><span data-stu-id="f1898-132">Modify the Course views</span></span>

<span data-ttu-id="f1898-133">W *obszarze Widoki/Kursy/Utwórz.cshtml*dodaj opcję "Wybierz dział" do listy rozwijanej **Dział,** zmień podpis z **Identyfikatora departamentu** na **Dział**i dodaj komunikat sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="f1898-133">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="f1898-134">W *obszarze Widoki/Kursy/Edit.cshtml*należy wprowadzić tę samą zmianę w polu Dział, co pole *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f1898-134">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="f1898-135">Również w *obszarze Widoki/Kursy/Edit.cshtml*dodaj pole numer kursu przed polem **Tytuł.**</span><span class="sxs-lookup"><span data-stu-id="f1898-135">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="f1898-136">Ponieważ numer kursu jest kluczem podstawowym, jest wyświetlany, ale nie można go zmienić.</span><span class="sxs-lookup"><span data-stu-id="f1898-136">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="f1898-137">Istnieje już ukryte pole`<input type="hidden">`( ) dla numeru kursu w widoku Edycja.</span><span class="sxs-lookup"><span data-stu-id="f1898-137">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="f1898-138">Dodanie `<label>` pomocnika znacznika nie eliminuje potrzeby korzystania z ukrytego pola, ponieważ nie powoduje, że numer kursu zostanie uwzględniony w opublikowanych danych, gdy użytkownik kliknie **przycisk Zapisz** na stronie **Edytuj.**</span><span class="sxs-lookup"><span data-stu-id="f1898-138">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="f1898-139">W *obszarze Widoki/Kursy/Usuń.cshtml*dodaj pole numeru kursu u góry i zmień identyfikator działu do nazwy działu.</span><span class="sxs-lookup"><span data-stu-id="f1898-139">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="f1898-140">W *widokach/kursach/details.cshtml*należy wprowadzić tę samą zmianę, co w przypadku *pliku Delete.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f1898-140">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="f1898-141">Testowanie stron kursu</span><span class="sxs-lookup"><span data-stu-id="f1898-141">Test the Course pages</span></span>

<span data-ttu-id="f1898-142">Uruchom aplikację, wybierz kartę **Kursy,** kliknij pozycję **Utwórz nowy**i wprowadź dane dla nowego kursu:</span><span class="sxs-lookup"><span data-stu-id="f1898-142">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Strona Utwór utwórz kurs](update-related-data/_static/course-create.png)

<span data-ttu-id="f1898-144">Kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f1898-144">Click **Create**.</span></span> <span data-ttu-id="f1898-145">Strona Indeks kursów jest wyświetlana z nowym kursem dodanym do listy.</span><span class="sxs-lookup"><span data-stu-id="f1898-145">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="f1898-146">Nazwa działu na liście strony Indeks pochodzi z właściwości nawigacji, pokazując, że relacja została ustanowiona poprawnie.</span><span class="sxs-lookup"><span data-stu-id="f1898-146">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="f1898-147">Kliknij **pozycję Edytuj** kurs na stronie Indeks kursów.</span><span class="sxs-lookup"><span data-stu-id="f1898-147">Click **Edit** on a course in the Courses Index page.</span></span>

![Strona edycji kursu](update-related-data/_static/course-edit.png)

<span data-ttu-id="f1898-149">Zmienianie danych na stronie i **klikanie przycisku Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="f1898-149">Change data on the page and click **Save**.</span></span> <span data-ttu-id="f1898-150">Strona Indeks kursów jest wyświetlana ze zaktualizowanymi danymi kursu.</span><span class="sxs-lookup"><span data-stu-id="f1898-150">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-instructors-edit-page"></a><span data-ttu-id="f1898-151">Strona Edycja dodaj instruktorów</span><span class="sxs-lookup"><span data-stu-id="f1898-151">Add Instructors Edit page</span></span>

<span data-ttu-id="f1898-152">Podczas edytowania rekordu instruktora chcesz mieć możliwość aktualizacji przypisania biura instruktora.</span><span class="sxs-lookup"><span data-stu-id="f1898-152">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="f1898-153">Jednostka Instructor ma relację jeden do zera lub jeden z OfficeAssignment jednostki, co oznacza, że kod musi obsługiwać następujące sytuacje:</span><span class="sxs-lookup"><span data-stu-id="f1898-153">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="f1898-154">Jeśli użytkownik czyści przypisanie biura i pierwotnie miał wartość, usuń OfficeAssignment jednostki.</span><span class="sxs-lookup"><span data-stu-id="f1898-154">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="f1898-155">Jeśli użytkownik wprowadzi wartość przypisania biura i pierwotnie była pusta, utwórz nową encję OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="f1898-155">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="f1898-156">Jeśli użytkownik zmieni wartość przypisania biura, zmień wartość w istniejącej encji OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="f1898-156">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="f1898-157">Aktualizowanie kontrolera instruktorów</span><span class="sxs-lookup"><span data-stu-id="f1898-157">Update the Instructors controller</span></span>

<span data-ttu-id="f1898-158">W *InstructorsController.cs*, zmień kod w HttpGet `Edit` metody tak, aby załadować `OfficeAssignment` Instructor właściwości `AsNoTracking`nawigacji jednostki i wywołuje:</span><span class="sxs-lookup"><span data-stu-id="f1898-158">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

<span data-ttu-id="f1898-159">Zastąp `Edit` metodę HttpPost następującym kodem do obsługi aktualizacji przypisania biura:</span><span class="sxs-lookup"><span data-stu-id="f1898-159">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="f1898-160">Kod wykonuje następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f1898-160">The code does the following:</span></span>

* <span data-ttu-id="f1898-161">Zmienia nazwę metody, `EditPost` ponieważ podpis jest teraz taki `Edit` sam `ActionName` jak HttpGet metody `/Edit/` (atrybut określa, że adres URL jest nadal używany).</span><span class="sxs-lookup"><span data-stu-id="f1898-161">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

* <span data-ttu-id="f1898-162">Pobiera bieżącą instructor jednostki z bazy `OfficeAssignment` danych przy użyciu ładowania eager dla właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-162">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="f1898-163">Jest to taka sama, jak to, co zrobiłeś w HttpGet `Edit` metody.</span><span class="sxs-lookup"><span data-stu-id="f1898-163">This is the same as what you did in the HttpGet `Edit` method.</span></span>

* <span data-ttu-id="f1898-164">Aktualizuje pobraną jednostkę Instructor z wartościami z spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="f1898-164">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="f1898-165">Przeciążenie `TryUpdateModel` umożliwia umieszczanie na białej liście właściwości, które chcesz uwzględnić.</span><span class="sxs-lookup"><span data-stu-id="f1898-165">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="f1898-166">Zapobiega to nadmiernemu publikowaniu, jak wyjaśniono w [drugim tutorialu](crud.md).</span><span class="sxs-lookup"><span data-stu-id="f1898-166">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* <span data-ttu-id="f1898-167">Jeśli lokalizacja biura jest pusta, ustawia Instructor.OfficeAssignment właściwość null tak, aby powiązany wiersz w OfficeAssignment tabeli zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="f1898-167">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* <span data-ttu-id="f1898-168">Zapisuje zmiany w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f1898-168">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="f1898-169">Aktualizowanie widoku edycji instruktora</span><span class="sxs-lookup"><span data-stu-id="f1898-169">Update the Instructor Edit view</span></span>

<span data-ttu-id="f1898-170">W *obszarze Widoki/Instruktorzy/Edit.cshtml*dodaj nowe pole do edycji lokalizacji biura na końcu przed przyciskiem **Zapisz:**</span><span class="sxs-lookup"><span data-stu-id="f1898-170">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="f1898-171">Uruchom aplikację, wybierz kartę **Instruktorzy,** a następnie kliknij pozycję **Edytuj** instruktora.</span><span class="sxs-lookup"><span data-stu-id="f1898-171">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="f1898-172">Zmień **lokalizację biura** i kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="f1898-172">Change the **Office Location** and click **Save**.</span></span>

![Strona edycji instruktora](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a><span data-ttu-id="f1898-174">Dodawanie kursów do strony Edycja</span><span class="sxs-lookup"><span data-stu-id="f1898-174">Add courses to Edit page</span></span>

<span data-ttu-id="f1898-175">Instruktorzy mogą nauczać dowolną liczbę kursów.</span><span class="sxs-lookup"><span data-stu-id="f1898-175">Instructors may teach any number of courses.</span></span> <span data-ttu-id="f1898-176">Teraz możesz ulepszyć stronę Edycja instruktora, dodając możliwość zmiany przydziałów kursów przy użyciu grupy pól wyboru, jak pokazano na poniższym zstrząśnieniu ekranu:</span><span class="sxs-lookup"><span data-stu-id="f1898-176">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f1898-178">Relacja między course i instructor jednostek jest wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="f1898-178">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="f1898-179">Aby dodać i usunąć relacje, należy dodać i usunąć encje do i z zestawu jednostek sprzężenia courseAssignments.</span><span class="sxs-lookup"><span data-stu-id="f1898-179">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="f1898-180">Interfejs użytkownika, który umożliwia zmianę kursów, do których jest przypisany instruktor, jest grupą pól wyboru.</span><span class="sxs-lookup"><span data-stu-id="f1898-180">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="f1898-181">Zostanie wyświetlone pole wyboru dla każdego kursu w bazie danych i wybrane są te, do których instruktor jest aktualnie przypisany.</span><span class="sxs-lookup"><span data-stu-id="f1898-181">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="f1898-182">Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu.</span><span class="sxs-lookup"><span data-stu-id="f1898-182">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="f1898-183">Jeśli liczba kursów były znacznie większe, prawdopodobnie chcesz użyć innej metody prezentowania danych w widoku, ale należy użyć tej samej metody manipulowania jednostki sprzężenia do tworzenia lub usuwania relacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-183">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="f1898-184">Aktualizowanie kontrolera instruktorów</span><span class="sxs-lookup"><span data-stu-id="f1898-184">Update the Instructors controller</span></span>

<span data-ttu-id="f1898-185">Aby udostępnić dane do widoku dla listy pól wyboru, użyj klasy modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="f1898-185">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="f1898-186">Utwórz *AssignedCourseData.cs* w folderze *SchoolViewModels* i zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f1898-186">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="f1898-187">W *InstructorsController.cs*, zastąp `Edit` HttpGet metody z następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="f1898-187">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="f1898-188">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="f1898-188">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="f1898-189">Kod dodaje wczesne ładowanie `Courses` dla właściwości nawigacji `PopulateAssignedCourseData` i wywołuje nową metodę, aby `AssignedCourseData` zapewnić informacje dla tablicy pola wyboru przy użyciu klasy modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="f1898-189">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="f1898-190">Kod w `PopulateAssignedCourseData` metodzie odczytuje za pośrednictwem wszystkich Course jednostek w celu załadowania listy kursów przy użyciu klasy modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="f1898-190">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="f1898-191">Dla każdego kursu kod sprawdza, czy kurs istnieje `Courses` we właściwości nawigacji instruktora.</span><span class="sxs-lookup"><span data-stu-id="f1898-191">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="f1898-192">Aby utworzyć efektywne wyszukiwanie podczas sprawdzania, czy kurs jest przypisany do instruktora, `HashSet` kursy przypisane do instruktora są umieszczane w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="f1898-192">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="f1898-193">Właściwość `Assigned` jest ustawiona na true dla kursów, do które jest przypisany instruktor.</span><span class="sxs-lookup"><span data-stu-id="f1898-193">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="f1898-194">Widok użyje tej właściwości, aby określić, które pola wyboru muszą być wyświetlane jako zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="f1898-194">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="f1898-195">Na koniec lista jest przekazywana `ViewData`do widoku w pliku .</span><span class="sxs-lookup"><span data-stu-id="f1898-195">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="f1898-196">Następnie dodaj kod, który jest wykonywany, gdy użytkownik kliknie **przycisk Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="f1898-196">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="f1898-197">Zastąp `EditPost` metodę następującym kodem i dodaj `Courses` nową metodę, która aktualizuje właściwość nawigacji instructor jednostki.</span><span class="sxs-lookup"><span data-stu-id="f1898-197">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="f1898-198">Podpis metody różni się teraz `Edit` od metody HttpGet, `EditPost` więc `Edit`nazwa metody zmienia się z powrotem na .</span><span class="sxs-lookup"><span data-stu-id="f1898-198">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="f1898-199">Ponieważ widok nie ma kolekcji Course jednostek, spinacza modelu nie `CourseAssignments` można automatycznie zaktualizować właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-199">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f1898-200">Zamiast używać spinacza modelu, `CourseAssignments` aby zaktualizować właściwość nawigacji, `UpdateInstructorCourses` można to zrobić w nowej metodzie.</span><span class="sxs-lookup"><span data-stu-id="f1898-200">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="f1898-201">W związku z tym `CourseAssignments` należy wykluczyć właściwość z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="f1898-201">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="f1898-202">Nie wymaga to żadnych zmian w `TryUpdateModel` kodzie, który wywołuje, ponieważ używasz przeciążenia białej listy i `CourseAssignments` nie znajduje się na liście dołączania.</span><span class="sxs-lookup"><span data-stu-id="f1898-202">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="f1898-203">Jeśli nie wybrano żadnych `UpdateInstructorCourses` pól wyboru, kod inicjuje właściwość `CourseAssignments` nawigacji z pustą kolekcją i zwraca:</span><span class="sxs-lookup"><span data-stu-id="f1898-203">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="f1898-204">Kod następnie pętli przez wszystkie kursy w bazie danych i sprawdza każdy kurs względem tych aktualnie przypisanych do instruktora w porównaniu z tymi, które zostały wybrane w widoku.</span><span class="sxs-lookup"><span data-stu-id="f1898-204">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="f1898-205">Aby ułatwić efektywne wyszukiwanie, dwie ostatnie `HashSet` kolekcje są przechowywane w obiektach.</span><span class="sxs-lookup"><span data-stu-id="f1898-205">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="f1898-206">Jeśli pole wyboru dla kursu zostało zaznaczone, ale `Instructor.CourseAssignments` kurs nie znajduje się we właściwości nawigacji, kurs jest dodawany do kolekcji we właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-206">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="f1898-207">Jeśli pole wyboru kursu nie zostało zaznaczone, ale `Instructor.CourseAssignments` kurs znajduje się we właściwości nawigacji, kurs zostanie usunięty z właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-207">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="f1898-208">Aktualizowanie widoków instruktora</span><span class="sxs-lookup"><span data-stu-id="f1898-208">Update the Instructor views</span></span>

<span data-ttu-id="f1898-209">W *widoku Views/Instructors/Edit.cshtml*dodaj pole **Kursy** z tablicą pól wyboru, `div` dodając następujący kod bezpośrednio `div` po elementach pola Pakietu **Office** i przed elementem przycisku **Zapisz.**</span><span class="sxs-lookup"><span data-stu-id="f1898-209">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="f1898-210">Po wklejeniu kodu w programie Visual Studio podziały wierszy mogą zostać zmienione w sposób, który przerywa kod.</span><span class="sxs-lookup"><span data-stu-id="f1898-210">When you paste the code in Visual Studio, line breaks might be changed in a way that breaks the code.</span></span> <span data-ttu-id="f1898-211">Jeśli kod wygląda inaczej po wklejeniu, naciśnij klawisze Ctrl+Z jeden raz, aby cofnąć automatyczne formatowanie.</span><span class="sxs-lookup"><span data-stu-id="f1898-211">If the code looks different after pasting, press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="f1898-212">Spowoduje to naprawienie podziałów wierszy, tak aby wyglądały jak to, co widzisz tutaj.</span><span class="sxs-lookup"><span data-stu-id="f1898-212">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="f1898-213">Wcięcie nie musi być doskonałe, ale `@</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` i wiersze muszą być w jednym wierszu, jak pokazano lub pojawi się błąd środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="f1898-213">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="f1898-214">Po wybraniu bloku nowego kodu naciśnij trzykrotnie klawisz Tab, aby wyrównać nowy kod z istniejącym kodem.</span><span class="sxs-lookup"><span data-stu-id="f1898-214">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="f1898-215">Ten problem został rozwiązany w programie Visual Studio 2019.</span><span class="sxs-lookup"><span data-stu-id="f1898-215">This problem is fixed in Visual Studio 2019.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="f1898-216">Ten kod tworzy tabelę HTML, która ma trzy kolumny.</span><span class="sxs-lookup"><span data-stu-id="f1898-216">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="f1898-217">W każdej kolumnie znajduje się pole wyboru, po którym następuje podpis, który składa się z numeru kursu i tytułu.</span><span class="sxs-lookup"><span data-stu-id="f1898-217">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="f1898-218">Wszystkie pola wyboru mają taką samą nazwę ("selectedCourses"), która informuje spinacz modelu, że mają być traktowane jako grupa.</span><span class="sxs-lookup"><span data-stu-id="f1898-218">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="f1898-219">Atrybut value każdego pola wyboru jest ustawiony `CourseID`na wartość .</span><span class="sxs-lookup"><span data-stu-id="f1898-219">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="f1898-220">Po zaksięgowaniu strony spinacz modelu przekazuje tablicę do `CourseID` kontrolera, która składa się z wartości tylko dla zaznaczonych pól wyboru.</span><span class="sxs-lookup"><span data-stu-id="f1898-220">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="f1898-221">Gdy pola wyboru są początkowo renderowane, te, które są dla kursów przypisanych do instruktora mają sprawdzone atrybuty, który je wybiera (wyświetla je zaznaczone).</span><span class="sxs-lookup"><span data-stu-id="f1898-221">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="f1898-222">Uruchom aplikację, wybierz kartę **Instruktorzy** i kliknij pozycję **Edytuj** instruktora, aby wyświetlić stronę **Edycja.**</span><span class="sxs-lookup"><span data-stu-id="f1898-222">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f1898-224">Zmień niektóre przypisania kursu i kliknij przycisk Zapisz.</span><span class="sxs-lookup"><span data-stu-id="f1898-224">Change some course assignments and click Save.</span></span> <span data-ttu-id="f1898-225">Wprowadzone zmiany zostaną odzwierciedlone na stronie Indeks.</span><span class="sxs-lookup"><span data-stu-id="f1898-225">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="f1898-226">Podejście przyjęte tutaj, aby edytować dane kursu instruktora, działa dobrze, gdy istnieje ograniczona liczba kursów.</span><span class="sxs-lookup"><span data-stu-id="f1898-226">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="f1898-227">Dla kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji będzie wymagane.</span><span class="sxs-lookup"><span data-stu-id="f1898-227">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-delete-page"></a><span data-ttu-id="f1898-228">Aktualizuj stronę Usuwania</span><span class="sxs-lookup"><span data-stu-id="f1898-228">Update Delete page</span></span>

<span data-ttu-id="f1898-229">W *InstructorsController.cs*, usuń `DeleteConfirmed` metodę i wstaw poniższy kod w jej miejsce.</span><span class="sxs-lookup"><span data-stu-id="f1898-229">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="f1898-230">Ten kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="f1898-230">This code makes the following changes:</span></span>

* <span data-ttu-id="f1898-231">Czy gorliwy `CourseAssignments` ładowania dla właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f1898-231">Does eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f1898-232">Musisz dołączyć to lub EF nie będzie `CourseAssignment` wiedzieć o powiązanych jednostek i nie będzie ich usuwać.</span><span class="sxs-lookup"><span data-stu-id="f1898-232">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span> <span data-ttu-id="f1898-233">Aby uniknąć konieczności ich odczytywania tutaj można skonfigurować kaskadowe usuwanie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f1898-233">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="f1898-234">Jeśli instruktor do usunięcia jest przypisany jako administrator wszystkich działów, usuwa przypisanie instruktora z tych działów.</span><span class="sxs-lookup"><span data-stu-id="f1898-234">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-create-page"></a><span data-ttu-id="f1898-235">Dodawanie lokalizacji i kursów w biurze do strony Tworzenie</span><span class="sxs-lookup"><span data-stu-id="f1898-235">Add office location and courses to Create page</span></span>

<span data-ttu-id="f1898-236">W *InstructorsController.cs*, usuń HttpGet i `Create` HttpPost metody, a następnie dodać następujący kod w ich miejsce:</span><span class="sxs-lookup"><span data-stu-id="f1898-236">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="f1898-237">Ten kod jest podobny do `Edit` tego, co widziałeś dla metod, z tą różnicą, że początkowo nie są wybierane żadne kursy.</span><span class="sxs-lookup"><span data-stu-id="f1898-237">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="f1898-238">HttpGet `Create` metoda wywołuje `PopulateAssignedCourseData` metodę nie dlatego, że mogą być kursy wybrane, `foreach` ale w celu zapewnienia pustej kolekcji dla pętli w widoku (w przeciwnym razie kod widoku będzie zgłosić wyjątek odwołania null).</span><span class="sxs-lookup"><span data-stu-id="f1898-238">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="f1898-239">HttpPost `Create` Metoda dodaje każdy wybrany `CourseAssignments` kurs do właściwości nawigacji, zanim sprawdzi błędy sprawdzania poprawności i dodaje nowego instruktora do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f1898-239">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="f1898-240">Kursy są dodawane, nawet jeśli istnieją błędy modelu, dzięki czemu w przypadku wystąpienia błędów modelu (na przykład użytkownik wpisał nieprawidłową datę), a strona jest ponownie świetliona komunikatem o błędzie, wszystkie wybory kursu, które zostały dokonane, są automatycznie przywracane.</span><span class="sxs-lookup"><span data-stu-id="f1898-240">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="f1898-241">Należy zauważyć, że aby móc dodać `CourseAssignments` kursy do właściwości nawigacji należy zainicjować właściwość jako pustą kolekcję:</span><span class="sxs-lookup"><span data-stu-id="f1898-241">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="f1898-242">Jako alternatywę dla tego w kodzie kontrolera, można to zrobić w modelu Instructor, zmieniając getter właściwości, aby automatycznie utworzyć kolekcję, jeśli nie istnieje, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f1898-242">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="f1898-243">Jeśli zmodyfikujesz `CourseAssignments` właściwość w ten sposób, można usunąć jawny kod inicjowania właściwości w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="f1898-243">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="f1898-244">W *polu Widoki/Instruktor/Utwórz.cshtml*dodaj pole tekstowe lokalizacji biura i pola wyboru dla kursów przed przyciskiem Prześlij.</span><span class="sxs-lookup"><span data-stu-id="f1898-244">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="f1898-245">Podobnie jak w przypadku strony Edycja, [należy naprawić formatowanie, jeśli program Visual Studio formatuje kod podczas wklejania go.](#notepad)</span><span class="sxs-lookup"><span data-stu-id="f1898-245">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="f1898-246">Przetestuj, uruchamiając aplikację i tworząc instruktora.</span><span class="sxs-lookup"><span data-stu-id="f1898-246">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="f1898-247">Obsługa transakcji</span><span class="sxs-lookup"><span data-stu-id="f1898-247">Handling Transactions</span></span>

<span data-ttu-id="f1898-248">Jak wyjaśniono w [samouczku CRUD,](crud.md)entity framework niejawnie implementuje transakcje.</span><span class="sxs-lookup"><span data-stu-id="f1898-248">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="f1898-249">W przypadku scenariuszy, w których potrzebujesz większej kontroli — na przykład, jeśli chcesz uwzględnić operacje wykonywane poza platformą Encji w transakcji — zobacz [Transakcje](/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="f1898-249">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f1898-250">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="f1898-250">Get the code</span></span>

[<span data-ttu-id="f1898-251">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="f1898-251">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="f1898-252">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="f1898-252">Next steps</span></span>

<span data-ttu-id="f1898-253">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f1898-253">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f1898-254">Strony dostosowane kursy</span><span class="sxs-lookup"><span data-stu-id="f1898-254">Customized Courses pages</span></span>
> * <span data-ttu-id="f1898-255">Dodano stronę Edycji instruktorów</span><span class="sxs-lookup"><span data-stu-id="f1898-255">Added Instructors Edit page</span></span>
> * <span data-ttu-id="f1898-256">Dodano kursy do strony Edycja</span><span class="sxs-lookup"><span data-stu-id="f1898-256">Added courses to Edit page</span></span>
> * <span data-ttu-id="f1898-257">Zaktualizowana strona Usuń</span><span class="sxs-lookup"><span data-stu-id="f1898-257">Updated Delete page</span></span>
> * <span data-ttu-id="f1898-258">Dodano lokalizację biura i kursy do utworzenia strony</span><span class="sxs-lookup"><span data-stu-id="f1898-258">Added office location and courses to Create page</span></span>

<span data-ttu-id="f1898-259">Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="f1898-259">Advance to the next tutorial to learn how to handle concurrency conflicts.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f1898-260">Obsługa konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="f1898-260">Handle concurrency conflicts</span></span>](concurrency.md)
