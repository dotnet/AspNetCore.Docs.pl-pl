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
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Poradnik: Odczyt powiązanych danych - ASP.NET MVC z EF Core

W poprzednim samouczku ukończono model danych szkoły. W tym samouczku będziesz czytać i wyświetlać powiązane dane — czyli dane, które entity framework ładuje do właściwości nawigacji.

Na poniższych ilustracjach przedstawiono strony, z którymi będziesz pracować.

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dowiedz się, jak załadować powiązane dane
> * Tworzenie strony Kursy
> * Tworzenie strony Instruktorzy
> * Dowiedz się więcej o jawnym ładowaniu

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie złożonego modelu danych](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Dowiedz się, jak załadować powiązane dane

Istnieje kilka sposobów, że oprogramowanie orm (Object-relational Mapping), takie jak Entity Framework, może załadować powiązane dane do właściwości nawigacji jednostki:

* Gorliwy załadunek. Podczas odczytywania jednostki powiązane dane są pobierane wraz z nią. Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne. Określić wczesne ładowanie w entity `Include` framework `ThenInclude` core przy użyciu i metody.

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)

  Można pobrać niektóre dane w oddzielnych kwerend i EF "rozwiązuje" właściwości nawigacji.  Oznacza to, że EF automatycznie dodaje oddzielnie pobrane jednostki, w których należą do właściwości nawigacji wcześniej pobranych jednostek. Dla kwerendy pobierającej powiązane dane można `Load` użyć metody zamiast metody zwracanej listy lub `ToList` `Single`obiektu, takiej jak lub .

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

* Jawne ładowanie. Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane. Piszesz kod, który pobiera powiązane dane, jeśli jest to potrzebne. Podobnie jak w przypadku zapału ładowania z oddzielnych zapytań, jawne ładowanie powoduje wiele zapytań wysyłanych do bazy danych. Różnica polega na tym, że przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane. W entity framework core 1.1 `Load` można użyć metody do jawnego ładowania. Przykład:

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* Z opóźnieniem. Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane. Jednak przy pierwszej próbie uzyskania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie. Kwerenda jest wysyłana do bazy danych za każdym razem, gdy próbujesz uzyskać dane z właściwości nawigacji po raz pierwszy. Entity Framework Core 1.0 nie obsługuje ładowania z opóźnieniem.

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Jeśli wiesz, że potrzebujesz powiązanych danych dla każdej pobranej jednostki, wczesne ładowanie często oferuje najlepszą wydajność, ponieważ pojedyncze zapytanie wysyłane do bazy danych jest zazwyczaj bardziej wydajne niż oddzielne zapytania dla każdej pobranej jednostki. Załóżmy na przykład, że każdy dział ma dziesięć powiązanych kursów. Wczesne ładowanie wszystkich powiązanych danych spowodowałoby tylko jedno zapytanie (sprzężenie) i pojedynczą podróż w obie strony do bazy danych. Oddzielne zapytanie dla kursów dla każdego działu spowodowałoby jedenaście rund do bazy danych. Dodatkowe rundy do bazy danych są szczególnie szkodliwe dla wydajności, gdy opóźnienie jest wysokie.

Z drugiej strony w niektórych scenariuszach oddzielne zapytania jest bardziej wydajne. Wczesne ładowanie wszystkich powiązanych danych w jednej kwerendzie może spowodować wygenerowanie bardzo złożonego sprzężenia, którego program SQL Server nie może wydajnie przetwarzać. Lub jeśli chcesz uzyskać dostęp do właściwości nawigacji jednostki tylko dla podzbioru zestawu jednostek, które przetwarzasz, oddzielne zapytania mogą działać lepiej, ponieważ wczesne ładowanie wszystkiego z góry spowoduje pobranie większej ilości danych niż jest to potrzebne. Jeśli wydajność jest krytyczna, najlepiej przetestować wydajność w obie strony, aby dokonać najlepszego wyboru.

## <a name="create-a-courses-page"></a>Tworzenie strony Kursy

Course Jednostki zawiera właściwość nawigacji, która zawiera Dział jednostki działu, do której kurs jest przypisany. Aby wyświetlić nazwę przypisanego działu na liście kursów, należy uzyskać Name właściwości z Department `Course.Department` jednostki, która znajduje się we właściwości nawigacji.

Utwórz kontroler o nazwie CoursesController dla typu jednostki Course, używając tych samych opcji dla **kontrolera MVC z widokami, używając** szkieletu entity framework, który wykonano wcześniej dla kontrolera Studenci, jak pokazano na poniższej ilustracji:

![Dodaj kontroler kursów](read-related-data/_static/add-courses-controller.png)

Otwórz *CoursesController.cs* i zbadaj `Index` metodę. Automatyczne szkielety określono wczesne ładowanie dla `Department` właściwości nawigacji `Include` przy użyciu metody.

