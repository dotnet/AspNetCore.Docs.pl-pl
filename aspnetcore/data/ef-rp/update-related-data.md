---
title: Część 7 Razor strony z EF Core w programie ASP.NET Core — aktualizacja powiązanych danych
author: rick-anderson
description: Część 7 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3ec88a862697c540a1a98e733c31d76922f81f7c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060537"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>Część 7 Razor strony z EF Core w programie ASP.NET Core — aktualizacja powiązanych danych

Przez [Tomasz Dykstra](https://github.com/tdykstra)i [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku pokazano, jak zaktualizować powiązane dane. Na poniższych ilustracjach przedstawiono niektóre z ukończonych stron.

![Strona edycji kursów — Edytuj ](update-related-data/_static/course-edit30.png)
 ![ stronę](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aktualizowanie stron tworzenie i edytowanie kursu

Kod szkieletowy dla stron tworzenie i edytowanie danych zawiera listę rozwijaną dział, która zawiera identyfikator działu (liczba całkowita). Lista rozwijana powinna zawierać nazwę działu, więc obie te strony muszą mieć listę nazw działów. Aby zapewnić tę listę, użyj klasy bazowej dla stron tworzenia i edytowania.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Tworzenie klasy bazowej do tworzenia i edytowania kursu

Utwórz plik *Pages/kurss/DepartmentNamePageModel. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Poprzedni kod tworzy [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) , aby zawierał listę nazw działów. Jeśli `selectedDepartment` jest określony, ten dział jest wybierany w `SelectList` .

Klasy Utwórz i edytuj model strony będą pochodzić od `DepartmentNamePageModel` .

### <a name="update-the-course-create-page-model"></a>Aktualizuj model tworzenia strony kursu

Kurs jest przypisywany do działu. Klasa bazowa dla stron tworzenia i edytowania umożliwia `SelectList` wybranie działu. Lista rozwijana korzystająca z `SelectList` `Course.DepartmentID` właściwości ustawia klucz obcy (FK). EF Core używa `Course.DepartmentID` klucza obcego do załadowania `Department` właściwości nawigacji.

![Utwórz kurs](update-related-data/_static/ddl30.png)

Zaktualizuj *strony/kursy/Utwórz. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Powyższy kod ma następujące działanie:

* Pochodzi od `DepartmentNamePageModel` .
* Używa `TryUpdateModelAsync` do zapobiegania [](xref:data/ef-rp/crud#overposting)przepisywaniu.
* Usuwa `ViewData["DepartmentID"]` . `DepartmentNameSL` z klasy podstawowej jest jednoznacznie określonym modelem i będzie używany przez Razor stronę. Modele silnie wpisane są preferowane za pośrednictwem słabo wpisanych. Aby uzyskać więcej informacji, zobacz [słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-no-locrazor-page"></a>Aktualizowanie strony tworzenia kursu Razor

Zaktualizuj *strony/kursy/Utwórz. cshtml* przy użyciu następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Poprzedni kod wprowadza następujące zmiany:

* Zmienia podpis z **DepartmentID** na **dział**.
* Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).
* Dodaje opcję "Wybierz dział". Ta zmiana renderuje "Select Department" na liście rozwijanej, gdy nie wybrano jeszcze żadnego działu, a nie pierwszego działu.
* Dodaje komunikat weryfikacyjny, gdy nie wybrano działu.

RazorStrona używa [pomocnika wybierania tagu](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Przetestuj stronę tworzenie. Na stronie Tworzenie zostanie wyświetlona nazwa działu, a nie identyfikator działu.

### <a name="update-the-course-edit-page-model"></a>Aktualizowanie modelu strony edytowania kursu

Zaktualizuj *strony/kursy/Edytuj. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Zmiany są podobne do tych, które zostały wprowadzone w modelu tworzenia strony. W poprzednim kodzie program `PopulateDepartmentsDropDownList` przekazuje identyfikator działu, który wybiera ten dział z listy rozwijanej.

### <a name="update-the-course-edit-no-locrazor-page"></a>Aktualizowanie strony edytowania kursu Razor

Aktualizowanie *stron/kursów/Edit. cshtml* przy użyciu następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Poprzedni kod wprowadza następujące zmiany:

* Wyświetla identyfikator kursu. Zazwyczaj klucz podstawowy (PK) jednostki nie jest wyświetlany. PKs są zwykle oznaczane przez użytkowników. W tym przypadku klucz podstawowy jest numerem kursu.
* Zmienia podpis dla listy rozwijanej działu od **DepartmentID** do **działu**.
* Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).

Ta strona zawiera ukryte pole ( `<input type="hidden">` ) dla numeru kursu. Dodanie `<label>` pomocnika tagów z `asp-for="Course.CourseID"` nie eliminuje potrzeby pola ukrytego. `<input type="hidden">` jest wymagana do uwzględnienia numeru kursu w opublikowanych danych, gdy użytkownik kliknie przycisk **Zapisz**.

## <a name="update-the-course-details-and-delete-pages"></a>Aktualizowanie szczegółów kursu i stron usuwania

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane.

### <a name="update-the-course-page-models"></a>Aktualizowanie modeli stron kursu

Zaktualizuj *strony/kursy/Delete. cshtml. cs* przy użyciu następującego kodu do dodania `AsNoTracking` :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Wprowadź tę samą zmianę w pliku *Pages/kurss/details. cshtml. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>Aktualizowanie stron kursu Razor

Zaktualizuj *strony/kursy/Delete. cshtml* przy użyciu następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Wprowadź te same zmiany na stronie Szczegóły.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Testowanie stron kursów

Przetestuj strony tworzenie, edytowanie, szczegóły i usuwanie.

## <a name="update-the-instructor-create-and-edit-pages"></a>Aktualizowanie strony instruktora tworzenie i edytowanie stron

Instruktorzy mogą uczyć się dowolnej liczby kursów. Na poniższej ilustracji przedstawiono stronę Edytowanie instruktora z tablicą pól wyboru kursu.

![Instruktor strony edytowania za pomocą kursów](update-related-data/_static/instructor-edit-courses30.png)

Pola wyboru umożliwiają zmianę kursów, do których zostanie przypisany instruktor. Pole wyboru jest wyświetlane dla każdego kursu w bazie danych. Wybrane są kursy, do których przypisano instruktora. Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu. Jeśli liczba kursów była znacznie większa, może to poprawić inny interfejs użytkownika. Jednak metoda zarządzania relacją wiele-do-wielu pokazana tutaj nie zmieniła się. Aby utworzyć lub usunąć relacje, można manipulować jednostką sprzężenia.

### <a name="create-a-class-for-assigned-courses-data"></a>Utwórz klasę dla danych przypisanych kursów

Utwórz *SchoolViewModels/AssignedCourseData. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData`Klasa zawiera dane, aby utworzyć pola wyboru dla kursów przypisanych do instruktora.

### <a name="create-an-instructor-page-model-base-class"></a>Utwórz klasę bazową modelu strony instruktora

Utwórz klasę bazową *stron/instruktorów/InstructorCoursesPageModel. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel`Jest klasą bazową, która będzie używana dla modeli stron Edycja i tworzenie. `PopulateAssignedCourseData` odczytuje wszystkie `Course` jednostki do wypełnienia `AssignedCourseDataList` . Dla każdego kursu kod ustawia `CourseID` , tytuł i określa, czy instruktor jest przypisany do kursu. [HashSet —](/dotnet/api/system.collections.generic.hashset-1) jest używany do wydajnego wyszukiwania.

Ponieważ Razor Strona nie zawiera kolekcji jednostek kursu, spinacz modelu nie może automatycznie zaktualizować `CourseAssignments` właściwości nawigacji. Zamiast używać spinacza modelu do aktualizowania `CourseAssignments` właściwości nawigacji, należy to zrobić w nowej `UpdateInstructorCourses` metodzie. W związku z tym należy wykluczyć `CourseAssignments` Właściwość z powiązania modelu. Nie wymaga żadnych zmian w kodzie, który wywołuje, `TryUpdateModel` ponieważ używasz przeciążenia z zadeklarowanymi właściwościami i `CourseAssignments` nie znajduje się na liście dołączania.

Jeśli nie wybrano żadnych pól wyboru, kod w `UpdateInstructorCourses` inicjuje `CourseAssignments` Właściwość nawigacji z pustą kolekcją i zwraca:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kod następnie przechodzi między wszystkimi kursami w bazie danych i sprawdza każdy kurs w odniesieniu do tych, które są aktualnie przypisane do instruktora, a także do tych, które zostały wybrane na stronie. Aby ułatwić efektywne wyszukiwanie, te dwie kolekcje są przechowywane w `HashSet` obiektach.

Jeśli pole wyboru dla kursu zostało zaznaczone, ale kurs nie jest we `Instructor.CourseAssignments` właściwości nawigacji, kurs zostanie dodany do kolekcji we właściwości nawigacji.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Jeśli nie wybrano pola wyboru dla kursu, ale kurs jest we `Instructor.CourseAssignments` właściwości nawigacji, kurs jest usuwany z właściwości nawigacji.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Obsługa lokalizacji biura

Inna relacja, którą Strona Edytuj musi obsłużyć, jest relacją "jeden do zera" lub "jeden", którą jednostka instruktora ma z `OfficeAssignment` jednostką. Program instruktora edytuje kod musi obsługiwać następujące scenariusze: 

* Jeśli użytkownik wyczyści przypisanie pakietu Office, Usuń `OfficeAssignment` jednostkę.
* Jeśli użytkownik wprowadzi przypisanie do pakietu Office i jest puste, należy utworzyć nową `OfficeAssignment` jednostkę.
* Jeśli użytkownik zmieni przypisanie pakietu Office, zaktualizuj `OfficeAssignment` jednostkę.

### <a name="update-the-instructor-edit-page-model"></a>Aktualizowanie modelu strony przez instruktora

Aktualizowanie *stron/instruktorów/Edit. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Powyższy kod ma następujące działanie:

* Pobiera bieżącą `Instructor` jednostkę z bazy danych przy użyciu eager ładowania dla `OfficeAssignment` `CourseAssignment` właściwości nawigacji, i `CourseAssignment.Course` .
* Aktualizuje pobraną `Instructor` jednostkę z wartościami ze spinacza modelu. `TryUpdateModel`zapobiega [](xref:data/ef-rp/crud#overposting)zastępowaniu.
* Jeśli lokalizacja biura jest pusta, ustawia `Instructor.OfficeAssignment` wartość na null. Gdy `Instructor.OfficeAssignment` ma wartość null, powiązany wiersz w `OfficeAssignment` tabeli jest usuwany.
* Wywołuje `PopulateAssignedCourseData` w `OnGetAsync` celu podania informacji dla pól wyboru przy użyciu `AssignedCourseData` klasy model widoku.
* Wywołuje `UpdateInstructorCourses` program `OnPostAsync` , aby zastosować informacje z pól wyboru do edytowanej jednostki instruktora.
* Wywołania `PopulateAssignedCourseData` i `UpdateInstructorCourses` w `OnPostAsync` przypadku `TryUpdateModel` niepowodzenia. Te wywołania metody powodują przywrócenie przypisanych danych kursu wprowadzonych na stronie, gdy jest on ponownie wyświetlany z komunikatem o błędzie.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>Aktualizowanie strony edytowania instruktora Razor

Aktualizowanie *stron/instruktorów/Edit. cshtml* przy użyciu następującego kodu:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny. Każda kolumna ma pole wyboru i podpis zawierający numer i tytuł kursu. Wszystkie pola wyboru mają taką samą nazwę ("selectedCourses"). Użycie tej samej nazwy informuje spinacz modelu, aby traktować go jako grupę. Atrybut value każdego pola wyboru jest ustawiony na `CourseID` . Po opublikowaniu strony spinacz modelu przekaże tablicę, która składa się z `CourseID` wartości tylko wybranych pól wyboru.

Gdy pola wyboru są początkowo renderowane, wybierane są kursy przypisane do instruktora.

Uwaga: podejście podjęte tutaj do edytowania danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów. W przypadku kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłyby bardziej użyteczny i wydajny.

Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów. Zmień niektóre przypisania kursu. Zmiany zostaną odzwierciedlone na stronie indeksu.

### <a name="update-the-instructor-create-page"></a>Aktualizowanie strony tworzenia instruktora

Zaktualizuj program instruktora Utwórz model strony i Razor stronę z kodem podobnym do strony edytowania:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Przetestuj stronę tworzenie instruktora.

## <a name="update-the-instructor-delete-page"></a>Aktualizuj stronę usuwania instruktora

Zaktualizuj *strony/instruktorów/Delete. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Poprzedni kod wprowadza następujące zmiany:

* Używa ładowania eager dla `CourseAssignments` właściwości nawigacji. `CourseAssignments` musi być dołączony lub nie jest usuwany po usunięciu instruktora. Aby uniknąć konieczności ich odczytywania, skonfiguruj kaskadowe usuwanie w bazie danych.

* Jeśli instruktor zostanie usunięty, zostanie przypisany jako administrator jakichkolwiek działów, program usunie przypisanie instruktora z tych urzędów.

Uruchom aplikację i Przetestuj stronę usuwania.

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/read-related-data) 
>  [Następny samouczek](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku pokazano, jak aktualizować powiązane dane. Jeśli występują problemy, których nie można rozwiązać, [Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instrukcje pobierania](xref:index#how-to-download-a-sample).

Na poniższych ilustracjach przedstawiono niektóre z ukończonych stron.

![Strona edycji kursów — Edytuj ](update-related-data/_static/course-edit.png)
 ![ stronę](update-related-data/_static/instructor-edit-courses.png)

Sprawdź i przetestuj strony Tworzenie i edytowanie kursu. Utwórz nowy kurs. Dział jest wybierany przez jego klucz podstawowy (liczba całkowita), a nie jego nazwę. Edytuj nowy kurs. Po zakończeniu testowania Usuń nowy kurs.

## <a name="create-a-base-class-to-share-common-code"></a>Utwórz klasę bazową, aby udostępnić wspólny kod

Wszystkie strony kursy/tworzenie i kursy/Edycja muszą mieć listę nazw działów. Utwórz klasę bazową *stron/kursów/DepartmentNamePageModel. cshtml. cs* dla stron tworzenia i edytowania:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Poprzedni kod tworzy [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) , aby zawierał listę nazw działów. Jeśli `selectedDepartment` jest określony, ten dział jest wybierany w `SelectList` .

Klasy Utwórz i edytuj model strony będą pochodzić od `DepartmentNamePageModel` .

## <a name="customize-the-courses-pages"></a>Dostosowywanie stron kursów

Po utworzeniu nowej jednostki kursu musi ona mieć relację z istniejącym działem. Aby dodać dział podczas tworzenia kursu, Klasa bazowa do tworzenia i edycji zawiera listę rozwijaną umożliwiającą wybranie działu. Lista rozwijana ustawia `Course.DepartmentID` Właściwość klucz obcy (FK). EF Core używa `Course.DepartmentID` klucza obcego do załadowania `Department` właściwości nawigacji.

![Utwórz kurs](update-related-data/_static/ddl.png)

Zaktualizuj model tworzenia strony przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Powyższy kod ma następujące działanie:

* Pochodzi od `DepartmentNamePageModel` .
* Używa `TryUpdateModelAsync` do zapobiegania [](xref:data/ef-rp/crud#overposting)przepisywaniu.
* Zamienia wartość `ViewData["DepartmentID"]` na `DepartmentNameSL` (z klasy bazowej).

`ViewData["DepartmentID"]` jest zastępowany silną typem `DepartmentNameSL` . Modele silnie wpisane są preferowane za pośrednictwem słabo wpisanych. Aby uzyskać więcej informacji, zobacz [słabo wpisane dane (ViewData i ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizowanie strony Tworzenie kursów

Zaktualizuj *strony/kursy/Utwórz. cshtml* przy użyciu następującego kodu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Poprzedzające znaczniki wprowadzają następujące zmiany:

* Zmienia podpis z **DepartmentID** na **dział**.
* Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).
* Dodaje opcję "Wybierz dział". Ta zmiana renderuje "Select Department" zamiast pierwszego działu.
* Dodaje komunikat weryfikacyjny, gdy nie wybrano działu.

RazorStrona używa [pomocnika wybierania tagu](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Przetestuj stronę tworzenie. Na stronie Tworzenie zostanie wyświetlona nazwa działu, a nie identyfikator działu.

### <a name="update-the-courses-edit-page"></a>Zaktualizuj stronę Edytowanie kursów.

Zastąp kod w obszarze *Pages/kursys/Edit. cshtml. cs* następującym kodem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Zmiany są podobne do tych, które zostały wprowadzone w modelu tworzenia strony. W poprzednim kodzie program `PopulateDepartmentsDropDownList` przekazuje identyfikator działu, który wybierze dział określony na liście rozwijanej.

Aktualizowanie *stron/kursów/Edit. cshtml* przy użyciu następującego znacznika:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Poprzedzające znaczniki wprowadzają następujące zmiany:

* Wyświetla identyfikator kursu. Zazwyczaj klucz podstawowy (PK) jednostki nie jest wyświetlany. PKs są zwykle oznaczane przez użytkowników. W tym przypadku klucz podstawowy jest numerem kursu.
* Zmienia podpis z **DepartmentID** na **dział**.
* Zamienia wartość `"ViewBag.DepartmentID"` na `DepartmentNameSL` (z klasy bazowej).

Ta strona zawiera ukryte pole ( `<input type="hidden">` ) dla numeru kursu. Dodanie `<label>` pomocnika tagów z `asp-for="Course.CourseID"` nie eliminuje potrzeby pola ukrytego. `<input type="hidden">` jest wymagana do uwzględnienia numeru kursu w opublikowanych danych, gdy użytkownik kliknie przycisk **Zapisz**.

Przetestuj zaktualizowany kod. Tworzenie, edytowanie i usuwanie kursu.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Dodawanie AsNoTracking do modeli szczegółów i stron usuwania

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) może zwiększyć wydajność, gdy śledzenie nie jest wymagane. Dodaj `AsNoTracking` do modelu strony usuwanie i szczegóły. Poniższy kod przedstawia zaktualizowany model strony usuwania:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Zaktualizuj `OnGetAsync` metodę w pliku *Pages/kursów/details. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modyfikowanie stron usuwania i szczegółów

Zaktualizuj stronę usuwania Razor przy użyciu następującej adjustacji:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Wprowadź te same zmiany na stronie Szczegóły.

### <a name="test-the-course-pages"></a>Testowanie stron kursów

Testowanie tworzenia, edytowania, szczegółów i usuwania.

## <a name="update-the-instructor-pages"></a>Aktualizowanie stron instruktora

W poniższych sekcjach zostały zaktualizowane strony instruktora.

### <a name="add-office-location"></a>Dodaj lokalizację biura

Podczas edytowania rekordu instruktora warto zaktualizować przypisanie biura instruktora. `Instructor`Jednostka ma relację jeden do zera lub jeden z `OfficeAssignment` jednostką. Kod instruktora musi obsłużyć:

* Jeśli użytkownik wyczyści przypisanie pakietu Office, Usuń `OfficeAssignment` jednostkę.
* Jeśli użytkownik wprowadzi przypisanie do pakietu Office i jest puste, należy utworzyć nową `OfficeAssignment` jednostkę.
* Jeśli użytkownik zmieni przypisanie pakietu Office, zaktualizuj `OfficeAssignment` jednostkę.

Zaktualizuj program instruktors Edytuj model strony przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Powyższy kod ma następujące działanie:

* Pobiera bieżącą `Instructor` jednostkę z bazy danych przy użyciu eager ładowania dla `OfficeAssignment` właściwości nawigacji.
* Aktualizuje pobraną `Instructor` jednostkę z wartościami ze spinacza modelu. `TryUpdateModel`zapobiega [](xref:data/ef-rp/crud#overposting)zastępowaniu.
* Jeśli lokalizacja biura jest pusta, ustawia `Instructor.OfficeAssignment` wartość na null. Gdy `Instructor.OfficeAssignment` ma wartość null, powiązany wiersz w `OfficeAssignment` tabeli jest usuwany.

### <a name="update-the-instructor-edit-page"></a>Aktualizowanie strony edytowania instruktora

Aktualizowanie *stron/instruktorów/Edit. cshtml* w lokalizacji biura:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Sprawdź, czy można zmienić lokalizację biura instruktorów.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Dodawanie przypisań kursu do strony edytowania instruktora

Instruktorzy mogą uczyć się dowolnej liczby kursów. W tej sekcji dodasz możliwość zmiany przypisań kursu. Na poniższej ilustracji przedstawiono zaktualizowaną stronę edycji instruktora:

![Instruktor strony edytowania za pomocą kursów](update-related-data/_static/instructor-edit-courses.png)

`Course` i `Instructor` ma relację wiele-do-wielu. Aby dodać i usunąć relacje, należy dodać i usunąć jednostki z `CourseAssignments` zestawu jednostek sprzężenia.

Pola wyboru umożliwiają zmianę kursów, do których zostanie przypisany instruktor. Pole wyboru jest wyświetlane dla każdego kursu w bazie danych. Kursy, do których jest przypisany instruktor, są sprawdzane. Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu. Jeśli liczba kursów była znacznie większa:

* Prawdopodobnie używasz innego interfejsu użytkownika do wyświetlania kursów.
* Metoda manipulowania jednostką sprzężenia w celu tworzenia lub usuwania relacji nie ulegnie zmianie.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Dodaj klasy do obsługi tworzenia i edytowania stron instruktorów

Utwórz *SchoolViewModels/AssignedCourseData. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData`Klasa zawiera dane, aby utworzyć pola wyboru dla przypisanych kursów przez instruktora.

Utwórz klasę bazową *stron/instruktorów/InstructorCoursesPageModel. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel`Jest klasą bazową, która będzie używana dla modeli stron Edycja i tworzenie. `PopulateAssignedCourseData` odczytuje wszystkie `Course` jednostki do wypełnienia `AssignedCourseDataList` . Dla każdego kursu kod ustawia `CourseID` , tytuł i określa, czy instruktor jest przypisany do kursu. [HashSet —](/dotnet/api/system.collections.generic.hashset-1) jest używany do tworzenia wydajnych wyszukiwań.

### <a name="instructors-edit-page-model"></a>Instruktorzy edytują model strony

Zaktualizuj model strony instruktora do edycji przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Poprzedni kod obsługuje zmiany przypisania pakietu Office.

Aktualizuj widok instruktora Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Gdy wkleisz kod w programie Visual Studio, podziały wierszy są zmieniane w sposób, który przerywa kod. Naciśnij klawisze Ctrl + Z po raz, aby cofnąć automatyczne formatowanie. Kombinacja klawiszy Ctrl + Z naprawia podziały wierszy, aby wyglądały tak, jak widać w tym miejscu. Wcięcie nie musi być doskonałe, ale `@:</tr><tr>` linie,, `@:<td>` `@:</td>` i `@:</tr>` muszą znajdować się w jednym wierszu, jak pokazano. Po wybraniu bloku nowego kodu naciśnij klawisz Tab trzy razy, aby wyrównać nowy kod z istniejącym kodem. Zagłosuj lub Sprawdź stan tej usterki [za pomocą tego linku](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Poprzedni kod tworzy tabelę HTML, która ma trzy kolumny. Każda kolumna ma pole wyboru i podpis zawierający numer i tytuł kursu. Wszystkie pola wyboru mają tę samą nazwę ("selectedCourses"). Użycie tej samej nazwy informuje spinacz modelu, aby traktować go jako grupę. Atrybut value każdego pola wyboru jest ustawiony na `CourseID` . Po opublikowaniu strony spinacz modelu przekaże tablicę, która składa się z `CourseID` wartości tylko wybranych pól wyboru.

Gdy pola wyboru są początkowo renderowane, kursy przypisane do instruktora mają zaznaczone atrybuty.

Uruchom aplikację i przetestuj zaktualizowaną stronę edycji instruktorów. Zmień niektóre przypisania kursu. Zmiany zostaną odzwierciedlone na stronie indeksu.

Uwaga: podejście podjęte tutaj do edytowania danych kursu instruktora działa dobrze, gdy istnieje ograniczona liczba kursów. W przypadku kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji byłyby bardziej użyteczny i wydajny.

### <a name="update-the-instructors-create-page"></a>Aktualizuj stronę tworzenia instruktorów

Zaktualizuj program instruktora Tworzenie modelu strony przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Poprzedni kod jest podobny do kodu *stron/instruktorów/Edit. cshtml. cs* .

Zaktualizuj stronę tworzenia instruktora Razor , korzystając z następującej adjustacji:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Przetestuj stronę tworzenie instruktora.

## <a name="update-the-delete-page"></a>Aktualizowanie strony usuwania

Zaktualizuj model usuwania stron przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Poprzedni kod wprowadza następujące zmiany:

* Używa ładowania eager dla `CourseAssignments` właściwości nawigacji. `CourseAssignments` musi być dołączony lub nie jest usuwany po usunięciu instruktora. Aby uniknąć konieczności ich odczytywania, skonfiguruj kaskadowe usuwanie w bazie danych.

* Jeśli instruktor zostanie usunięty, zostanie przypisany jako administrator jakichkolwiek działów, program usunie przypisanie instruktora z tych urzędów.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja usługi YouTube w tym samouczku (część 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Wersja usługi YouTube w tym samouczku (część 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/read-related-data) 
>  [Dalej](xref:data/ef-rp/concurrency)

::: moniker-end
