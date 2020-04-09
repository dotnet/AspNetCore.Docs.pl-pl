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
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Samouczek: Aktualizacja powiązanych danych - ASP.NET MVC z EF Core

W poprzednim samouczku wyświetlane powiązane dane; w tym samouczku zaktualizujesz powiązane dane, aktualizując pola klucza obcego i właściwości nawigacji.

Na poniższych ilustracjach przedstawiono niektóre strony, z którymi będziesz pracować.

![Strona edycji kursu](update-related-data/_static/course-edit.png)

![Strona edycji instruktora](update-related-data/_static/instructor-edit-courses.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dostosowywanie stron kursów
> * Strona Edycja dodaj instruktorów
> * Dodawanie kursów do strony Edycja
> * Aktualizuj stronę Usuwania
> * Dodawanie lokalizacji i kursów w biurze do strony Tworzenie

## <a name="prerequisites"></a>Wymagania wstępne

* [Odczytywanie powiązanych danych](read-related-data.md)

## <a name="customize-courses-pages"></a>Dostosowywanie stron kursów

Po utworzeniu nowej jednostki kursu musi mieć relację z istniejącym działem. Aby to ułatwić, szkieletowy kod zawiera metody kontrolera i Tworzenie i edytowanie widoków, które zawierają listę rozwijaną do wybierania działu. Lista rozwijana ustawia `Course.DepartmentID` właściwość klucza obcego i to wszystko entity framework `Department` potrzebuje w celu załadowania właściwości nawigacji z odpowiednią jednostką działu. Użyjesz kodu szkieletu, ale zmień go nieznacznie, aby dodać obsługę błędów i posortować listę rozwijaną.

W *CoursesController.cs*usuń cztery metody Tworzenia i edycji i zastąp je następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Po `Edit` HttpPost metody, utwórz nową metodę, która ładuje informacje o dziale dla listy rozwijanej.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Metoda `PopulateDepartmentsDropDownList` pobiera listę wszystkich działów posortowane `SelectList` według nazwy, tworzy kolekcję dla listy rozwijanej i przekazuje kolekcję do widoku w `ViewBag`. Metoda akceptuje parametr `selectedDepartment` opcjonalny, który umożliwia kod wywołujący, aby określić element, który zostanie wybrany podczas renderowania listy rozwijanej. Widok przekaże nazwę "DepartmentID" `<select>` do pomocnika tagu, a pomocnik następnie `ViewBag` wie, `SelectList` aby spojrzeć w obiekcie na nazwany "DepartmentID".

HttpGet `Create` Metoda wywołuje `PopulateDepartmentsDropDownList` metodę bez ustawiania wybranego elementu, ponieważ dla nowego kursu dział nie jest jeszcze ustanowiony:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet `Edit` Metoda ustawia wybrany element, na podstawie identyfikatora działu, który jest już przypisany do kursu edytowane:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Metody HttpPost dla `Create` `Edit` obu, a także kod, który ustawia wybrany element, gdy ponownie wyświetlić stronę po błędzie. Gwarantuje to, że gdy strona zostanie ponownie wyświetlona, aby wyświetlić komunikat o błędzie, niezależnie od wybranego działu pozostaje zaznaczone.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Dodaj. AsNoTracking do szczegółów i metody usuwania

Aby zoptymalizować wydajność szczegóły kursu i `AsNoTracking` usuń strony, dodaj wywołania w `Details` i HttpGet `Delete` metody.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Modyfikowanie widoków kursu

W *obszarze Widoki/Kursy/Utwórz.cshtml*dodaj opcję "Wybierz dział" do listy rozwijanej **Dział,** zmień podpis z **Identyfikatora departamentu** na **Dział**i dodaj komunikat sprawdzania poprawności.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

W *obszarze Widoki/Kursy/Edit.cshtml*należy wprowadzić tę samą zmianę w polu Dział, co pole *Create.cshtml*.

Również w *obszarze Widoki/Kursy/Edit.cshtml*dodaj pole numer kursu przed polem **Tytuł.** Ponieważ numer kursu jest kluczem podstawowym, jest wyświetlany, ale nie można go zmienić.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Istnieje już ukryte pole`<input type="hidden">`( ) dla numeru kursu w widoku Edycja. Dodanie `<label>` pomocnika znacznika nie eliminuje potrzeby korzystania z ukrytego pola, ponieważ nie powoduje, że numer kursu zostanie uwzględniony w opublikowanych danych, gdy użytkownik kliknie **przycisk Zapisz** na stronie **Edytuj.**

W *obszarze Widoki/Kursy/Usuń.cshtml*dodaj pole numeru kursu u góry i zmień identyfikator działu do nazwy działu.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

W *widokach/kursach/details.cshtml*należy wprowadzić tę samą zmianę, co w przypadku *pliku Delete.cshtml*.

### <a name="test-the-course-pages"></a>Testowanie stron kursu

Uruchom aplikację, wybierz kartę **Kursy,** kliknij pozycję **Utwórz nowy**i wprowadź dane dla nowego kursu:

![Strona Utwór utwórz kurs](update-related-data/_static/course-create.png)

Kliknij przycisk **Utwórz**. Strona Indeks kursów jest wyświetlana z nowym kursem dodanym do listy. Nazwa działu na liście strony Indeks pochodzi z właściwości nawigacji, pokazując, że relacja została ustanowiona poprawnie.

Kliknij **pozycję Edytuj** kurs na stronie Indeks kursów.

![Strona edycji kursu](update-related-data/_static/course-edit.png)

Zmienianie danych na stronie i **klikanie przycisku Zapisz**. Strona Indeks kursów jest wyświetlana ze zaktualizowanymi danymi kursu.

## <a name="add-instructors-edit-page"></a>Strona Edycja dodaj instruktorów

Podczas edytowania rekordu instruktora chcesz mieć możliwość aktualizacji przypisania biura instruktora. Jednostka Instructor ma relację jeden do zera lub jeden z OfficeAssignment jednostki, co oznacza, że kod musi obsługiwać następujące sytuacje:

* Jeśli użytkownik czyści przypisanie biura i pierwotnie miał wartość, usuń OfficeAssignment jednostki.

* Jeśli użytkownik wprowadzi wartość przypisania biura i pierwotnie była pusta, utwórz nową encję OfficeAssignment.

* Jeśli użytkownik zmieni wartość przypisania biura, zmień wartość w istniejącej encji OfficeAssignment.

### <a name="update-the-instructors-controller"></a>Aktualizowanie kontrolera instruktorów

W *InstructorsController.cs*, zmień kod w HttpGet `Edit` metody tak, aby załadować `OfficeAssignment` Instructor właściwości `AsNoTracking`nawigacji jednostki i wywołuje:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Zastąp `Edit` metodę HttpPost następującym kodem do obsługi aktualizacji przypisania biura:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Kod wykonuje następujące czynności:

* Zmienia nazwę metody, `EditPost` ponieważ podpis jest teraz taki `Edit` sam `ActionName` jak HttpGet metody `/Edit/` (atrybut określa, że adres URL jest nadal używany).

* Pobiera bieżącą instructor jednostki z bazy `OfficeAssignment` danych przy użyciu ładowania eager dla właściwości nawigacji. Jest to taka sama, jak to, co zrobiłeś w HttpGet `Edit` metody.

* Aktualizuje pobraną jednostkę Instructor z wartościami z spinacza modelu. Przeciążenie `TryUpdateModel` umożliwia umieszczanie na białej liście właściwości, które chcesz uwzględnić. Zapobiega to nadmiernemu publikowaniu, jak wyjaśniono w [drugim tutorialu](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Jeśli lokalizacja biura jest pusta, ustawia Instructor.OfficeAssignment właściwość null tak, aby powiązany wiersz w OfficeAssignment tabeli zostaną usunięte.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Zapisuje zmiany w bazie danych.

### <a name="update-the-instructor-edit-view"></a>Aktualizowanie widoku edycji instruktora

W *obszarze Widoki/Instruktorzy/Edit.cshtml*dodaj nowe pole do edycji lokalizacji biura na końcu przed przyciskiem **Zapisz:**

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Uruchom aplikację, wybierz kartę **Instruktorzy,** a następnie kliknij pozycję **Edytuj** instruktora. Zmień **lokalizację biura** i kliknij przycisk **Zapisz**.

![Strona edycji instruktora](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Dodawanie kursów do strony Edycja

Instruktorzy mogą nauczać dowolną liczbę kursów. Teraz możesz ulepszyć stronę Edycja instruktora, dodając możliwość zmiany przydziałów kursów przy użyciu grupy pól wyboru, jak pokazano na poniższym zstrząśnieniu ekranu:

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses.png)

Relacja między course i instructor jednostek jest wiele do wielu. Aby dodać i usunąć relacje, należy dodać i usunąć encje do i z zestawu jednostek sprzężenia courseAssignments.

Interfejs użytkownika, który umożliwia zmianę kursów, do których jest przypisany instruktor, jest grupą pól wyboru. Zostanie wyświetlone pole wyboru dla każdego kursu w bazie danych i wybrane są te, do których instruktor jest aktualnie przypisany. Użytkownik może zaznaczyć lub wyczyścić pola wyboru, aby zmienić przypisania kursu. Jeśli liczba kursów były znacznie większe, prawdopodobnie chcesz użyć innej metody prezentowania danych w widoku, ale należy użyć tej samej metody manipulowania jednostki sprzężenia do tworzenia lub usuwania relacji.

### <a name="update-the-instructors-controller"></a>Aktualizowanie kontrolera instruktorów

Aby udostępnić dane do widoku dla listy pól wyboru, użyj klasy modelu widoku.

Utwórz *AssignedCourseData.cs* w folderze *SchoolViewModels* i zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

W *InstructorsController.cs*, zastąp `Edit` HttpGet metody z następującym kodem. Zmiany są wyróżnione.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Kod dodaje wczesne ładowanie `Courses` dla właściwości nawigacji `PopulateAssignedCourseData` i wywołuje nową metodę, aby `AssignedCourseData` zapewnić informacje dla tablicy pola wyboru przy użyciu klasy modelu widoku.

Kod w `PopulateAssignedCourseData` metodzie odczytuje za pośrednictwem wszystkich Course jednostek w celu załadowania listy kursów przy użyciu klasy modelu widoku. Dla każdego kursu kod sprawdza, czy kurs istnieje `Courses` we właściwości nawigacji instruktora. Aby utworzyć efektywne wyszukiwanie podczas sprawdzania, czy kurs jest przypisany do instruktora, `HashSet` kursy przypisane do instruktora są umieszczane w kolekcji. Właściwość `Assigned` jest ustawiona na true dla kursów, do które jest przypisany instruktor. Widok użyje tej właściwości, aby określić, które pola wyboru muszą być wyświetlane jako zaznaczone. Na koniec lista jest przekazywana `ViewData`do widoku w pliku .

Następnie dodaj kod, który jest wykonywany, gdy użytkownik kliknie **przycisk Zapisz**. Zastąp `EditPost` metodę następującym kodem i dodaj `Courses` nową metodę, która aktualizuje właściwość nawigacji instructor jednostki.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Podpis metody różni się teraz `Edit` od metody HttpGet, `EditPost` więc `Edit`nazwa metody zmienia się z powrotem na .

Ponieważ widok nie ma kolekcji Course jednostek, spinacza modelu nie `CourseAssignments` można automatycznie zaktualizować właściwości nawigacji. Zamiast używać spinacza modelu, `CourseAssignments` aby zaktualizować właściwość nawigacji, `UpdateInstructorCourses` można to zrobić w nowej metodzie. W związku z tym `CourseAssignments` należy wykluczyć właściwość z powiązania modelu. Nie wymaga to żadnych zmian w `TryUpdateModel` kodzie, który wywołuje, ponieważ używasz przeciążenia białej listy i `CourseAssignments` nie znajduje się na liście dołączania.

Jeśli nie wybrano żadnych `UpdateInstructorCourses` pól wyboru, kod inicjuje właściwość `CourseAssignments` nawigacji z pustą kolekcją i zwraca:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Kod następnie pętli przez wszystkie kursy w bazie danych i sprawdza każdy kurs względem tych aktualnie przypisanych do instruktora w porównaniu z tymi, które zostały wybrane w widoku. Aby ułatwić efektywne wyszukiwanie, dwie ostatnie `HashSet` kolekcje są przechowywane w obiektach.

Jeśli pole wyboru dla kursu zostało zaznaczone, ale `Instructor.CourseAssignments` kurs nie znajduje się we właściwości nawigacji, kurs jest dodawany do kolekcji we właściwości nawigacji.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Jeśli pole wyboru kursu nie zostało zaznaczone, ale `Instructor.CourseAssignments` kurs znajduje się we właściwości nawigacji, kurs zostanie usunięty z właściwości nawigacji.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Aktualizowanie widoków instruktora

W *widoku Views/Instructors/Edit.cshtml*dodaj pole **Kursy** z tablicą pól wyboru, `div` dodając następujący kod bezpośrednio `div` po elementach pola Pakietu **Office** i przed elementem przycisku **Zapisz.**

<a id="notepad"></a>
> [!NOTE]
> Po wklejeniu kodu w programie Visual Studio podziały wierszy mogą zostać zmienione w sposób, który przerywa kod. Jeśli kod wygląda inaczej po wklejeniu, naciśnij klawisze Ctrl+Z jeden raz, aby cofnąć automatyczne formatowanie. Spowoduje to naprawienie podziałów wierszy, tak aby wyglądały jak to, co widzisz tutaj. Wcięcie nie musi być doskonałe, ale `@</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` i wiersze muszą być w jednym wierszu, jak pokazano lub pojawi się błąd środowiska uruchomieniowego. Po wybraniu bloku nowego kodu naciśnij trzykrotnie klawisz Tab, aby wyrównać nowy kod z istniejącym kodem. Ten problem został rozwiązany w programie Visual Studio 2019.

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Ten kod tworzy tabelę HTML, która ma trzy kolumny. W każdej kolumnie znajduje się pole wyboru, po którym następuje podpis, który składa się z numeru kursu i tytułu. Wszystkie pola wyboru mają taką samą nazwę ("selectedCourses"), która informuje spinacz modelu, że mają być traktowane jako grupa. Atrybut value każdego pola wyboru jest ustawiony `CourseID`na wartość . Po zaksięgowaniu strony spinacz modelu przekazuje tablicę do `CourseID` kontrolera, która składa się z wartości tylko dla zaznaczonych pól wyboru.

Gdy pola wyboru są początkowo renderowane, te, które są dla kursów przypisanych do instruktora mają sprawdzone atrybuty, który je wybiera (wyświetla je zaznaczone).

Uruchom aplikację, wybierz kartę **Instruktorzy** i kliknij pozycję **Edytuj** instruktora, aby wyświetlić stronę **Edycja.**

![Strona edycji instruktora z kursami](update-related-data/_static/instructor-edit-courses.png)

Zmień niektóre przypisania kursu i kliknij przycisk Zapisz. Wprowadzone zmiany zostaną odzwierciedlone na stronie Indeks.

> [!NOTE]
> Podejście przyjęte tutaj, aby edytować dane kursu instruktora, działa dobrze, gdy istnieje ograniczona liczba kursów. Dla kolekcji, które są znacznie większe, inny interfejs użytkownika i inna metoda aktualizacji będzie wymagane.

## <a name="update-delete-page"></a>Aktualizuj stronę Usuwania

W *InstructorsController.cs*, usuń `DeleteConfirmed` metodę i wstaw poniższy kod w jej miejsce.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Ten kod wprowadza następujące zmiany:

* Czy gorliwy `CourseAssignments` ładowania dla właściwości nawigacji. Musisz dołączyć to lub EF nie będzie `CourseAssignment` wiedzieć o powiązanych jednostek i nie będzie ich usuwać. Aby uniknąć konieczności ich odczytywania tutaj można skonfigurować kaskadowe usuwanie w bazie danych.

* Jeśli instruktor do usunięcia jest przypisany jako administrator wszystkich działów, usuwa przypisanie instruktora z tych działów.

## <a name="add-office-location-and-courses-to-create-page"></a>Dodawanie lokalizacji i kursów w biurze do strony Tworzenie

W *InstructorsController.cs*, usuń HttpGet i `Create` HttpPost metody, a następnie dodać następujący kod w ich miejsce:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Ten kod jest podobny do `Edit` tego, co widziałeś dla metod, z tą różnicą, że początkowo nie są wybierane żadne kursy. HttpGet `Create` metoda wywołuje `PopulateAssignedCourseData` metodę nie dlatego, że mogą być kursy wybrane, `foreach` ale w celu zapewnienia pustej kolekcji dla pętli w widoku (w przeciwnym razie kod widoku będzie zgłosić wyjątek odwołania null).

HttpPost `Create` Metoda dodaje każdy wybrany `CourseAssignments` kurs do właściwości nawigacji, zanim sprawdzi błędy sprawdzania poprawności i dodaje nowego instruktora do bazy danych. Kursy są dodawane, nawet jeśli istnieją błędy modelu, dzięki czemu w przypadku wystąpienia błędów modelu (na przykład użytkownik wpisał nieprawidłową datę), a strona jest ponownie świetliona komunikatem o błędzie, wszystkie wybory kursu, które zostały dokonane, są automatycznie przywracane.

Należy zauważyć, że aby móc dodać `CourseAssignments` kursy do właściwości nawigacji należy zainicjować właściwość jako pustą kolekcję:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Jako alternatywę dla tego w kodzie kontrolera, można to zrobić w modelu Instructor, zmieniając getter właściwości, aby automatycznie utworzyć kolekcję, jeśli nie istnieje, jak pokazano w poniższym przykładzie:

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

Jeśli zmodyfikujesz `CourseAssignments` właściwość w ten sposób, można usunąć jawny kod inicjowania właściwości w kontrolerze.

W *polu Widoki/Instruktor/Utwórz.cshtml*dodaj pole tekstowe lokalizacji biura i pola wyboru dla kursów przed przyciskiem Prześlij. Podobnie jak w przypadku strony Edycja, [należy naprawić formatowanie, jeśli program Visual Studio formatuje kod podczas wklejania go.](#notepad)

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Przetestuj, uruchamiając aplikację i tworząc instruktora.

## <a name="handling-transactions"></a>Obsługa transakcji

Jak wyjaśniono w [samouczku CRUD,](crud.md)entity framework niejawnie implementuje transakcje. W przypadku scenariuszy, w których potrzebujesz większej kontroli — na przykład, jeśli chcesz uwzględnić operacje wykonywane poza platformą Encji w transakcji — zobacz [Transakcje](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Strony dostosowane kursy
> * Dodano stronę Edycji instruktorów
> * Dodano kursy do strony Edycja
> * Zaktualizowana strona Usuń
> * Dodano lokalizację biura i kursy do utworzenia strony

Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać konflikty współbieżności.

> [!div class="nextstepaction"]
> [Obsługa konfliktów współbieżności](concurrency.md)