`Index` Zastąp metodę następującym kodem, który `IQueryable` używa bardziej odpowiedniej`courses` nazwy dla `schoolContext`jednostek kursu , która zwraca ( zamiast):

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Otwórz *widoki/kursy/index.cshtml* i zastąp kod szablonu następującym kodem. Zmiany są wyróżnione:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Wprowadzono następujące zmiany w kodzie szkieletu:

* Zmieniono nagłówek z Indeks na Kursy.

* Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości. Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych. Jednak w tym przypadku klucz podstawowy ma znaczenie i chcesz go pokazać.

* Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu. Kod wyświetla `Name` właściwość department jednostki, która `Department` jest ładowana do właściwości nawigacji:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Tworzenie strony Instruktorzy

W tej sekcji utworzysz kontroler i widok dla instructor jednostki w celu wyświetlenia instruktorów strony:

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:

* Lista instruktorów wyświetla powiązane dane z officeAssignment jednostki. Jednostki Instructor i OfficeAssignment znajdują się w relacji jeden do zera lub jeden. Użyjesz zaauwania chętnie dla officeAssignment jednostek. Jak wyjaśniono wcześniej, wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy potrzebujesz powiązanych danych dla wszystkich pobranych wierszy tabeli podstawowej. W takim przypadku chcesz wyświetlić przypisania biura dla wszystkich wyświetlanych instruktorów.

* Gdy użytkownik wybierze instruktora, wyświetlane są powiązane jednostki kursu. Instruktor i kurs jednostek są w relacji wiele do wielu. Użyjesz wczesnego ładowania dla jednostek Kursu i powiązanych z nimi jednostek Działów. W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są kursy tylko dla wybranego instruktora. Jednak w tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w obrębie jednostek, które są same we właściwości nawigacji.

* Gdy użytkownik wybierze kurs, wyświetlane są powiązane dane z zestawu jednostek Rejestracje. Course i Enrollment jednostki są w relacji jeden do wielu. Użyjesz oddzielnych zapytań dla jednostek rejestracji i powiązanych jednostek studenta.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Tworzenie modelu widoku dla widoku Indeks instruktora

Instruktorzy strona zawiera dane z trzech różnych tabel. W związku z tym utworzysz model widoku, który zawiera trzy właściwości, z których każda przechowuje dane dla jednej z tabel.

W folderze *SchoolViewModels* utwórz *InstructorIndexData.cs* i zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Tworzenie kontrolera instruktora i widoków

Utwórz kontroler instruktorów z akcjami odczytu/zapisu EF, jak pokazano na poniższej ilustracji:

![Dodawanie kontrolera instruktorów](read-related-data/_static/add-instructors-controller.png)

Otwórz *InstructorsController.cs* i dodaj instrukcję using dla obszaru nazw ViewModels:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Zastąp Index metody z następującym kodem, aby wykonać wczesne ładowanie powiązanych danych i umieścić go w modelu widoku.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Metoda akceptuje opcjonalne dane`id`trasy ( )`courseID`i parametru ciągu zapytania ( ), które zawierają wartości identyfikatorów wybranego instruktora i wybranego kursu. Parametry są dostarczane przez **wybierz** hiperłącza na stronie.

Kod rozpoczyna się od utworzenia wystąpienia modelu widoku i umieszczenia w nim listy instruktorów. Kod określa wczesne ładowanie `Instructor.OfficeAssignment` dla `Instructor.CourseAssignments` i właściwości nawigacji. W `CourseAssignments` obrębie właściwości `Course` właściwość jest ładowana, `Department` a w tym, `Enrollment` `Enrollments` i `Student` właściwości są ładowane, a w ramach każdej jednostki właściwość jest ładowany.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Ponieważ widok zawsze wymaga OfficeAssignment jednostki, jest bardziej efektywne, aby pobrać, że w tej samej kwerendy. Jednostki kursu są wymagane, gdy instruktor jest wybrany na stronie sieci web, więc pojedyncze zapytanie jest lepsze niż wiele zapytań tylko wtedy, gdy strona jest wyświetlana częściej z wybranym kursem niż bez.

Kod powtarza `CourseAssignments` się `Course` i dlatego, że `Course`potrzebne są dwie właściwości z . Pierwszy ciąg `ThenInclude` wywołań `CourseAssignment.Course` `Course.Enrollments`dostaje `Enrollment.Student`, i .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

W tym momencie w `ThenInclude` kodzie, inny `Student`będzie dla właściwości nawigacji , które nie są potrzebne. Ale `Include` wywołanie zaczyna `Instructor` się od początku z właściwości, więc trzeba przejść `Course.Department` przez `Course.Enrollments`łańcuch ponownie, tym razem określając zamiast .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Poniższy kod jest wykonywany, gdy wybrano instruktora. Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku. `Courses` Właściwość modelu widoku jest następnie ładowany z Course `CourseAssignments` jednostek z właściwości nawigacji tego instruktora.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

Metoda `Where` zwraca kolekcję, ale w tym przypadku kryteria przekazane do tej metody spowodować tylko jednej instructor jednostki zwracane. Metoda `Single` konwertuje kolekcję na jedną jednostkę Instructor, która `CourseAssignments` daje dostęp do właściwości tej jednostki. Właściwość `CourseAssignments` `CourseAssignment` zawiera jednostki, z których `Course` mają być tylko powiązane jednostki.

