---
title: Część 6 Razor strony z EF Core w ASP.NET Core — Odczytaj powiązane dane
author: rick-anderson
description: Część 6 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061070"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Część 6 Razor strony z EF Core w ASP.NET Core — Odczytaj powiązane dane

Autorzy [Dykstra](https://github.com/tdykstra), [Jan P Kowalski](https://twitter.com/thereformedprog)i [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku pokazano, jak odczytywać i wyświetlać powiązane dane. Powiązane dane to dane, które EF Core ładowane do właściwości nawigacji.

Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:

![Strona indeksu kursów](read-related-data/_static/courses-index30.png)

![Strona indeksu instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Eager, jawne i ładowanie z opóźnieniem

Istnieje kilka sposobów, EF Core mogą ładować powiązane dane do właściwości nawigacji jednostki:

* [Ładowanie eager](/ef/core/querying/related-data#eager-loading). Ładowanie eager jest, gdy zapytanie dla jednego typu jednostki również ładuje powiązane jednostki. Po odczytaniu jednostki pobierane są powiązane z nią dane. Zwykle powoduje to pojedyncze zapytanie sprzężenia, które pobiera wszystkie dane, które są zbędne. EF Core będzie wystawiał wiele zapytań dla niektórych typów ładowania eager. Wygenerowanie wielu zapytań może być bardziej wydajne niż bardzo duże pojedyncze zapytanie. Ładowanie eager jest określone przy użyciu `Include` `ThenInclude` metod i.

  ![Przykład ładowania eager](read-related-data/_static/eager-loading.png)
 
  Podczas ładowania eager są wysyłane wiele zapytań, gdy zostanie dołączona Nawigacja kolekcji:

  * Jedno zapytanie dotyczące głównej kwerendy 
  * Jedno zapytanie dla każdej kolekcji "Edge" w drzewie ładowania.

* Oddziel zapytania z `Load` : dane można pobrać w oddzielnych zapytaniach, a EF Core "naprawia" właściwości nawigacji. "Rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji. Osobne zapytania i `Load` są bardziej podobne do jawnego ładowania niż ładowanie eager.

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  **Uwaga:** EF Core automatycznie naprawia właściwości nawigacji do wszystkich innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu. Nawet jeśli dane dla właściwości nawigacji *nie* są jawnie uwzględniane, właściwość można nadal wypełnić, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.

* [Jawne ładowanie](/ef/core/querying/related-data#explicit-loading). Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane. Kod musi być zapisany, aby można było pobrać powiązane dane, gdy jest to konieczne. Jawne ładowanie z oddzielnymi zapytania powoduje wysłanie wielu zapytań do bazy danych. W przypadku jawnego ładowania kod określa właściwości nawigacji do załadowania. Użyj `Load` metody, aby przeprowadzić jawne ładowanie. Przykład:

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* [Ładowanie z opóźnieniem](/ef/core/querying/related-data#lazy-loading). Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane. Podczas pierwszego uzyskiwania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie. Zapytanie jest wysyłane do bazy danych przy każdym dostępie do właściwości nawigacji po raz pierwszy. Ładowanie z opóźnieniem może obniżyć wydajność, na przykład gdy deweloperzy używają wzorców N + 1, ładując element nadrzędny i wyliczający za pomocą elementów podrzędnych.

## <a name="create-course-pages"></a>Tworzenie stron kursu

`Course`Jednostka zawiera właściwość nawigacji, która zawiera powiązaną `Department` jednostkę.

![Kurs. Dział](read-related-data/_static/dep-crs.png)

Aby wyświetlić nazwę przypisanego działu dla kursu:

* Załaduj powiązaną `Department` jednostkę do `Course.Department` właściwości nawigacji.
* Pobierz nazwę z `Department` `Name` właściwości jednostki.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Strony kursu szkieletowego

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Postępuj zgodnie z instrukcjami na [stronach uczniów tworzenia szkieletów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:

  * Utwórz folder *strony/kursy* .
  * Użyj `Course` dla klasy model.
  * Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Utwórz folder *strony/kursy* .

* Uruchom następujące polecenie, aby połączyć strony kursu.

  **W systemie Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **W systemie Linux lub macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Otwórz *stronę/kursy/index. cshtml. cs* i Przeanalizuj `OnGetAsync` metodę. Aparat tworzenia szkieletów określił eager ładowania dla `Department` właściwości nawigacji. `Include`Metoda określa eager ładowania.

* Uruchom aplikację i wybierz łącze **kursy** . W kolumnie dział zostanie wyświetlona wartość `DepartmentID` , która nie jest przydatna.

### <a name="display-the-department-name"></a>Wyświetl nazwę działu

Zaktualizuj strony/kursy/index. cshtml. cs przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Poprzedni kod zmienia `Course` Właściwość na `Courses` i dodaje `AsNoTracking` . `AsNoTracking` zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone. Nie trzeba śledzić jednostek, ponieważ nie są one aktualizowane w bieżącym kontekście.

Zaktualizuj *strony/kursy/index. cshtml* przy użyciu następującego kodu.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

W kodzie szkieletowym wprowadzono następujące zmiany:

* Zmieniono `Course` nazwę właściwości na `Courses` .
* Dodano kolumnę **liczbową** , która wyświetla `CourseID` wartość właściwości. Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle nie są oznaczane przez użytkowników końcowych. Jednak w tym przypadku klucz podstawowy ma znaczenie.
* Zmieniono kolumnę **działu** , aby wyświetlić nazwę działu. Kod wyświetla `Name` Właściwość `Department` jednostki, która jest ładowana do `Department` właściwości nawigacji:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uruchom aplikację i wybierz kartę **kursy** , aby wyświetlić listę z nazwami działów.

![Strona indeksu kursów](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Ładowanie powiązanych danych przy użyciu opcji Select

`OnGetAsync`Metoda ładuje powiązane dane przy użyciu `Include` metody. `Select`Metoda jest alternatywą, która ładuje tylko powiązane dane. Dla pojedynczych elementów, podobnie jak w przypadku `Department.Name` użycia sprzężenia wewnętrznego SQL. W przypadku kolekcji program używa innego dostępu do bazy danych, ale w związku z tym wykonuje `Include` operator dla kolekcji.

Poniższy kod ładuje powiązane dane przy użyciu `Select` metody:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Poprzedni kod nie zwraca żadnych typów jednostek, dlatego śledzenie nie jest wykonywane. Aby uzyskać więcej informacji na temat śledzenia EF, zobacz [śledzenie a No-Tracking zapytań](/ef/core/querying/tracking).

`CourseViewModel`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Zobacz [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) , aby zapoznać się z kompletnym przykładem.

## <a name="create-instructor-pages"></a>Utwórz strony instruktora

Ta sekcja szkieletuje strony instruktorów i dodaje powiązane kursy i rejestracje do strony indeksu instruktorów.

<a name="IP"></a>
![Strona indeksu instruktorów](read-related-data/_static/instructors-index30.png)

Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:

* Lista instruktorów wyświetla powiązane dane z `OfficeAssignment` jednostki (Office na powyższym obrazie). `Instructor`Jednostki i `OfficeAssignment` znajdują się w relacji jeden-do-zero-lub-jednego. Eager ładowania jest używany dla `OfficeAssignment` jednostek. Ładowanie eager jest zwykle wydajniejsze, gdy wymagane jest wyświetlenie powiązanych danych. W takim przypadku wyświetlane są przypisania pakietu Office dla instruktorów.
* Gdy użytkownik wybierze instruktora, `Course` wyświetlane są powiązane jednostki. `Instructor`Jednostki i `Course` znajdują się w relacji wiele-do-wielu. Ładowanie eager jest używane dla `Course` jednostek i ich powiązanych `Department` jednostek. W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebują tylko kursów dla wybranego instruktora. Ten przykład pokazuje, jak używać eager ładowania dla właściwości nawigacji w jednostkach, które są we właściwościach nawigacji.
* Gdy użytkownik wybierze kurs, zostaną wyświetlone powiązane dane z `Enrollments` jednostki. Na powyższym obrazie wyświetlana jest nazwa ucznia i jej klasy. `Course`Jednostki i `Enrollment` znajdują się w relacji jeden do wielu.

### <a name="create-a-view-model"></a>Tworzenie modelu widoku

Na stronie instruktorzy są wyświetlane dane z trzech różnych tabel. Wymagany jest model widoku, który zawiera trzy właściwości reprezentujące trzy tabele.

Utwórz *SchoolViewModels/InstructorIndexData. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Strony instruktorów dla szkieletów

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Postępuj zgodnie z instrukcjami w temacie Tworzenie [szkieletu stron uczniów](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:

  * Utwórz folder *stron/instruktorów* .
  * Użyj `Instructor` dla klasy model.
  * Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Utwórz folder *stron/instruktorów* .

* Uruchom następujące polecenie, aby połączyć strony instruktora.

  **W systemie Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **W systemie Linux lub macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Aby sprawdzić, jak wygląda strona szkieletowa przed aktualizacją, uruchom aplikację i przejdź do strony instruktorzy.

Aktualizowanie *stron/instruktorów/index. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

`OnGetAsync`Metoda akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.

Zbadaj zapytanie w pliku */instruktors/index. cshtml. cs* :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Kod określa eager ładowania dla następujących właściwości nawigacji:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Zwróć uwagę na powtarzanie `Include` i `ThenInclude` metody dla `CourseAssignments` i `Course` . To powtórzenie jest niezbędne do określenia ładowania eager dla dwóch właściwości nawigacji `Course` jednostki.

Poniższy kod jest wykonywany po wybraniu instruktora ( `id != null` ).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku. Właściwość widoku modelu `Courses` jest ładowana z `Course` jednostkami z tej `CourseAssignments` właściwości nawigacji instruktora.

`Where`Metoda zwraca kolekcję. Ale w tym przypadku filtr wybierze pojedynczą jednostkę, więc `Single` Metoda jest wywoływana w celu przekonwertowania kolekcji na jedną `Instructor` jednostkę. `Instructor`Jednostka zapewnia dostęp do `CourseAssignments` właściwości. `CourseAssignments` zapewnia dostęp do powiązanych `Course` jednostek.

![M:M instruktora do kursu](complex-data-model/_static/courseassignment.png)

`Single`Metoda jest używana w kolekcji, gdy kolekcja zawiera tylko jeden element. `Single`Metoda zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element. Alternatywą jest `SingleOrDefault` , która zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta.

Poniższy kod wypełnia właściwość modelu widoku w `Enrollments` przypadku wybrania kursu:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Aktualizowanie strony indeksu instruktorów

Aktualizowanie *stron/instruktorów/index. cshtml* przy użyciu następującego kodu.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Poprzedni kod wprowadza następujące zmiany:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"` . `"{id:int?}"` jest szablonem trasy. Szablon trasy zmienia ciągi zapytań liczb całkowitych w adresie URL, aby przesyłać dane. Na przykład kliknięcie linku **Wybierz** dla instruktora z tylko `@page` dyrektywą spowoduje utworzenie adresu URL w następujący sposób:

  `https://localhost:5001/Instructors?id=2`

  Gdy dyrektywa Page ma wartość `@page "{id:int?}"` , adres URL to:

  `https://localhost:5001/Instructors/2`

* Dodaje kolumnę **pakietu Office** , która `item.OfficeAssignment.Location` jest wyświetlana tylko wtedy, gdy `item.OfficeAssignment` nie ma wartości null. Ponieważ jest to relacja "jeden do zera" lub "jeden", nie może być powiązana jednostka OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Dodaje kolumnę **kursów** , która wyświetla nauczanie kursów przez każdego instruktora. Aby uzyskać więcej informacji na temat tej składni Razor, zobacz [jawne przejście liniowe](xref:mvc/views/razor#explicit-line-transition) .

* Dodaje kod, który dynamicznie dodaje `class="success"` do `tr` elementu wybranego instruktora i kursu. Ustawia kolor tła dla wybranego wiersza przy użyciu klasy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Dodaje nowe hiperłącze z etykietą **SELECT**. Ten link powoduje wysłanie wybranego identyfikatora instruktora do `Index` metody i ustawienie koloru tła.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Dodaje tabelę kursów dla wybranego instruktora.

* Dodaje tabelę rejestracji uczniów dla wybranego kursu.

Uruchom aplikację i wybierz kartę **Instruktorzy** . Na stronie zostanie wyświetlona strona `Location` (Biuro) z jednostki powiązanej `OfficeAssignment` . Jeśli `OfficeAssignment` ma wartość null, zostanie wyświetlona pusta komórka tabeli.

Kliknij link **Wybierz** dla instruktora. Zostaną wyświetlone zmiany w stylu wiersza i kursy przypisane do tego instruktora.

Wybierz kurs, aby zobaczyć listę zarejestrowanych studentów i ich klasy.

![Wybrany instruktor i kurs dla instruktorów](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Korzystanie z jednego

`Single`Metoda może przekazać `Where` warunek zamiast wywołania `Where` metody osobno:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Użycie `Single` z warunkiem WHERE jest kwestią preferencji osobistych. Nie zapewnia żadnych korzyści z używania `Where` metody.

## <a name="explicit-loading"></a>Jawne ładowanie

Bieżący kod określa eager ładowania dla `Enrollments` i `Students` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Załóżmy, że użytkownicy rzadko chcą widzieć rejestracje w kursie. W takim przypadku Optymalizacja będzie ładować dane rejestracji tylko wtedy, gdy jest to wymagane. W tej sekcji `OnGetAsync` zostanie zaktualizowany w celu użycia jawnego ładowania `Enrollments` i `Students` .

Aktualizowanie *stron/instruktorów/index. cshtml. cs* przy użyciu następującego kodu.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Poprzedni kod odrzuca metody *ThenInclude* w celu uzyskania danych dotyczących rejestracji i uczniów. W przypadku wybrania kursu jawny kod ładowania pobiera:

* `Enrollment`Jednostki wybranego kursu.
* `Student`Jednostki dla każdej z `Enrollment` nich.

Zwróć uwagę, że poprzedzający kod komentarz ma wartość `.AsNoTracking()` . Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.

Testowanie aplikacji. Z perspektywy użytkownika aplikacja zachowuje się identycznie z poprzednią wersją.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku pokazano, jak zaktualizować powiązane dane.

>[!div class="step-by-step"]
>[Poprzedni samouczek](xref:data/ef-rp/complex-data-model) 
> [Następny samouczek](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku dane pokrewne są odczytywane i wyświetlane. Powiązane dane to dane, które EF Core ładowane do właściwości nawigacji.

Jeśli występują problemy, których nie można rozwiązać, [Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instrukcje pobierania](xref:index#how-to-download-a-sample).

Na poniższych ilustracjach przedstawiono ukończone strony dla tego samouczka:

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Eager, jawne i opóźnione ładowanie powiązanych danych

Istnieje kilka sposobów, EF Core mogą ładować powiązane dane do właściwości nawigacji jednostki:

* [Ładowanie eager](/ef/core/querying/related-data#eager-loading). Ładowanie eager jest, gdy zapytanie dla jednego typu jednostki również ładuje powiązane jednostki. Po odczytaniu jednostki są pobierane powiązane dane. Zwykle powoduje to pojedyncze zapytanie sprzężenia, które pobiera wszystkie dane, które są zbędne. EF Core będzie wystawiał wiele zapytań dla niektórych typów ładowania eager. Wygenerowanie wielu zapytań może być bardziej wydajne niż w przypadku niektórych zapytań w EF6, w których wystąpiło pojedyncze zapytanie. Ładowanie eager jest określone przy użyciu `Include` `ThenInclude` metod i.

  ![Przykład ładowania eager](read-related-data/_static/eager-loading.png)
 
  Podczas ładowania eager są wysyłane wiele zapytań, gdy zostanie dołączona Nawigacja kolekcji:

  * Jedno zapytanie dotyczące głównej kwerendy 
  * Jedno zapytanie dla każdej kolekcji "Edge" w drzewie ładowania.

* Oddziel zapytania z `Load` : dane można pobrać w oddzielnych zapytaniach, a EF Core "naprawia" właściwości nawigacji. "rozwiązuje" oznacza, że EF Core automatycznie wypełnia właściwości nawigacji. Osobne zapytania i `Load` są bardziej podobne do jawnego ładowania niż ładowanie eager.

  ![Przykład oddzielnych zapytań](read-related-data/_static/separate-queries.png)

  Uwaga: EF Core automatycznie naprawia właściwości nawigacji do wszystkich innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu. Nawet jeśli dane dla właściwości nawigacji *nie* są jawnie uwzględniane, właściwość można nadal wypełnić, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.

* [Jawne ładowanie](/ef/core/querying/related-data#explicit-loading). Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane. Kod musi być zapisany, aby można było pobrać powiązane dane, gdy jest to konieczne. Jawne ładowanie z oddzielnymi zapytaniami powoduje wysłanie wielu zapytań do bazy danych. W przypadku jawnego ładowania kod określa właściwości nawigacji do załadowania. Użyj `Load` metody, aby przeprowadzić jawne ładowanie. Przykład:

  ![Przykład jawnego ładowania](read-related-data/_static/explicit-loading.png)

* [Ładowanie z opóźnieniem](/ef/core/querying/related-data#lazy-loading). [Ładowanie z opóźnieniem zostało dodane do EF Core w wersji 2,1](/ef/core/querying/related-data#lazy-loading). Gdy obiekt jest najpierw odczytywany, powiązane dane nie są pobierane. Podczas pierwszego uzyskiwania dostępu do właściwości nawigacji dane wymagane dla tej właściwości nawigacji są pobierane automatycznie. Zapytanie jest wysyłane do bazy danych przy każdym dostępie do właściwości nawigacji po raz pierwszy.

* `Select`Operator ładuje tylko powiązane dane.

## <a name="create-a-course-page-that-displays-department-name"></a>Tworzenie strony kursu, która wyświetla nazwę działu

Jednostka kursu zawiera właściwość nawigacji, która zawiera `Department` jednostkę. `Department`Jednostka zawiera dział, do którego jest przypisany kurs.

Aby wyświetlić nazwę przypisanego działu na liście kursów:

* Pobierz `Name` Właściwość z `Department` jednostki.
* `Department`Jednostka pochodzi z `Course.Department` właściwości nawigacji.

![Kurs. Dział](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Tworzenie szkieletu modelu kursu

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio) 

Postępuj zgodnie z instrukcjami w obszarze [szkieletem model ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i Użyj `Course` klasy modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Poprzednie polecenie szkieletuje `Course` model. Otwórz projekt w programie Visual Studio.

Otwórz *stronę/kursy/index. cshtml. cs* i Przeanalizuj `OnGetAsync` metodę. Aparat tworzenia szkieletów określił eager ładowania dla `Department` właściwości nawigacji. `Include`Metoda określa eager ładowania.

Uruchom aplikację i wybierz łącze **kursy** . W kolumnie dział zostanie wyświetlona wartość `DepartmentID` , która nie jest przydatna.

Zaktualizuj `OnGetAsync` metodę przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Poprzedni kod dodaje `AsNoTracking` . `AsNoTracking` zwiększa wydajność, ponieważ zwrócone jednostki nie są śledzone. Jednostki nie są śledzone, ponieważ nie są aktualizowane w bieżącym kontekście.

Aktualizuj *strony/kursy/index. cshtml* z następującymi wyróżnionymi znacznikami:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

W kodzie szkieletowym wprowadzono następujące zmiany:

* Zmieniono nagłówek z indeksu na kursy.
* Dodano kolumnę **liczbową** , która wyświetla `CourseID` wartość właściwości. Domyślnie klucze podstawowe nie są szkieletowe, ponieważ zwykle nie są oznaczane przez użytkowników końcowych. Jednak w tym przypadku klucz podstawowy ma znaczenie.
* Zmieniono kolumnę **działu** , aby wyświetlić nazwę działu. Kod wyświetla `Name` Właściwość `Department` jednostki, która jest ładowana do `Department` właściwości nawigacji:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uruchom aplikację i wybierz kartę **kursy** , aby wyświetlić listę z nazwami działów.

![Strona indeksu kursów](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Ładowanie powiązanych danych przy użyciu opcji Select

`OnGetAsync`Metoda ładuje powiązane dane przy użyciu `Include` metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select`Operator ładuje tylko powiązane dane. Dla pojedynczych elementów, podobnie jak w przypadku `Department.Name` użycia sprzężenia wewnętrznego SQL. W przypadku kolekcji program używa innego dostępu do bazy danych, ale w związku z tym wykonuje `Include` operator dla kolekcji.

Poniższy kod ładuje powiązane dane przy użyciu `Select` metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Zobacz [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) i [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) , aby zapoznać się z kompletnym przykładem.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Utwórz stronę instruktorów pokazującą kursy i rejestracje

W tej sekcji zostanie utworzona strona instruktorzy.

<a name="IP"></a>
![Strona indeksu instruktorów](read-related-data/_static/instructors-index.png)

Ta strona odczytuje i wyświetla powiązane dane w następujący sposób:

* Lista instruktorów wyświetla powiązane dane z `OfficeAssignment` jednostki (Office na powyższym obrazie). `Instructor`Jednostki i `OfficeAssignment` znajdują się w relacji jeden-do-zero-lub-jednego. Eager ładowania jest używany dla `OfficeAssignment` jednostek. Ładowanie eager jest zwykle wydajniejsze, gdy wymagane jest wyświetlenie powiązanych danych. W takim przypadku wyświetlane są przypisania pakietu Office dla instruktorów.
* Gdy użytkownik wybierze instruktora (Harui na poprzednim obrazie), `Course` zostaną wyświetlone powiązane jednostki. `Instructor`Jednostki i `Course` znajdują się w relacji wiele-do-wielu. Ładowanie eager jest używane dla `Course` jednostek i ich powiązanych `Department` jednostek. W takim przypadku oddzielne zapytania mogą być bardziej wydajne, ponieważ potrzebują tylko kursów dla wybranego instruktora. Ten przykład pokazuje, jak używać eager ładowania dla właściwości nawigacji w jednostkach, które są we właściwościach nawigacji.
* Gdy użytkownik wybierze kurs (chemia na powyższym obrazie), zostaną wyświetlone powiązane dane z `Enrollments` jednostki. Na powyższym obrazie wyświetlana jest nazwa ucznia i jej klasy. `Course`Jednostki i `Enrollment` znajdują się w relacji jeden do wielu.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Utwórz model widoku dla widoku indeksu instruktora

Na stronie instruktorzy są wyświetlane dane z trzech różnych tabel. Tworzony jest model widoku, który zawiera trzy jednostki reprezentujące trzy tabele.

W folderze *SchoolViewModels* Utwórz *InstructorIndexData.cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Tworzenie szkieletu modelu instruktora

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio) 

Postępuj zgodnie z instrukcjami w obszarze [szkieletem model ucznia](xref:data/ef-rp/intro#scaffold-the-student-model) i Użyj `Instructor` klasy modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Poprzednie polecenie szkieletuje `Instructor` model. 
Uruchom aplikację i przejdź do strony instruktorzy.

Zamień *strony/instruktorów/index. cshtml. cs* na następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync`Metoda akceptuje opcjonalne dane trasy dla identyfikatora wybranego instruktora.

Zbadaj zapytanie w pliku */instruktors/index. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Zapytanie zawiera dwa:

* `OfficeAssignment`: Wyświetlane w [widoku instruktorów](#IP).
* `CourseAssignments`: Co to jest w nauczaniu kursów.

### <a name="update-the-instructors-index-page"></a>Aktualizowanie strony indeksu instruktorów

Aktualizowanie *stron/instruktorów/index. cshtml* przy użyciu następującego znacznika:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Poprzedzające znaczniki wprowadzają następujące zmiany:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int?}"` . `"{id:int?}"` jest szablonem trasy. Szablon trasy zmienia ciągi zapytań liczb całkowitych w adresie URL, aby przesyłać dane. Na przykład kliknięcie linku **Wybierz** dla instruktora z tylko `@page` dyrektywą spowoduje utworzenie adresu URL w następujący sposób:

  `http://localhost:1234/Instructors?id=2`

  Gdy dyrektywa Page ma wartość `@page "{id:int?}"` , poprzedni adres URL to:

  `http://localhost:1234/Instructors/2`

* Tytuł strony to **Instruktorzy**.
* Dodano kolumnę **pakietu Office** , która `item.OfficeAssignment.Location` jest wyświetlana tylko wtedy, gdy `item.OfficeAssignment` nie ma wartości null. Ponieważ jest to relacja "jeden do zera" lub "jeden", nie może być powiązana jednostka OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Dodano kolumnę **kursów** , która wyświetla nauczanie kursów przez każdego instruktora. Aby uzyskać więcej informacji na temat tej składni Razor, zobacz [jawne przejście liniowe](xref:mvc/views/razor#explicit-line-transition) .

* Dodano kod, który dynamicznie dodaje `class="success"` do `tr` elementu wybranego instruktora. Ustawia kolor tła dla wybranego wiersza przy użyciu klasy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Dodano nowe hiperłącze z etykietą **SELECT**. Ten link powoduje wysłanie wybranego identyfikatora instruktora do `Index` metody i ustawienie koloru tła.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uruchom aplikację i wybierz kartę **Instruktorzy** . Na stronie zostanie wyświetlona strona `Location` (Biuro) z jednostki powiązanej `OfficeAssignment` . Jeśli OfficeAssignment ' ma wartość null, zostanie wyświetlona pusta komórka tabeli.

Kliknij link **Wybierz** . Styl wiersza zmienia się.

### <a name="add-courses-taught-by-selected-instructor"></a>Dodaj nauczanie kursów według wybranych instruktorów

Zaktualizuj `OnGetAsync` metodę na *stronach/instruktorów/index. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Dodana `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Zbadaj zaktualizowane zapytanie:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Poprzednie zapytanie dodaje `Department` jednostki.

Poniższy kod jest wykonywany po wybraniu instruktora ( `id != null` ). Wybrany instruktor jest pobierany z listy instruktorów w modelu widoku. Właściwość widoku modelu `Courses` jest ładowana z `Course` jednostkami z tej `CourseAssignments` właściwości nawigacji instruktora.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where`Metoda zwraca kolekcję. W poprzedniej `Where` metodzie zwracana jest tylko pojedyncza `Instructor` jednostka. `Single`Metoda konwertuje kolekcję na jedną `Instructor` jednostkę. `Instructor`Jednostka zapewnia dostęp do `CourseAssignments` właściwości. `CourseAssignments` zapewnia dostęp do powiązanych `Course` jednostek.

![M:M instruktora do kursu](complex-data-model/_static/courseassignment.png)

`Single`Metoda jest używana w kolekcji, gdy kolekcja zawiera tylko jeden element. `Single`Metoda zgłasza wyjątek, jeśli kolekcja jest pusta lub jeśli istnieje więcej niż jeden element. Alternatywą jest `SingleOrDefault` , która zwraca wartość domyślną (null w tym przypadku), jeśli kolekcja jest pusta. Używanie `SingleOrDefault` w pustej kolekcji:

* Wynikiem jest wyjątek (od próby znalezienia `Courses` właściwości w odwołaniu o wartości null).
* Komunikat o wyjątku będzie mniej jasno wskazywał przyczynę problemu.

Poniższy kod wypełnia właściwość modelu widoku w `Enrollments` przypadku wybrania kursu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Dodaj następujący znacznik na końcu strony */instruktorów/index. cshtml* Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Powyższy znacznik wyświetla listę kursów związanych z instruktorem w przypadku wybrania instruktora.

Testowanie aplikacji. Kliknij link **Wybierz** na stronie instruktorów.

### <a name="show-student-data"></a>Pokaż dane ucznia

W tej sekcji aplikacja zostanie zaktualizowana, aby wyświetlić dane uczniów dla wybranego kursu.

Zaktualizuj zapytanie w `OnGetAsync` metodzie na *stronach/instruktorów/index. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Aktualizowanie *stron/instruktorów/index. cshtml*. Dodaj następujący znacznik na końcu pliku:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Powyższy znacznik wyświetla listę studentów, którzy są zarejestrowani w wybranym kursie.

Odśwież stronę i wybierz instruktora. Wybierz kurs, aby zobaczyć listę zarejestrowanych studentów i ich klasy.

![Wybrany instruktor i kurs dla instruktorów](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Korzystanie z jednego

`Single`Metoda może przekazać `Where` warunek zamiast wywołania `Where` metody osobno:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Poprzednie `Single` podejście nie zapewnia żadnych korzyści w porównaniu z korzystaniem z programu `Where` . Niektórzy deweloperzy preferują `Single` styl podejścia.

## <a name="explicit-loading"></a>Jawne ładowanie

Bieżący kod określa eager ładowania dla `Enrollments` i `Students` :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Załóżmy, że użytkownicy rzadko chcą widzieć rejestracje w kursie. W takim przypadku Optymalizacja będzie ładować dane rejestracji tylko wtedy, gdy jest to wymagane. W tej sekcji `OnGetAsync` zostanie zaktualizowany w celu użycia jawnego ładowania `Enrollments` i `Students` .

Zaktualizuj `OnGetAsync` program przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Poprzedni kod odrzuca metody *ThenInclude* w celu uzyskania danych dotyczących rejestracji i uczniów. W przypadku wybrania kursu wyróżniony kod pobiera:

* `Enrollment`Jednostki wybranego kursu.
* `Student`Jednostki dla każdej z `Enrollment` nich.

Zwróć uwagę na powyższe komentarze w kodzie `.AsNoTracking()` . Właściwości nawigacji można jawnie załadować tylko dla śledzonych jednostek.

Testowanie aplikacji. Z perspektywy użytkowników aplikacja zachowuje się identycznie z poprzednią wersją.

W następnym samouczku pokazano, jak zaktualizować powiązane dane.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja tego samouczka usługi YouTube (part1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Wersja tego samouczka usługi YouTube (part2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Poprzedni](xref:data/ef-rp/complex-data-model) 
> [Dalej](xref:data/ef-rp/update-related-data)

::: moniker-end
