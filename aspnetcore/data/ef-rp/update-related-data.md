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
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Strony brzytwy z EF Core w ASP.NET Core - Aktualizacja powiązanych danych - 7 z 8

Przez [Tom Dykstra](https://github.com/tdykstra)i [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku pokazano, jak zaktualizować powiązane dane. Na poniższych ilustracjach przedstawiono niektóre ukończone strony.

![Strona edycji kursu](update-related-data/_static/course-edit30.png)
![Edycja instruktora](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aktualizowanie stron Tworzenie i edytowanie kursu

Szkieletowy kod dla stron Utwórz i Edytuj kurs ma listę rozwijaną Dział, która pokazuje identyfikator działu (liczba całkowita). Lista rozwijana powinna być wyświetlana nazwę działu, więc obie te strony wymagają listy nazw działów. Aby udostępnić tę listę, należy użyć klasy podstawowej dla stron Tworzenie i edytowanie.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Tworzenie klasy podstawowej dla tworzenia i edytowania kursu

Utwórz plik *Pages/Courses/DepartmentNamePageModel.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Poprzedni kod tworzy [SelectList,](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) aby zawierać listę nazw działów. Jeśli `selectedDepartment` jest określony, ten dział `SelectList`jest zaznaczony w .

Klasy modelu strony Utwórz i `DepartmentNamePageModel`edytuj będą pochodzić z .

### <a name="update-the-course-create-page-model"></a>Aktualizowanie modelu strony Tworzenie kursu

Kurs jest przypisany do działu. Klasa podstawowa stron Tworzenie i `SelectList` edytowanie umożliwia wybranie działu. Lista rozwijana, która używa `SelectList` właściwości `Course.DepartmentID` ustawia klucz obcy (FK). EF Core używa `Course.DepartmentID` FK do `Department` załadowania właściwości nawigacji.

![Tworzenie kursu](update-related-data/_static/ddl30.png)

Aktualizuj *strony/kursy/Create.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Powyższy kod ma następujące działanie:

* Pochodzi z `DepartmentNamePageModel`.
* Służy `TryUpdateModelAsync` do zapobiegania [nadpostowaniu](xref:data/ef-rp/crud#overposting).
* Usuwa `ViewData["DepartmentID"]`plik . `DepartmentNameSL`z klasy podstawowej jest modelem silnie typiwanym i będzie używany przez stronę Razor. Silnie typizowane modele są preferowane niż słabo wpisane. Aby uzyskać więcej informacji, zobacz [Słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-razor-page"></a>Aktualizowanie strony Tworzenie brzytwy kursu

Zaktualizuj *strony/kursy/Create.cshtml* za pomocą następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Poprzedni kod wprowadza następujące zmiany:

* Zmienia podpis z **DepartmentID** na **Dział**.
* `"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).
* Dodaje opcję "Wybierz dział". Ta zmiana powoduje, że "Wybierz dział" w rozwijaniu, gdy nie wybrano jeszcze żadnego działu, a nie pierwszego działu.
* Dodaje komunikat sprawdzania poprawności, gdy dział nie jest zaznaczony.

Strona Razor używa [pomocnika wyboru znacznika:](xref:mvc/views/working-with-forms#the-select-tag-helper)

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Przetestuj stronę Utwórz. Na stronie Utwórz wyświetlana jest nazwa działu, a nie identyfikator działu.

### <a name="update-the-course-edit-page-model"></a>Aktualizowanie modelu strony Edycji kursu

Zaktualizuj *strony/kursy/Edit.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Zmiany są podobne do tych wprowadzonych w modelu strony Tworzenie. W poprzednim kodzie `PopulateDepartmentsDropDownList` przekazuje identyfikator działu, który wybiera ten dział na liście rozwijanej.

### <a name="update-the-course-edit-razor-page"></a>Aktualizowanie strony Edycji Razora kursu

Zaktualizuj *strony/kursy/edit.cshtml* za pomocą następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Poprzedni kod wprowadza następujące zmiany:

* Wyświetla identyfikator kursu. Ogólnie rzecz biorąc klucz podstawowy (PK) jednostki nie jest wyświetlany. PKs są zwykle bez znaczenia dla użytkowników. W tym przypadku PK jest numerem kursu.
* Zmienia podpis listy rozwijanej Departament **z DepartmentID** na **Department**.
* `"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).

Strona zawiera ukryte pole`<input type="hidden">`( ) dla numeru kursu. Dodanie `<label>` pomocnika tagu `asp-for="Course.CourseID"` nie eliminuje potrzeby korzystania z ukrytego pola. `<input type="hidden">`jest wymagane, aby numer kursu został uwzględniony w opublikowanych danych, gdy użytkownik kliknie **przycisk Zapisz**.

## <a name="update-the-course-details-and-delete-pages"></a>Aktualizowanie szczegółów kursu i usuwanie stron

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane.

### <a name="update-the-course-page-models"></a>Aktualizowanie modeli stron kursu

Aktualizuj *strony/kursy/usuń.cshtml.cs* z następującym `AsNoTracking`kodem do dodania:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Wprowadzać tę samą zmianę w pliku *Pages/Courses/Details.cshtml.cs:*

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a>Aktualizowanie stron Razor kursu

Zaktualizuj *strony/kursy/usuń.cshtml* za pomocą następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Wprowadzać te same zmiany na stronie Szczegóły.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Testowanie stron kursu

Przetestuj strony tworzenia, edytowania, szczegółów i usuwania.

## <a name="update-the-instructor-create-and-edit-pages"></a>Aktualizowanie stron tworzenia i edytowania instruktora

Instruktorzy mogą nauczać dowolną liczbę kursów. Na poniższej ilustracji przedstawiono stronę edycji instruktora z tablicą pól wyboru kursu.

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses30.png)

Pola wyboru umożliwiają zmiany kursów, do które jest przypisany instruktor. Pole wyboru jest wyświetlane dla każdego kursu w bazie danych. Wybrane są kursy przypisane do instruktora. Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu. Jeśli liczba kursów była znacznie większa, inny interfejs użytkownika może działać lepiej. Ale metoda zarządzania relacją wiele do wielu pokazana tutaj nie zmieni. Aby utworzyć lub usunąć relacje, należy manipulować encją sprzężenia.

### <a name="create-a-class-for-assigned-courses-data"></a>Tworzenie klasy dla przypisanych danych kursów

Utwórz *schoolviewmodels/AssignedCourseData.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

Klasa `AssignedCourseData` zawiera dane do tworzenia pól wyboru dla kursów przypisanych do instruktora.

### <a name="create-an-instructor-page-model-base-class"></a>Tworzenie klasy podstawowej modelu strony instruktora

Utwórz klasę podstawową *Pages/Instructors/InstructorCoursesPageModel.cs:*

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

Jest `InstructorCoursesPageModel` to klasa podstawowa, której użyjesz w modelach stron Edycja i Tworzenie. `PopulateAssignedCourseData`odczytuje `Course` wszystkie jednostki `AssignedCourseDataList`do zapełnienia . Dla każdego kursu kod `CourseID`ustawia , tytuł i czy instruktor jest przypisany do kursu. A [HashSet](/dotnet/api/system.collections.generic.hashset-1) jest używany do efektywnych odnośów.

Ponieważ Razor strona nie ma kolekcji Course jednostek, spinacza modelu nie `CourseAssignments` można automatycznie zaktualizować właściwości nawigacji. Zamiast używać spinacza modelu, `CourseAssignments` aby zaktualizować właściwość nawigacji, `UpdateInstructorCourses` można to zrobić w nowej metodzie. W związku z tym `CourseAssignments` należy wykluczyć właściwość z powiązania modelu. Nie wymaga to żadnych zmian w `TryUpdateModel` kodzie, który wywołuje, ponieważ używasz przeciążenia białej listy i `CourseAssignments` nie znajduje się na liście dołączania.

Jeśli nie wybrano żadnych `UpdateInstructorCourses` pól wyboru, kod inicjuje właściwość `CourseAssignments` nawigacji z pustą kolekcją i zwraca:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kod następnie pętli przez wszystkie kursy w bazie danych i sprawdza każdy kurs względem tych aktualnie przypisanych do instruktora w porównaniu z tymi, które zostały wybrane na stronie. Aby ułatwić efektywne wyszukiwanie, dwie ostatnie `HashSet` kolekcje są przechowywane w obiektach.

Jeśli pole wyboru dla kursu zostało zaznaczone, ale `Instructor.CourseAssignments` kurs nie znajduje się we właściwości nawigacji, kurs jest dodawany do kolekcji we właściwości nawigacji.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Jeśli pole wyboru kursu nie zostało zaznaczone, ale `Instructor.CourseAssignments` kurs znajduje się we właściwości nawigacji, kurs zostanie usunięty z właściwości nawigacji.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Obsługa lokalizacji biura

Inną relacją, którą strona edycji ma do obsługi, jest relacja jeden do `OfficeAssignment` zera lub jeden, która ma jednostkę Instructor z encją. Kod edycji instruktora musi obsługiwać następujące scenariusze: 

* Jeśli użytkownik wyczyści przypisanie biura, `OfficeAssignment` usuń encję.
* Jeśli użytkownik wprowadzi przypisanie biura i jest puste, utwórz nową `OfficeAssignment` encję.
* Jeśli użytkownik zmieni przypisanie biura, zaktualizuj encję. `OfficeAssignment`

### <a name="update-the-instructor-edit-page-model"></a>Aktualizowanie modelu strony Edycja instruktora

Aktualizuj *strony/instruktorzy/edit.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Powyższy kod ma następujące działanie:

* Pobiera `Instructor` bieżącą jednostkę z bazy `OfficeAssignment`danych `CourseAssignment`przy `CourseAssignment.Course` użyciu ładowania chętnych dla , i właściwości nawigacji.
* Aktualizuje pobraną encję `Instructor` wartościami z spinacza modelu. `TryUpdateModel`zapobiega [nadpostowaniu](xref:data/ef-rp/crud#overposting).
* Jeśli lokalizacja biura jest `Instructor.OfficeAssignment` pusta, ustawia wartość null. Gdy `Instructor.OfficeAssignment` wartość null jest zerowa, pokrewny wiersz w `OfficeAssignment` tabeli jest usuwany.
* `PopulateAssignedCourseData` Wywołania `OnGetAsync` w celu zapewnienia informacji o `AssignedCourseData` polach wyboru przy użyciu klasy modelu widoku.
* `UpdateInstructorCourses` Wywołania `OnPostAsync` w celu zastosowania informacji z pól wyboru do instructor jednostki edytowane.
* Połączenia `PopulateAssignedCourseData` `UpdateInstructorCourses` i `OnPostAsync` `TryUpdateModel` w przypadku niepowodzenia. Te wywołania metody przywrócić przypisane dane kursu wprowadzone na stronie, gdy jest ponownie wyświetlany z komunikatem o błędzie.

### <a name="update-the-instructor-edit-razor-page"></a>Aktualizowanie strony Edycja razora instruktora

Aktualizuj *strony/instruktorzy/edit.cshtml* za pomocą następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny. Każda kolumna ma pole wyboru i podpis zawierający numer kursu i tytuł. Wszystkie pola wyboru mają tę samą nazwę ("wybrane Cieki"). Przy użyciu tej samej nazwy informuje spinacza modelu, aby traktować je jako grupę. Atrybut wartości każdego pola wyboru jest `CourseID`ustawiony na . Gdy strona jest księgowane, spinacz modelu przekazuje `CourseID` tablicy, która składa się z wartości tylko dla pól wyboru, które są zaznaczone.

Gdy pola wyboru są początkowo renderowane, kursy przypisane do instruktora są wybierane.

Uwaga: Podejście przyjęte tutaj w celu edycji danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów. Dla kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłoby bardziej użyteczne i wydajne.

Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów. Zmień niektóre zadania kursu. Zmiany zostaną odzwierciedlone na stronie Indeks.

### <a name="update-the-instructor-create-page"></a>Aktualizowanie strony Tworzenie instruktora

Zaktualizuj model strony Tworzenie instruktora i stronę Razor za pomocą kodu podobnego do strony Edytuj:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Przetestuj instruktora Utwórz stronę.

## <a name="update-the-instructor-delete-page"></a>Aktualizowanie strony Usuwanie instruktora

Aktualizuj *strony/instruktorzy/delete.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Poprzedni kod wprowadza następujące zmiany:

* Używa zaakusu dla właściwości `CourseAssignments` nawigacji. `CourseAssignments`muszą zostać uwzględnione lub nie zostaną usunięte po usunięciu instruktora. Aby uniknąć konieczności ich odczytywania, należy skonfigurować usuwanie kaskadowe w bazie danych.

* Jeśli instruktor do usunięcia jest przypisany jako administrator wszystkich działów, usuwa przypisanie instruktora z tych działów.

Uruchom aplikację i przetestuj stronę Usuń.

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/read-related-data)
> [Następny samouczek](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku pokazano aktualizowanie powiązanych danych. Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pobierz instrukcje](xref:index#how-to-download-a-sample).

Na poniższych ilustracjach przedstawiono niektóre z ukończonych stron.

![Strona edycji kursu](update-related-data/_static/course-edit.png)
![Edycja instruktora](update-related-data/_static/instructor-edit-courses.png)

Umożliwia badanie i testowanie stron kursu Tworzenie i edytowanie. Utwórz nowy kurs. Dział jest wybierany przez jego klucz podstawowy (liczba całkowita), a nie jego nazwę. Edytuj nowy kurs. Po zakończeniu testowania usuń nowy kurs.

## <a name="create-a-base-class-to-share-common-code"></a>Tworzenie klasy podstawowej w celu udostępniania wspólnego kodu

Kursy/Tworzenie i kursy/Edytuj strony wymagają listy nazw działów. Utwórz klasę podstawową *Pages/Courses/DepartmentNamePageModel.cs.cs* dla stron Tworzenie i edytowanie:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Poprzedni kod tworzy [SelectList,](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) aby zawierać listę nazw działów. Jeśli `selectedDepartment` jest określony, ten dział `SelectList`jest zaznaczony w .

Klasy modelu strony Utwórz i `DepartmentNamePageModel`edytuj będą pochodzić z .

## <a name="customize-the-courses-pages"></a>Dostosowywanie stron kursów

Po utworzeniu nowej jednostki kursu musi mieć relację z istniejącym działem. Aby dodać dział podczas tworzenia kursu, klasa podstawowa dla tworzenia i edytowania zawiera listę rozwijaną do wybierania działu. Lista rozwijana ustawia `Course.DepartmentID` właściwość klucza obcego (FK). EF Core używa `Course.DepartmentID` FK do `Department` załadowania właściwości nawigacji.

![Tworzenie kursu](update-related-data/_static/ddl.png)

Zaktualizuj model strony Utwórz za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Powyższy kod ma następujące działanie:

* Pochodzi z `DepartmentNamePageModel`.
* Służy `TryUpdateModelAsync` do zapobiegania [nadpostowaniu](xref:data/ef-rp/crud#overposting).
* `ViewData["DepartmentID"]` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).

`ViewData["DepartmentID"]`zastępuje się silnie wpisanym `DepartmentNameSL`. Silnie typizowane modele są preferowane niż słabo wpisane. Aby uzyskać więcej informacji, zobacz [Słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizowanie strony Tworzenie kursów

Zaktualizuj *strony/kursy/Create.cshtml* za pomocą następującego kodu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Poprzedni znacznik wprowadza następujące zmiany:

* Zmienia podpis z **DepartmentID** na **Dział**.
* `"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).
* Dodaje opcję "Wybierz dział". Ta zmiana powoduje, że "Wybierz dział", a nie pierwszy dział.
* Dodaje komunikat sprawdzania poprawności, gdy dział nie jest zaznaczony.

Strona Razor używa [pomocnika wyboru znacznika:](xref:mvc/views/working-with-forms#the-select-tag-helper)

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Przetestuj stronę Utwórz. Na stronie Utwórz wyświetlana jest nazwa działu, a nie identyfikator działu.

### <a name="update-the-courses-edit-page"></a>Zaktualizuj stronę Edycja kursów.

Zastąp kod w *pages/courses/edit.cshtml.cs* następującym kodem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Zmiany są podobne do tych wprowadzonych w modelu strony Tworzenie. W poprzednim kodzie `PopulateDepartmentsDropDownList` przekazuje w identyfikatorze działu, który wybiera dział określony na liście rozwijanej.

Aktualizuj *strony/kursy/edit.cshtml* za pomocą następujących znaczników:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Poprzedni znacznik wprowadza następujące zmiany:

* Wyświetla identyfikator kursu. Ogólnie rzecz biorąc klucz podstawowy (PK) jednostki nie jest wyświetlany. PKs są zwykle bez znaczenia dla użytkowników. W tym przypadku PK jest numerem kursu.
* Zmienia podpis z **DepartmentID** na **Dział**.
* `"ViewBag.DepartmentID"` Zastępuje `DepartmentNameSL` się (z klasy podstawowej).

Strona zawiera ukryte pole`<input type="hidden">`( ) dla numeru kursu. Dodanie `<label>` pomocnika tagu `asp-for="Course.CourseID"` nie eliminuje potrzeby korzystania z ukrytego pola. `<input type="hidden">`jest wymagane, aby numer kursu został uwzględniony w opublikowanych danych, gdy użytkownik kliknie **przycisk Zapisz**.

Przetestuj zaktualizowany kod. Tworzenie, edytowanie i usuwanie kursu.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Dodawanie AsNoTracking do modeli stron Szczegóły i Usuwanie

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane. Dodaj `AsNoTracking` do modelu strony Usuń i Szczegóły. Poniższy kod przedstawia zaktualizowany model strony Usuwania:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Zaktualizuj `OnGetAsync` metodę w pliku *Pages/Courses/Details.cshtml.cs:*

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modyfikowanie stron Usuwanie i Szczegóły

Zaktualizuj stronę Usuń maszynkę do golenia następującymi znacznikami:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Wprowadzać te same zmiany na stronie Szczegóły.

### <a name="test-the-course-pages"></a>Testowanie stron kursu

Przetestuj tworzenie, edytowanie, szczegóły i usuwanie.

## <a name="update-the-instructor-pages"></a>Aktualizowanie stron instruktora

W poniższych sekcjach zaktualizowano strony instruktora.

### <a name="add-office-location"></a>Dodawanie lokalizacji biura

Podczas edytowania rekordu instruktora można zaktualizować przypisanie biura instruktora. Jednostka `Instructor` ma relację jeden do zera lub jeden `OfficeAssignment` z jednostką. Kod instruktora musi obsługiwać:

* Jeśli użytkownik wyczyści przypisanie biura, `OfficeAssignment` usuń encję.
* Jeśli użytkownik wprowadzi przypisanie biura i jest puste, utwórz nową `OfficeAssignment` encję.
* Jeśli użytkownik zmieni przypisanie biura, zaktualizuj encję. `OfficeAssignment`

Zaktualizuj instruktorów Edytuj model strony o następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Powyższy kod ma następujące działanie:

* Pobiera bieżącej `Instructor` jednostki z bazy `OfficeAssignment` danych przy użyciu ładowania eager dla właściwości nawigacji.
* Aktualizuje pobraną encję `Instructor` wartościami z spinacza modelu. `TryUpdateModel`zapobiega [nadpostowaniu](xref:data/ef-rp/crud#overposting).
* Jeśli lokalizacja biura jest `Instructor.OfficeAssignment` pusta, ustawia wartość null. Gdy `Instructor.OfficeAssignment` wartość null jest zerowa, pokrewny wiersz w `OfficeAssignment` tabeli jest usuwany.

### <a name="update-the-instructor-edit-page"></a>Aktualizowanie strony edycji instruktora

Aktualizuj *strony/instruktorzy/edit.cshtml* z lokalizacją biura:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Sprawdź, czy możesz zmienić lokalizację biura instruktorów.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Dodawanie przypisań kursu do strony Edytowanie instruktora

Instruktorzy mogą nauczać dowolną liczbę kursów. W tej sekcji można dodać możliwość zmiany przypisania kursu. Na poniższej ilustracji przedstawiono zaktualizowaną stronę edycji instruktora:

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses.png)

`Course`i `Instructor` ma wiele do wielu relacji. Aby dodać i usunąć relacje, należy dodać `CourseAssignments` i usunąć encje z zestawu encji sprzężenia.

Pola wyboru umożliwiają zmiany kursów przypisanych przez instruktora. Pole wyboru jest wyświetlane dla każdego kursu w bazie danych. Kursy, do których jest przypisany instruktor, są sprawdzane. Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu. Jeśli liczba kursów była znacznie większa:

* Prawdopodobnie użyjesz innego interfejsu użytkownika, aby wyświetlić kursy.
* Metoda manipulowania jednostką sprzężenia w celu tworzenia lub usuwania relacji nie ulegnie zmianie.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Dodawanie klas do obsługi stron instruktora tworzenia i edytowania

Utwórz *schoolviewmodels/AssignedCourseData.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Klasa `AssignedCourseData` zawiera dane do tworzenia pól wyboru dla przypisanych kursów przez instruktora.

Utwórz klasę podstawową *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs:*

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

Jest `InstructorCoursesPageModel` to klasa podstawowa, której użyjesz w modelach stron Edycja i Tworzenie. `PopulateAssignedCourseData`odczytuje `Course` wszystkie jednostki `AssignedCourseDataList`do zapełnienia . Dla każdego kursu kod `CourseID`ustawia , tytuł i czy instruktor jest przypisany do kursu. A [HashSet](/dotnet/api/system.collections.generic.hashset-1) służy do tworzenia efektywnych odnośów.

### <a name="instructors-edit-page-model"></a>Instruktorzy Edytuj model strony

Zaktualizuj model strony edycji instruktora o następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Poprzedni kod obsługuje zmiany przypisania biura.

Zaktualizuj instruktora Razor View:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Po wklejeniu kodu w programie Visual Studio podziały wierszy są zmieniane w sposób, który przerywa kod. Naciśnij jednokrotnie klawisze Ctrl+Z, aby cofnąć automatyczne formatowanie. Ctrl+Z naprawia podziały wierszy tak, aby wyglądały jak to, co widzisz tutaj. Wcięcie nie musi być doskonałe, ale `@:</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` i linie muszą być w jednej linii, jak pokazano. Po wybraniu bloku nowego kodu naciśnij trzykrotnie klawisz Tab, aby wyrównać nowy kod z istniejącym kodem. Zagłosuj lub przejrzyj stan tego błędu [za pomocą tego linku](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny. Każda kolumna ma pole wyboru i podpis zawierający numer kursu i tytuł. Wszystkie pola wyboru mają tę samą nazwę ("wybrane Cieki"). Przy użyciu tej samej nazwy informuje spinacza modelu, aby traktować je jako grupę. Atrybut wartości każdego pola wyboru jest `CourseID`ustawiony na . Gdy strona jest księgowane, spinacz modelu przekazuje `CourseID` tablicy, która składa się z wartości tylko dla pól wyboru, które są zaznaczone.

Gdy pola wyboru są początkowo renderowane, kursy przypisane do instruktora mają sprawdzone atrybuty.

Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów. Zmień niektóre zadania kursu. Zmiany zostaną odzwierciedlone na stronie Indeks.

Uwaga: Podejście przyjęte tutaj w celu edycji danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów. Dla kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłoby bardziej użyteczne i wydajne.

### <a name="update-the-instructors-create-page"></a>Aktualizowanie strony Tworzenie instruktorów

Zaktualizuj instruktora Utwórz model strony o następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Powyższy kod jest podobny do kodu *Pages/Instructors/Edit.cshtml.cs.*

Zaktualizuj stronę utwórz maszynkę do golenia instruktorem za pomocą następujących znaczników:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Przetestuj instruktora Utwórz stronę.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

Zaktualizuj model strony Usuń o następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Poprzedni kod wprowadza następujące zmiany:

* Używa zaakusu dla właściwości `CourseAssignments` nawigacji. `CourseAssignments`muszą zostać uwzględnione lub nie zostaną usunięte po usunięciu instruktora. Aby uniknąć konieczności ich odczytywania, należy skonfigurować usuwanie kaskadowe w bazie danych.

* Jeśli instruktor do usunięcia jest przypisany jako administrator wszystkich działów, usuwa przypisanie instruktora z tych działów.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja YouTube tego samouczka (część 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Wersja YouTube tego samouczka (część 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/read-related-data)
> [następny](xref:data/ef-rp/concurrency)

::: moniker-end