Metoda jest `Single` używana w kolekcji, gdy wiesz, że kolekcja będzie miała tylko jeden element. Single Metoda zgłasza wyjątek, jeśli kolekcja przekazana do jest pusta lub jeśli istnieje więcej niż jeden element. Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta. Jednak w tym przypadku, które nadal spowodować wyjątek `Courses` (od próby znalezienia właściwości na odwołanie null), a komunikat wyjątku będzie mniej wyraźnie wskazać przyczynę problemu. Po wywołaniu `Single` metody, można również przekazać w Where warunek `Where` zamiast wywoływania metody oddzielnie:

```csharp
.Single(i => i.ID == id.Value)
```

Zamiast:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Następnie, jeśli wybrano kurs, wybrany kurs jest pobierany z listy kursów w modelu widoku. Następnie `Enrollments` właściwość modelu widoku jest ładowana z Enrollment `Enrollments` jednostek z właściwości nawigacji tego kursu.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Modyfikowanie widoku indeksu instruktora

W *views/instructors/index.cshtml*, zastąp kod szablonu następującym kodem. Zmiany są wyróżnione.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Wprowadzono następujące zmiany w istniejącym kodzie:

* Zmieniono klasę `InstructorIndexData`modelu na .

* Zmieniono tytuł strony z **Indeks** na **Instruktorzy**.

* Dodano kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null. (Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment).

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Dodano kolumnę **Kursy,** w której wyświetlane są kursy prowadzone przez każdego instruktora. Aby uzyskać więcej informacji, zobacz [sekcję Jawne przejście wiersza](xref:mvc/views/razor#explicit-line-transition) w artykule składni Razor.

* Dodano kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora. Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Dodano nowe hiperłącze oznaczone **Select** bezpośrednio przed innymi łączami w każdym wierszu, co `Index` powoduje, że identyfikator wybranego instruktora ma zostać wysłany do metody.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla Właściwość Lokalizacja powiązanych encji OfficeAssignment i pustą komórkę tabeli, gdy nie ma powiązanej encji OfficeAssignment.

![Strona Indeks instruktorów nic nie zaznaczona](read-related-data/_static/instructors-index-no-selection.png)

W *pliku Views/Instructors/Index.cshtml* po elemencie tabeli zamknięcia (na końcu pliku) dodaj następujący kod. Ten kod wyświetla listę kursów związanych z instruktorem, gdy zostanie wybrany instruktor.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Ten kod `Courses` odczytuje właściwość modelu widoku, aby wyświetlić listę kursów. Zawiera również **wybierz** hiperłącze, które wysyła identyfikator wybranego `Index` kursu do metody akcji.

Odśwież stronę i wybierz instruktora. Teraz zostanie wyświetlona siatka, która wyświetla kursy przypisane do wybranego instruktora, a dla każdego kursu zostanie wyświetlona nazwa przypisanego działu.

![Wybrano instruktora strony Indeks instruktorów](read-related-data/_static/instructors-index-instructor-selected.png)

Po dodaniu bloku kodu dodaj następujący kod. Spowoduje to wyświetlenie listy uczniów, którzy są zapisani na kurs po wybraniu tego kursu.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Ten kod odczytuje Enrollments właściwość modelu widoku w celu wyświetlenia listy studentów zarejestrowanych w kursie.

Odśwież stronę ponownie i wybierz instruktora. Następnie wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>Informacje o jawnym ładowaniu

Po pobraniu listy instruktorów w *InstructorsController.cs,* określono wczesne ładowanie `CourseAssignments` dla właściwości nawigacji.

Załóżmy, że użytkownicy rzadko chcą widzieć rejestracje w wybranym instruktorze i kursie. W takim przypadku można załadować dane rejestracji tylko wtedy, gdy jest to wymagane. Aby zobaczyć przykład jak zrobić jawne `Index` ładowanie, zastąp metodę następującym kodem, który usuwa wczesne ładowanie dla rejestracji i ładuje tę właściwość jawnie. Zmiany kodu są wyróżnione.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Nowy kod porzuca *ThenInclude* metoda wywołuje dane rejestracji z kodu, który pobiera jednostki instruktora. To również `AsNoTracking`spada .  Jeśli wybrano instruktora i kurs, wyróżniony kod pobiera jednostki rejestracji dla wybranego kursu i jednostki Student dla każdej rejestracji.

Uruchom aplikację, przejdź do strony Indeks instruktorów teraz i nie zobaczysz żadnej różnicy w tym, co jest wyświetlane na stronie, chociaż zmieniono sposób pobierania danych.

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dowiedz się, jak załadować powiązane dane
> * Utworzono stronę Kursy
> * Utworzono stronę Instruktorzy
> * Dowiedziałem się o jawnym załadunku

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować powiązane dane.

> [!div class="nextstepaction"]
> [Aktualizowanie powiązanych danych](update-related-data.md)
