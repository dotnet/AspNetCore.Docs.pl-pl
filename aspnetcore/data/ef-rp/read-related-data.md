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
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a>Strony brzytwy z EF Core w ASP.NET Core - Odczyt powiązanych danych - 6 z 8

Przez [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)i Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku pokazano, jak odczytywać i wyświetlać powiązane dane. Powiązane dane to dane, które EF Core ładuje do właściwości nawigacji.

Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:

![Strona Indeks kursów](read-related-data/_static/courses-index30.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Zapalony, jawny i leniwy ładowanie

Istnieje kilka sposobów, że EF Core można załadować powiązane dane do właściwości nawigacji jednostki:

* [Gorliwy załadunek](/ef/core/querying/related-data#eager-loading). Wczesne ładowanie jest, gdy kwerenda dla jednego typu jednostki ładuje również powiązane jednostki. Podczas odczytywania jednostki pobierane są powiązane z nią dane. Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne. EF Core wyda wiele zapytań dla niektórych typów ładowania chętnych. Wydawanie wielu zapytań może być bardziej wydajne niż gigantyczne pojedyncze zapytanie. Wczesne ładowanie jest `Include` określone `ThenInclude` za pomocą i metod.

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)
 
  Wczesne ładowanie wysyła wiele zapytań, gdy nawigacja kolekcji jest uwzględniona:

  * Jedno zapytanie dla kwerendy głównej 
  * Jedna kwerenda dla każdej kolekcji "krawędzi" w drzewie obciążenia.

* Oddzielne zapytania `Load`z: Dane mogą być pobierane w oddzielnych kwerend, a EF Core "rozwiązuje" właściwości nawigacji. "Poprawki w górę" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji. Oddzielne zapytania `Load` z jest bardziej jak jawne ładowanie niż wczesne ładowanie.

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  Uwaga: EF Core automatycznie naprawia właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu. Nawet jeśli dane dla właściwości nawigacji *nie* jest jawnie uwzględnione, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.

* [Jawne ładowanie](/ef/core/querying/related-data#explicit-loading). Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane. Kod musi być zapisany, aby pobrać powiązane dane, gdy jest to potrzebne. Jawne ładowanie z oddzielnymi zapytaniami powoduje wiele zapytań wysyłanych do bazy danych. Przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane. Użyj `Load` metody, aby zrobić jawne ładowanie. Przykład:

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* [Z opóźnieniem ładowania](/ef/core/querying/related-data#lazy-loading). [Z opóźnieniem dodano do EF Core w wersji 2.1](/ef/core/querying/related-data#lazy-loading). Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane. Przy pierwszym dostępie do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie. Kwerenda jest wysyłana do bazy danych za każdym razem, gdy właściwość nawigacji jest dostępna po raz pierwszy.

## <a name="create-course-pages"></a>Tworzenie stron kursu

Jednostka `Course` zawiera właściwość nawigacji, `Department` która zawiera jednostkę pokrewną.

![Course.Department](read-related-data/_static/dep-crs.png)

Aby wyświetlić nazwę przypisanego działu kursu:

* Załaduj `Department` `Course.Department` powiązaną encję do właściwości nawigacji.
* Pobierz nazwę z `Department` właściwości `Name` jednostki.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Strony kursu rusztowania

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Postępuj zgodnie z instrukcjami na [stronach Scaffold Student](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:

  * Utwórz folder *Strony/Kursy.*
  * Użyj `Course` dla klasy modelu.
  * Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Utwórz folder *Strony/Kursy.*

* Uruchom następujące polecenie, aby s rusztować strony course.

  **W systemie Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **W systemie Linux lub macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Otwórz *strony/kursy/index.cshtml.cs* i `OnGetAsync` sprawdź metodę. Aparat rusztowań określone gorliwe ładowanie dla właściwości `Department` nawigacji. Metoda `Include` określa wczesne ładowanie.

* Uruchom aplikację i wybierz **łącze Kursy.** W kolumnie `DepartmentID`działu jest wyświetlany program , który nie jest przydatny.

### <a name="display-the-department-name"></a>Wyświetlanie nazwy działu

Aktualizuj strony/kursy/index.cshtml.cs za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Powyższy kod zmienia `Course` właściwość `Courses` `AsNoTracking`i dodaje . `AsNoTracking`zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone. Jednostki nie muszą być śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.

Zaktualizuj *strony/kursy/index.cshtml* za pomocą następującego kodu.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

W kodzie rusztowania wprowadzono następujące zmiany:

* Zmieniono `Course` nazwę `Courses`właściwości na .
* Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości. Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych. Jednak w tym przypadku klucz podstawowy ma znaczenie.
* Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu. Kod wyświetla `Name` właściwość `Department` jednostki, która jest `Department` ładowana do właściwości nawigacji:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.

![Strona Indeks kursów](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Ładowanie powiązanych danych za pomocą opcji Wybierz

Metoda `OnGetAsync` ładuje powiązane dane `Include` za pomocą metody. Metoda `Select` jest alternatywą, która ładuje tylko powiązane dane potrzebne. Dla pojedynczych elementów, takich `Department.Name` jak używa SQL INNER JOIN. Dla kolekcji używa innego dostępu do bazy `Include` danych, ale tak samo operator w kolekcjach.

Następujący kod ładuje powiązane `Select` dane za pomocą metody:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Poprzedni kod nie zwraca żadnych typów jednostek, w związku z tym nie śledzenie jest wykonywane. Aby uzyskać więcej informacji na temat śledzenia EF, zobacz [Śledzenie a nie śledzenie zapytań](/ef/core/querying/tracking).

W: `CourseViewModel`

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Zobacz [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) aby uzyskać pełny przykład.

## <a name="create-instructor-pages"></a>Tworzenie stron instruktora

W tej sekcji szkielety instructor stron i dodaje powiązanych kursów i rejestracji do instruktorów indeks strony.

<a name="IP"></a>
![Strona Indeks instruktorów](read-related-data/_static/instructors-index30.png)

Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:

* Lista instruktorów wyświetla powiązane dane `OfficeAssignment` z jednostki (Office w poprzednim obrazie). I `Instructor` `OfficeAssignment` jednostki są w relacji jeden do zera lub jeden. Wczesne ładowanie jest `OfficeAssignment` używane dla jednostek. Wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy powiązane dane muszą być wyświetlane. W takim przypadku są wyświetlane zadania biurowe dla instruktorów.
* Gdy użytkownik wybierze instruktora, są wyświetlane powiązane `Course` jednostki. I `Instructor` `Course` jednostki są w relacji wiele do wielu. Wczesne ładowanie jest `Course` używane dla jednostek i ich jednostek powiązanych. `Department` W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są tylko kursy dla wybranego instruktora. W tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w jednostkach, które znajdują się we właściwościach nawigacji.
* Gdy użytkownik wybierze kurs, wyświetlane `Enrollments` są powiązane dane z encji. Na poprzednim obrazie wyświetlana jest nazwa ucznia i ocena. I `Course` `Enrollment` jednostki są w relacji jeden do wielu.

### <a name="create-a-view-model"></a>Tworzenie modelu widoku

Strona instruktorów zawiera dane z trzech różnych tabel. Potrzebny jest model widoku, który zawiera trzy właściwości reprezentujące trzy tabele.

Utwórz *SchoolViewModels/InstructorIndexData.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Strony instruktora rusztowania

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Postępuj zgodnie z instrukcjami w [rusztowania stron uczniów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:

  * Utwórz folder *Strony/Instruktorzy.*
  * Użyj `Instructor` dla klasy modelu.
  * Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Utwórz folder *Strony/Instruktorzy.*

* Uruchom następujące polecenie, aby smisowywać strony instruktora.

  **W systemie Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **W systemie Linux lub macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Aby zobaczyć, jak wygląda strona szkieletu przed jej zaktualizowaniem, uruchom aplikację i przejdź do strony Instruktorzy.

Aktualizuj *strony/instruktorzy/index.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

Metoda `OnGetAsync` akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.

Sprawdź kwerendę w pliku *Pages/Instructors/Index.cshtml.cs:*

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Kod określa wczesne ładowanie dla następujących właściwości nawigacji:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Zwróć uwagę na `Include` powtarzanie i `ThenInclude` metody dla `CourseAssignments` i `Course`. To powtórzenie jest konieczne, aby określić wczesne `Course` ładowanie dla dwóch właściwości nawigacji jednostki.

Poniższy kod jest wykonywany po`id != null`wybraniu instruktora ( ).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku. `Courses` Właściwość modelu widoku jest ładowana z `Course` jednostek z właściwości nawigacji tego instruktora. `CourseAssignments`

Metoda `Where` zwraca kolekcję. Ale w takim przypadku filtr wybierze jedną jednostkę. więc `Single` metoda jest wywoływana do konwersji kolekcji do pojedynczej `Instructor` jednostki. Jednostka `Instructor` zapewnia dostęp `CourseAssignments` do właściwości. `CourseAssignments`zapewnia dostęp do `Course` powiązanych jednostek.

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

Metoda `Single` jest używana w kolekcji, gdy kolekcja ma tylko jeden element. Metoda `Single` zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element. Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.

Następujący kod wypełnia `Enrollments` właściwość modelu widoku po wybraniu kursu:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Aktualizowanie strony indeksu instruktorów

Aktualizuj *strony/instruktorzy/index.cshtml* za pomocą następującego kodu.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Poprzedni kod wprowadza następujące zmiany:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"`. `"{id:int?}"`jest szablonem trasy. Szablon trasy zmienia ciągi zapytania liczby całkowitej w adresie URL, aby rozsyłać dane. Na przykład kliknięcie łącza **Wybierz** dla instruktora `@page` tylko z dyrektywą powoduje uzyskanie następującego adresu URL:

  `https://localhost:5001/Instructors?id=2`

  Gdy dyrektywa strony `@page "{id:int?}"`jest , adres URL jest:

  `https://localhost:5001/Instructors/2`

* Dodaje kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null. Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Dodaje kolumnę **Kursy,** w której są wyświetlane kursy prowadzone przez każdego instruktora. Zobacz [Jawne przejście wiersza,](xref:mvc/views/razor#explicit-line-transition) aby uzyskać więcej informacji na temat tej składni maszynki do golenia.

* Dodaje kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora i kursu. Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Dodaje nowe hiperłącze z etykietą **Wybierz**. To łącze wysyła identyfikator wybranego `Index` instruktora do metody i ustawia kolor tła.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Dodaje tabelę kursów dla wybranego instruktora.

* Dodaje tabelę rejestracji uczniów dla wybranego kursu.

Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla `Location` (biuro) z `OfficeAssignment` jednostki pokrewne. Jeśli `OfficeAssignment` wartość null ma wartość null, wyświetlana jest pusta komórka tabeli.

Kliknij łącze **Wybierz** dla instruktora. Zmiany stylu wiersza i kursy przypisane do tego instruktora są wyświetlane.

Wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Korzystanie z pojedynczego

Metoda `Single` może przejść `Where` w warunku `Where` zamiast wywoływania metody oddzielnie:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Korzystanie `Single` z Gdzie warunek jest kwestią osobistych preferencji. Zapewnia żadnych korzyści za `Where` pomocą metody.

## <a name="explicit-loading"></a>Jawne ładowanie

Bieżący kod określa wczesne `Enrollments` `Students`ładowanie i:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Załóżmy, że użytkownicy rzadko chcą zobaczyć rejestracje w trakcie. W takim przypadku optymalizacja będzie tylko załadować dane rejestracji, jeśli jest to wymagane. W tej sekcji `OnGetAsync` jest aktualizowana w `Enrollments` `Students`celu użycia jawnego ładowania i .

Aktualizuj *strony/instruktorzy/index.cshtml.cs* za pomocą następującego kodu.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Poprzedni kod porzuca *ThenInclude* metoda wywołuje rejestracji i danych studenta. Jeśli kurs jest zaznaczony, jawny kod ładowania pobiera:

* Encje `Enrollment` dla wybranego kursu.
* Jednostki `Student` dla `Enrollment`każdego .

Należy zauważyć, że poprzedni `.AsNoTracking()`kod komentuje . Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.

Testowanie aplikacji. Z punktu widzenia użytkownika aplikacja zachowuje się identycznie jak poprzednia wersja.

## <a name="next-steps"></a>Następne kroki

Następny samouczek pokazuje, jak zaktualizować powiązane dane.

>[!div class="step-by-step"]
>[Poprzedni samouczek](xref:data/ef-rp/complex-data-model)
>[Następny samouczek](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku powiązane dane są odczytywane i wyświetlane. Powiązane dane to dane, które EF Core ładuje do właściwości nawigacji.

Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pobierz instrukcje](xref:index#how-to-download-a-sample).

Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Chętne, wyraźne i leniwe ładowanie powiązanych danych

Istnieje kilka sposobów, że EF Core można załadować powiązane dane do właściwości nawigacji jednostki:

* [Gorliwy załadunek](/ef/core/querying/related-data#eager-loading). Wczesne ładowanie jest, gdy kwerenda dla jednego typu jednostki ładuje również powiązane jednostki. Po odczytaniu jednostki pobierane są powiązane z nią dane. Zazwyczaj powoduje to pojedyncze sprzężenie kwerendy, która pobiera wszystkie dane, które są potrzebne. EF Core wyda wiele zapytań dla niektórych typów ładowania chętnych. Wydawanie wielu zapytań może być bardziej wydajne niż w przypadku niektórych zapytań w EF6, gdzie było pojedyncze zapytanie. Wczesne ładowanie jest `Include` określone `ThenInclude` za pomocą i metod.

  ![Gorliwy przykład ładowania](read-related-data/_static/eager-loading.png)
 
  Wczesne ładowanie wysyła wiele zapytań, gdy nawigacja kolekcji jest uwzględniona:

  * Jedno zapytanie dla kwerendy głównej 
  * Jedna kwerenda dla każdej kolekcji "krawędzi" w drzewie obciążenia.

* Oddzielne zapytania `Load`z: Dane mogą być pobierane w oddzielnych kwerend, a EF Core "rozwiązuje" właściwości nawigacji. "rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji. Oddzielne zapytania `Load` z jest bardziej jak jawne ładowanie niż wczesne ładowanie.

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  Uwaga: EF Core automatycznie naprawia właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu. Nawet jeśli dane dla właściwości nawigacji *nie* jest jawnie uwzględnione, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.

* [Jawne ładowanie](/ef/core/querying/related-data#explicit-loading). Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane. Kod musi być zapisany, aby pobrać powiązane dane, gdy jest to potrzebne. Jawne ładowanie z oddzielnymi zapytaniami powoduje wiele zapytań wysyłanych do bazy danych. Przy jawnym ładowaniu kod określa właściwości nawigacji, które mają zostać załadowane. Użyj `Load` metody, aby zrobić jawne ładowanie. Przykład:

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* [Z opóźnieniem ładowania](/ef/core/querying/related-data#lazy-loading). [Z opóźnieniem dodano do EF Core w wersji 2.1](/ef/core/querying/related-data#lazy-loading). Gdy jednostka jest odczytywana po raz pierwszy, powiązane dane nie są pobierane. Przy pierwszym dostępie do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie. Kwerenda jest wysyłana do bazy danych za każdym razem, gdy właściwość nawigacji jest dostępna po raz pierwszy.

* Operator `Select` ładuje tylko potrzebne powiązane dane.

## <a name="create-a-course-page-that-displays-department-name"></a>Tworzenie strony kursu z nazwą działu

Course Jednostki zawiera właściwość `Department` nawigacji, która zawiera jednostki. Jednostka `Department` zawiera dział, do której jest przypisany kurs.

Aby wyświetlić nazwę przypisanego działu na liście kursów:

* Pobierz `Name` właściwość `Department` z jednostki.
* Jednostka `Department` pochodzi z `Course.Department` właściwości nawigacji.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Rusztowanie modelu kursu

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio) 

Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i używać `Course` dla klasy modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Poprzednie polecenie scaffolds `Course` modelu. Otwórz projekt w programie Visual Studio.

Otwórz *strony/kursy/index.cshtml.cs* i `OnGetAsync` sprawdź metodę. Aparat rusztowań określone gorliwe ładowanie dla właściwości `Department` nawigacji. Metoda `Include` określa wczesne ładowanie.

Uruchom aplikację i wybierz **łącze Kursy.** W kolumnie `DepartmentID`działu jest wyświetlany program , który nie jest przydatny.

Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Poprzedni kod dodaje `AsNoTracking`. `AsNoTracking`zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone. Jednostki nie są śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.

Aktualizuj *strony/kursy/index.cshtml* z następującymi wyróżnionymi znacznikami:

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

W kodzie rusztowania wprowadzono następujące zmiany:

* Zmieniono nagłówek z Indeks na Kursy.
* Dodano kolumnę **Liczba,** która pokazuje wartość `CourseID` właściwości. Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle są one bez znaczenia dla użytkowników końcowych. Jednak w tym przypadku klucz podstawowy ma znaczenie.
* Zmieniono kolumnę **Dział,** aby wyświetlić nazwę działu. Kod wyświetla `Name` właściwość `Department` jednostki, która jest `Department` ładowana do właściwości nawigacji:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uruchom aplikację i wybierz kartę **Kursy,** aby wyświetlić listę z nazwami działów.

![Strona Indeks kursów](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Ładowanie powiązanych danych za pomocą opcji Wybierz

Metoda `OnGetAsync` ładuje powiązane dane `Include` za pomocą metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Operator `Select` ładuje tylko potrzebne powiązane dane. Dla pojedynczych elementów, takich `Department.Name` jak używa SQL INNER JOIN. Dla kolekcji używa innego dostępu do bazy `Include` danych, ale tak samo operator w kolekcjach.

Następujący kod ładuje powiązane `Select` dane za pomocą metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

W: `CourseViewModel`

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Zobacz [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) aby uzyskać pełny przykład.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Tworzenie strony Instruktorzy z kursami i rejestracjami

W tej sekcji zostanie utworzona strona Instruktorzy.

<a name="IP"></a>
![Strona Indeks instruktorów](read-related-data/_static/instructors-index.png)

Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:

* Lista instruktorów wyświetla powiązane dane `OfficeAssignment` z jednostki (Office w poprzednim obrazie). I `Instructor` `OfficeAssignment` jednostki są w relacji jeden do zera lub jeden. Wczesne ładowanie jest `OfficeAssignment` używane dla jednostek. Wczesne ładowanie jest zazwyczaj bardziej wydajne, gdy powiązane dane muszą być wyświetlane. W takim przypadku są wyświetlane zadania biurowe dla instruktorów.
* Gdy użytkownik wybierze instruktora (Harui w poprzednim `Course` obrazie), wyświetlane są powiązane encje. I `Instructor` `Course` jednostki są w relacji wiele do wielu. Wczesne ładowanie jest `Course` używane dla jednostek i ich jednostek powiązanych. `Department` W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebne są tylko kursy dla wybranego instruktora. W tym przykładzie pokazano, jak używać ładowania chętnych dla właściwości nawigacji w jednostkach, które znajdują się we właściwościach nawigacji.
* Gdy użytkownik wybierze kurs (Chemia na poprzednim obrazie), wyświetlane są powiązane dane z `Enrollments` encji. Na poprzednim obrazie wyświetlana jest nazwa ucznia i ocena. I `Course` `Enrollment` jednostki są w relacji jeden do wielu.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Tworzenie modelu widoku dla widoku Indeks instruktora

Strona instruktorów zawiera dane z trzech różnych tabel. Tworzony jest model widoku, który zawiera trzy jednostki reprezentujące trzy tabele.

W folderze *SchoolViewModels* utwórz *InstructorIndexData.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Rusztowanie modelu instructor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio) 

Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i używać `Instructor` dla klasy modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Poprzednie polecenie scaffolds `Instructor` modelu. 
Uruchom aplikację i przejdź do strony instruktorów.

Zastąp *strony/instruktorzy/index.cshtml.cs* następującym kodem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

Metoda `OnGetAsync` akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.

Sprawdź kwerendę w pliku *Pages/Instructors/Index.cshtml.cs:*

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Kwerenda zawiera dwa:

* `OfficeAssignment`: Wyświetlane w [widoku instruktorów](#IP).
* `CourseAssignments`: Co przynosi w kursach nauczanych.

### <a name="update-the-instructors-index-page"></a>Aktualizowanie strony indeksu instruktorów

Aktualizuj *strony/instruktorów/index.cshtml* za pomocą następujących znaczników:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Poprzedni znacznik wprowadza następujące zmiany:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"`. `"{id:int?}"`jest szablonem trasy. Szablon trasy zmienia ciągi zapytania liczby całkowitej w adresie URL, aby rozsyłać dane. Na przykład kliknięcie łącza **Wybierz** dla instruktora `@page` tylko z dyrektywą powoduje uzyskanie następującego adresu URL:

  `http://localhost:1234/Instructors?id=2`

  Gdy dyrektywa strony `@page "{id:int?}"`jest , poprzedni adres URL jest:

  `http://localhost:1234/Instructors/2`

* Tytuł strony to **Instruktorzy**.
* Dodano kolumnę **pakietu Office,** która jest wyświetlana `item.OfficeAssignment.Location` tylko wtedy, `item.OfficeAssignment` gdy nie ma wartości null. Ponieważ jest to relacja jeden do zera lub jeden, może nie być powiązana encja OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Dodano kolumnę **Kursy,** w której wyświetlane są kursy prowadzone przez każdego instruktora. Zobacz [Jawne przejście wiersza,](xref:mvc/views/razor#explicit-line-transition) aby uzyskać więcej informacji na temat tej składni maszynki do golenia.

* Dodano kod, który `class="success"` dynamicznie dodaje do `tr` elementu wybranego instruktora. Spowoduje to ustawienie koloru tła dla wybranego wiersza przy użyciu klasy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Dodano nowe hiperłącze z etykietą **Wybierz**. To łącze wysyła identyfikator wybranego `Index` instruktora do metody i ustawia kolor tła.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uruchom aplikację i wybierz kartę **Instruktorzy.** Strona wyświetla `Location` (biuro) z `OfficeAssignment` jednostki pokrewne. Jeśli officeassignment' ma wartość null, wyświetlana jest pusta komórka tabeli.

Kliknij łącze **Wybierz.** Zmienia się styl wiersza.

### <a name="add-courses-taught-by-selected-instructor"></a>Dodawanie kursów prowadzonych przez wybranego instruktora

Zaktualizuj `OnGetAsync` metodę w *pages/instructors/index.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Dodaj`public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Sprawdź zaktualizowaną kwerendę:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Poprzednia kwerenda `Department` dodaje jednostki.

Poniższy kod jest wykonywany po`id != null`wybraniu instruktora ( ). Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku. `Courses` Właściwość modelu widoku jest ładowana z `Course` jednostek z właściwości nawigacji tego instruktora. `CourseAssignments`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Metoda `Where` zwraca kolekcję. W poprzedniej `Where` metodzie zwracana `Instructor` jest tylko pojedyncza jednostka. Metoda `Single` konwertuje kolekcję `Instructor` na jedną jednostkę. Jednostka `Instructor` zapewnia dostęp `CourseAssignments` do właściwości. `CourseAssignments`zapewnia dostęp do `Course` powiązanych jednostek.

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

Metoda `Single` jest używana w kolekcji, gdy kolekcja ma tylko jeden element. Metoda `Single` zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element. Alternatywą `SingleOrDefault`jest , który zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta. Korzystanie `SingleOrDefault` z pustej kolekcji:

* Powoduje wyjątek (od próby `Courses` znalezienia właściwości na odwołanie null).
* Komunikat o wyjątku będzie mniej wyraźnie wskazać przyczynę problemu.

Następujący kod wypełnia `Enrollments` właściwość modelu widoku po wybraniu kursu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Dodaj następujące znaczniki na końcu *strony Pages/Instructors/Index.cshtml* Razor:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Poprzedni znaczników wyświetla listę kursów związanych z instruktorem, gdy instruktor jest wybrany.

Testowanie aplikacji. Kliknij łącze **Wybierz** na stronie instruktorów.

### <a name="show-student-data"></a>Pokaż dane uczniów

W tej sekcji aplikacja jest aktualizowana, aby wyświetlić dane uczniów dla wybranego kursu.

Zaktualizuj `OnGetAsync` kwerendę w metodzie *Pages/Instructors/Index.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Aktualizuj *strony/instruktorzy/index.cshtml*. Dodaj następujące znaczniki na końcu pliku:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

W poprzednim znaczniku wyświetlana jest lista uczniów, którzy zostali zapisani na wybrany kurs.

Odśwież stronę i wybierz instruktora. Wybierz kurs, aby wyświetlić listę zarejestrowanych uczniów i ich ocen.

![Instruktorzy Indeks strony instruktora i wybranego kursu](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Korzystanie z pojedynczego

Metoda `Single` może przejść `Where` w warunku `Where` zamiast wywoływania metody oddzielnie:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Powyższe `Single` podejście nie zapewnia `Where`żadnych korzyści w stosunku do korzystania . Niektórzy deweloperzy `Single` preferują styl podejścia.

## <a name="explicit-loading"></a>Jawne ładowanie

Bieżący kod określa wczesne `Enrollments` `Students`ładowanie i:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Załóżmy, że użytkownicy rzadko chcą zobaczyć rejestracje w trakcie. W takim przypadku optymalizacja będzie tylko załadować dane rejestracji, jeśli jest to wymagane. W tej sekcji `OnGetAsync` jest aktualizowana w `Enrollments` `Students`celu użycia jawnego ładowania i .

Zaktualizuj `OnGetAsync` następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Poprzedni kod porzuca *ThenInclude* metoda wywołuje rejestracji i danych studenta. Jeśli kurs jest zaznaczony, wyróżniony kod pobiera:

* Encje `Enrollment` dla wybranego kursu.
* Jednostki `Student` dla `Enrollment`każdego .

Zwróć uwagę na powyższe komentarze do kodu `.AsNoTracking()`. Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.

Testowanie aplikacji. Z punktu widzenia użytkowników aplikacja zachowuje się identycznie jak poprzednia wersja.

Następny samouczek pokazuje, jak zaktualizować powiązane dane.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja YouTube tego samouczka (część1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Wersja YouTube tego samouczka (część2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Poprzedni](xref:data/ef-rp/complex-data-model)
>[następny](xref:data/ef-rp/update-related-data)

::: moniker-end
