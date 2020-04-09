---
title: Strony brzytwy z EF Core w rdzeniu ASP.NET - Model danych - 5 z 8
author: rick-anderson
description: W tym samouczku dodaj więcej jednostek i relacji i dostosuj model danych, określając reguły formatowania, sprawdzania poprawności i mapowania.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665719"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Strony brzytwy z EF Core w rdzeniu ASP.NET - Model danych - 5 z 8

Przez [Tom Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Poprzednie samouczki pracował z podstawowym modelem danych, który składał się z trzech jednostek. W tym samouczku:

* Dodano więcej jednostek i relacji.
* Model danych jest dostosowywany przez określenie reguł formatowania, sprawdzania poprawności i mapowania bazy danych.

Ukończony model danych jest pokazany na poniższej ilustracji:

![Diagram encji](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Jednostka Student

![Jednostka studencka](complex-data-model/_static/student-entity.png)

Zastąp kod w *Models/Student.cs* następującym kodem:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Powyższy kod dodaje `FullName` właściwość i dodaje następujące atrybuty do istniejących właściwości:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>Właściwość obliczana fullname

`FullName`jest obliczoną właściwością, która zwraca wartość utworzoną przez łączenie dwóch innych właściwości. `FullName`nie można ustawić, więc ma tylko akcesor get. Żadna kolumna nie `FullName` jest tworzona w bazie danych.

### <a name="the-datatype-attribute"></a>Atrybut DataType

```csharp
[DataType(DataType.Date)]
```

W przypadku dat rejestracji uczniów wszystkie strony są obecnie wyświetlane o godzinie dnia wraz z datą, chociaż tylko data jest odpowiednia. Za pomocą atrybutów adnotacji danych, można wprowadzić jedną zmianę kodu, który naprawi format wyświetlania na każdej stronie, która pokazuje dane. 

[Atrybut DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) określa typ danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych. W takim przypadku powinna być wyświetlana tylko data, a nie data i godzina. [Wyliczenie datatype](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress, itp. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu. Przykład:

* Łącze `mailto:` jest tworzone `DataType.EmailAddress`automatycznie dla pliku .
* Selektor daty jest `DataType.Date` dostępny w większości przeglądarek.

Atrybut `DataType` emituje atrybuty `data-` HTML 5 (wymawiane rozdzielenie danych). Atrybuty `DataType` nie zapewniają sprawdzania poprawności.

### <a name="the-displayformat-attribute"></a>Atrybut DisplayFormat

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date`nie określa formatu daty, która jest wyświetlana. Domyślnie pole daty jest wyświetlane zgodnie z domyślnymi formatami opartymi na [programie CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serwera .

Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty. Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane do interfejsu użytkownika edycji. Niektóre pola nie `ApplyFormatInEditMode`powinny używać . Na przykład symbol waluty zazwyczaj nie powinien być wyświetlany w polu tekstowym edycji.

Atrybut `DisplayFormat` może być używany przez siebie. Ogólnie warto użyć atrybutu `DataType` z atrybutem. `DisplayFormat` Atrybut `DataType` przekazuje semantyki danych, w przeciwieństwie do sposobu renderowania go na ekranie. Atrybut `DataType` zapewnia następujące korzyści, które nie `DisplayFormat`są dostępne w:

* Przeglądarka może włączyć funkcje HTML5. Na przykład pokaż formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, łącza e-mail i sprawdzanie poprawności danych wejściowych po stronie klienta.
* Domyślnie przeglądarka renderuje dane przy użyciu poprawnego formatu opartego na ustawieniach regionalnych.

Aby uzyskać więcej informacji, zobacz [ \<dokumentację> Pomocnika tagów](xref:mvc/views/working-with-forms#the-input-tag-helper).

### <a name="the-stringlength-attribute"></a>Atrybut StringLength

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

Reguły sprawdzania poprawności danych i komunikaty o błędach sprawdzania poprawności można określić za pomocą atrybutów. [Atrybut StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) określa minimalną i maksymalną długość znaków dozwolonych w polu danych. Pokazany kod ogranicza nazwy do nie więcej niż 50 znaków. Przykład, który ustawia minimalną długość ciągu, jest wyświetlany [później](#the-required-attribute).

Atrybut `StringLength` zapewnia również weryfikacji po stronie klienta i po stronie serwera. Minimalna wartość nie ma wpływu na schemat bazy danych.

Atrybut `StringLength` nie uniemożliwia użytkownikowi wprowadzania odstępu dla nazwy. [Atrybut RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) może służyć do stosowania ograniczeń danych wejściowych. Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki alfabetycznie:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W **programie SQL Server Object Explorer** (SSOX) otwórz projektanta tabel uczniów, klikając dwukrotnie tabelę **Student.**

![Tabela studentów w SSOX przed migracją](complex-data-model/_static/ssox-before-migration.png)

Poprzedni obraz przedstawia schemat tabeli. `Student` Pola nazwy mają `nvarchar(MAX)`typ . Po utworzeniu i zastosowaniu migracji w dalszej `nvarchar(50)` części tego samouczka pola nazw stają się wynikiem atrybutów długości ciągu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

W narzędziu SQLite sprawdź definicje `Student` kolumn dla tabeli. Pola nazwy mają `Text`typ . Należy zauważyć, że pole `FirstMidName`imienia jest wywoływane . W następnej sekcji nazwę tej kolumny `FirstName`można zmienić na .

---

### <a name="the-column-attribute"></a>Atrybut Kolumna

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Atrybuty mogą kontrolować sposób mapowania klas i właściwości do bazy danych. W `Student` modelu `Column` atrybut jest używany do mapowania `FirstMidName` nazwy właściwości do "FirstName" w bazie danych.

Podczas tworzenia bazy danych nazwy właściwości w modelu są używane `Column` dla nazw kolumn (z wyjątkiem sytuacji, gdy używany jest atrybut). Model `Student` używa `FirstMidName` dla pola imienia, ponieważ pole może również zawierać drugie imię.

Za `[Column]` pomocą atrybutu `Student.FirstMidName` w modelu danych `FirstName` jest `Student` mapowana do kolumny tabeli. Dodanie atrybutu `Column` zmienia model, który `SchoolContext`wspiera program . Model kopii `SchoolContext` zapasowej nie jest już zgodny z bazą danych. Ta rozbieżność zostanie rozwiązana przez dodanie migracji w dalszej części tego samouczka.

### <a name="the-required-attribute"></a>Wymagany atrybut

```csharp
[Required]
```

Atrybut `Required` sprawia, że właściwości nazwy wymagane pola. Atrybut `Required` nie jest potrzebny dla typów nienastępalnych do wartości `DateTime`null, takich jak typy wartości (na przykład , `int`, i `double`). Typy, których nie można mieć wartości null, są automatycznie traktowane jako pola wymagane.

Atrybut `Required` musi być używany `MinimumLength` z `MinimumLength` do wymuszania.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength`i `Required` zezwolić na zaspokojenie sprawdzania poprawności. Użyj `RegularExpression` atrybutu dla pełnej kontroli nad ciągiem.

### <a name="the-display-attribute"></a>Atrybut Display

```csharp
[Display(Name = "Last Name")]
```

Atrybut `Display` określa, że podpis dla pól tekstowych powinien być "Imię", "Nazwisko", "Imię" i "Data rejestracji". Domyślne podpisy nie miały miejsca dzielącego wyrazy, na przykład "Nazwisko".

### <a name="create-a-migration"></a>Tworzenie migracji

Uruchom aplikację i przejdź do strony Uczniowie. Wyjątek zostanie zgłoszony. Atrybut `[Column]` powoduje, że EF oczekiwać, `FirstName`aby znaleźć kolumnę o `FirstMidName`nazwie , ale nazwa kolumny w bazie danych jest nadal .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Komunikat o błędzie jest podobny do następującego przykładu:

```
SqlException: Invalid column name 'FirstName'.
```

* W pmc wprowadź następujące polecenia, aby utworzyć nową migrację i zaktualizować bazę danych:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  Pierwsze z tych poleceń generuje następujący komunikat ostrzegawczy:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Ostrzeżenie jest generowane, ponieważ pola nazw są teraz ograniczone do 50 znaków. Jeśli nazwa w bazie danych ma więcej niż 50 znaków, 51 do ostatniego znaku zostaną utracone.

* Otwórz tabelę uczniów w SSOX:

  ![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

  Przed zastosowaniem migracji kolumny nazw były typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Kolumny nazw są `nvarchar(50)`teraz . Nazwa kolumny została `FirstMidName` zmieniona z na `FirstName`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komunikat o błędzie jest podobny do następującego przykładu:

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Otwórz okno polecenia w folderze projektu. Wprowadź następujące polecenia, aby utworzyć nową migrację i zaktualizować bazę danych:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Polecenie aktualizacji bazy danych wyświetla błąd, taki jak w poniższym przykładzie:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

W tym samouczku sposobem na ominięcia tego błędu jest usunięcie i ponowne utworzenie migracji początkowej. Aby uzyskać więcej informacji, zobacz notatkę o ostrzeżeniu SQLite w górnej części [samouczka migracji](xref:data/ef-rp/migrations).

* Usuń folder *Migracje.*
* Uruchom następujące polecenia, aby usunąć bazę danych, utworzyć nową migrację początkową i zastosować migrację:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* Sprawdź tabelę Student w narzędziu SQLite. Kolumna, która była FirstMidName jest teraz FirstName.

---

* Uruchom aplikację i przejdź do strony Uczniowie.
* Należy zauważyć, że godziny nie są wprowadzane ani wyświetlane wraz z datami.
* Wybierz **pozycję Utwórz nowy**i spróbuj wprowadzić nazwę dłuższą niż 50 znaków.

> [!Note]
> W poniższych sekcjach tworzenie aplikacji na niektórych etapach generuje błędy kompilatora. Instrukcje określają, kiedy utworzyć aplikację.

## <a name="the-instructor-entity"></a>Jednostka instruktora

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

Utwórz *modele/instructor.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Wiele atrybutów może znajdować się w jednym wierszu. Atrybuty `HireDate` mogą być zapisywane w następujący sposób:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Właściwości nawigacji

Właściwości `CourseAssignments` `OfficeAssignment` i są właściwości nawigacji.

Instruktor może uczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Instruktor może mieć co najwyżej jedno `OfficeAssignment` biuro, `OfficeAssignment` więc właściwość posiada jedną jednostkę. `OfficeAssignment`jest null, jeśli nie jest przypisany żaden urząd.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>Jednostka OfficeAssignment

![Encja officeassignment](complex-data-model/_static/officeassignment-entity.png)

Utwórz *modele/officeassignment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atrybut Klucz

Atrybut `[Key]` jest używany do identyfikowania właściwości jako klucz podstawowy (PK), gdy nazwa właściwości jest czymś innym niż classnameID lub ID.

Istnieje relacja jeden do zera lub jeden między `Instructor` jednostkami. `OfficeAssignment` Przypisanie biura istnieje tylko w odniesieniu do instruktora, do który jest przypisany. PK `OfficeAssignment` jest również jego klucz obcy (FK) do jednostki. `Instructor`

EF Core nie można `InstructorID` automatycznie rozpoznać `OfficeAssignment` jako `InstructorID` PK, ponieważ nie jest zgodna z konwencją nazewnictwa identyfikatora lub classnameID. W związku `Key` z tym atrybut `InstructorID` jest używany do identyfikowania jako PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Domyślnie EF Core traktuje klucz jako nieogenerowana przez bazę danych, ponieważ kolumna jest dla identyfikującej relacji.

### <a name="the-instructor-navigation-property"></a>Właściwość nawigacji instruktora

Właściwość `Instructor.OfficeAssignment` nawigacji może mieć wartość null, ponieważ może nie być wiersz `OfficeAssignment` dla danego instruktora. Instruktor może nie mieć przypisania biura.

Właściwość `OfficeAssignment.Instructor` nawigacji zawsze będzie mieć jednostkę `InstructorID` instruktora, ponieważ typ klucza obcego jest `int`, typ wartości nienastępującej wartości. Przypisanie biura nie może istnieć bez instruktora.

Gdy `Instructor` jednostka ma `OfficeAssignment` jednostkę pokrewną, każda jednostka ma odwołanie do drugiej w swojej właściwości nawigacji.

## <a name="the-course-entity"></a>Jednostka kursu

![Jednostka kursu](complex-data-model/_static/course-entity.png)

Zaktualizuj *modele/Course.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

Jednostka `Course` ma właściwość `DepartmentID`klucza obcego (FK). `DepartmentID`wskazuje na `Department` podmiot powiązany. Jednostka `Course` ma `Department` właściwość nawigacji.

EF Core nie wymaga właściwości klucza obcego dla modelu danych, gdy model ma właściwość nawigacji dla jednostki pokrewne. EF Core automatycznie tworzy pliki FK w bazie danych, gdziekolwiek są potrzebne. EF Core tworzy [właściwości cienia](/ef/core/modeling/shadow-properties) dla automatycznie tworzonych FKs. Jednak jawnie włączenie FK w modelu danych można ułatwić aktualizacje i bardziej wydajne. Rozważmy na przykład model, w `DepartmentID` którym właściwość FK *nie* jest uwzględniona. Po pobraniu jednostki kursu do edycji:

* Właściwość `Department` jest null, jeśli nie jest jawnie załadowany.
* Aby zaktualizować jednostkę `Department` kursu, jednostka musi najpierw zostać pobrana.

Gdy FK `DepartmentID` właściwość znajduje się w modelu danych, nie ma `Department` potrzeby pobierania jednostki przed aktualizacją.

### <a name="the-databasegenerated-attribute"></a>Atrybut DatabaseGenerated

Atrybut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` określa, że PK jest dostarczany przez aplikację, a nie generowane przez bazę danych.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Domyślnie EF Core zakłada, że wartości PK są generowane przez bazę danych. Generowane przez bazę danych jest zazwyczaj najlepszym rozwiązaniem. Dla `Course` jednostek użytkownik określa PK. Na przykład numer kursu, taki jak seria 1000 dla działu matematyki, seria 2000 dla działu angielskiego.

Atrybut `DatabaseGenerated` może również służyć do generowania wartości domyślnych. Na przykład baza danych może automatycznie wygenerować pole daty, aby zarejestrować datę utworzenia lub zaktualizowania wiersza. Aby uzyskać więcej informacji, zobacz [Wygenerowane właściwości](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego (FK) i `Course` właściwości nawigacji w jednostce odzwierciedlają następujące relacje:

Kurs jest przypisany do jednego działu, `DepartmentID` więc istnieje `Department` FK i właściwości nawigacji.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurs może mieć dowolną liczbę studentów zapisanych `Enrollments` w nim, więc właściwość nawigacji jest kolekcja:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs może być prowadzony przez wielu instruktorów, więc właściwość `CourseAssignments` nawigacji jest kolekcją:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`zostanie wyjaśnione [później](#many-to-many-relationships).

## <a name="the-department-entity"></a>Jednostka departamentu

![Jednostka działu](complex-data-model/_static/department-entity.png)

Utwórz *modele/department.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Atrybut Kolumna

Wcześniej `Column` atrybut był używany do zmiany mapowania nazw kolumn. W kodzie `Department` jednostki `Column` atrybut jest używany do zmiany mapowania typów danych SQL. Kolumna `Budget` jest definiowana przy użyciu typu pieniądza programu SQL Server w bazie danych:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapowanie kolumn zazwyczaj nie jest wymagane. EF Core wybiera odpowiedni typ danych programu SQL Server na podstawie typu CLR dla właściwości. Typ CLR `decimal` jest mapowany `decimal` na typ programu SQL Server. `Budget`jest dla waluty, a typ danych pieniądza jest bardziej odpowiedni dla waluty.

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

FK i właściwości nawigacji odzwierciedlają następujące relacje:

* Dział może, ale nie musi mieć administratora.
* Administrator jest zawsze instruktorem. W związku `InstructorID` z tym właściwość jest `Instructor` uwzględniona jako FK do jednostki.

Właściwość nawigacji `Administrator` jest nazwany, ale posiada jednostki: `Instructor`

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Znak zapytania (?) w poprzednim kodzie określa, że właściwość jest nullable.

Dział może mieć wiele kursów, więc istnieje właściwość nawigacji Kursy:

```csharp
public ICollection<Course> Courses { get; set; }
```

Zgodnie z konwencją EF Core umożliwia kaskadowe usuwanie dla nienastępalnych do null FK i dla relacji wiele do wielu. To domyślne zachowanie może spowodować reguły usuwania kaskady kołowych. Reguły usuwania kaskadowego cyklicznego powodują wyjątek po dodaniu migracji.

Na przykład jeśli `Department.InstructorID` właściwość została zdefiniowana jako nie można nullable, EF Core skonfiguruje regułę usuwania kaskadowego. W takim przypadku dział zostanie usunięty, gdy instruktor przypisany jako administrator zostanie usunięty. W tym scenariuszu reguła ograniczenia miałaby większy sens. Poniższy [płynny interfejs API](#fluent-api-alternative-to-attributes) ustawi regułę ograniczania i wyłączy usuwanie kaskadowe.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Jednostka Rejestracji

Rekord rejestracji jest dla jednego kursu podjętego przez jednego ucznia.

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

Zaktualizuj *modele/enrollment.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości FK i właściwości nawigacji odzwierciedlają następujące relacje:

Rekord rejestracji jest dla jednego kursu, `CourseID` więc istnieje właściwość FK i właściwość `Course` nawigacji:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Rekord rejestracji jest dla jednego ucznia, `StudentID` więc istnieje `Student` właściwość FK i właściwość nawigacji:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relacje wiele do wielu

Istnieje wiele do wielu relacji między `Student` i `Course` jednostek. Jednostka `Enrollment` działa jako tabela sprzężenia wiele do wielu *z ładunkiem* w bazie danych. "Z ładunkiem" oznacza, że `Enrollment` tabela zawiera dodatkowe dane oprócz FK dla tabel `Grade`połączonych (w tym przypadku PK i ).

Na poniższej ilustracji przedstawiono, jak wyglądają te relacje na diagramie jednostek. (Ten diagram został wygenerowany przy użyciu [ef power tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) dla EF 6.x. Tworzenie diagramu nie jest częścią samouczka.)

![Student-Kurs wielu do wielu relacji](complex-data-model/_static/student-course.png)

Każda linia relacji ma 1 na jednym końcu i gwiazdkę (*) na drugim, wskazując relację jeden do wielu.

Jeśli `Enrollment` tabela nie zawiera informacji o gatunku, musiałaby zawierać`CourseID` tylko `StudentID`dwa FK (i ). Tabela sprzężeń wiele do wielu bez ładunku jest czasami nazywana tabelą czystego sprzężenia (PJT).

I `Instructor` `Course` jednostek mają relację wiele do wielu przy użyciu tabeli sprzężenia czystego.

Uwaga: EF 6.x obsługuje tabele sprzężenia niejawnego dla relacji wiele-do-wielu, ale EF Core nie. Aby uzyskać więcej informacji, zobacz [Relacje wiele do wielu w EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Jednostka CourseAssignment

![Jednostka courseAssignment](complex-data-model/_static/courseassignment-entity.png)

Utwórz *modele/courseAssignment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Relacja Instructor-to-Courses wiele-do-wielu wymaga tabeli sprzężenia, a encja dla tej tabeli sprzężenia jest CourseAssignment.

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

Często nazywa się encję `EntityName1EntityName2`sprzężenia . Na przykład instructor-to-kursy dołączyć tabeli przy `CourseInstructor`użyciu tego wzorca będzie . Jednak zaleca się użycie nazwy opisującej relację.

Modele danych zaczynają się proste i rosną. Dołączanie do tabel bez ładunku (PJTs) często ewoluuje w celu uwzględnienia ładunku. Zaczynając od opisowej nazwy encji, nazwa nie musi się zmieniać po zmianie tabeli sprzężenia. W idealnym przypadku jednostka sprzężenia miałaby własną naturalną (ewentualnie pojedynczą nazwę) w domenie biznesowej. Na przykład książki i klienci mogą być połączone z jednostką sprzężenia o nazwie Oceny. Dla instruktora do kursów wiele do wielu `CourseAssignment` relacji, `CourseInstructor`jest preferowany nad .

### <a name="composite-key"></a>Klucz złożony

Dwa FKs `CourseAssignment` w`InstructorID` `CourseID`( i ) razem jednoznacznie zidentyfikować każdy wiersz `CourseAssignment` tabeli. `CourseAssignment`nie wymaga dedykowanego PK. I `InstructorID` `CourseID` właściwości działają jako złożona PK. Jedynym sposobem określenia złożonych PKs do EF Core jest *płynny interfejs API*. W następnej sekcji pokazano, jak skonfigurować złożoną PK.

Klucz złożony zapewnia, że:

* Wiele wierszy są dozwolone dla jednego kursu.
* Wiele wierszy są dozwolone dla jednego instruktora.
* Wiele wierszy nie są dozwolone dla tego samego instruktora i kursu.

Jednostka `Enrollment` sprzężenia definiuje własną PK, więc duplikaty tego rodzaju są możliwe. Aby zapobiec takim duplikatom:

* Dodaj unikatowy indeks w polach FK lub
* Skonfiguruj `Enrollment` za pomocą `CourseAssignment`podstawowego klucza kompozytowego podobnego do . Aby uzyskać więcej informacji, zobacz [Indeksy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizowanie kontekstu bazy danych

Zaktualizuj *plik Data/SchoolContext.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Powyższy kod dodaje nowe jednostki i `CourseAssignment` konfiguruje złożoną PK jednostki.

## <a name="fluent-api-alternative-to-attributes"></a>Płynna alternatywa dla API dla atrybutów

Metoda `OnModelCreating` w poprzednim kodzie używa *płynnego interfejsu API* do konfigurowania zachowania EF Core. Interfejs API jest nazywany "fluent", ponieważ jest często używany przez ciągnie serię wywołań metody razem w jednej instrukcji. [Poniższy kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) jest przykładem płynnego interfejsu API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

W tym samouczku płynny interfejs API jest używany tylko do mapowania bazy danych, którego nie można wykonać za pomocą atrybutów. Jednak płynny interfejs API można określić większość reguł formatowania, sprawdzania poprawności i mapowania, które można wykonać za pomocą atrybutów.

Niektóre atrybuty, takie jak `MinimumLength` nie można zastosować za pomocą interfejsu API fluent. `MinimumLength`nie zmienia schematu, stosuje tylko regułę sprawdzania poprawności minimalnej długości.

Niektórzy deweloperzy wolą używać płynnego interfejsu API wyłącznie, aby mogli zachować swoje klasy jednostek "czyste". Atrybuty i płynny interfejs API można mieszać. Istnieją pewne konfiguracje, które można wykonać tylko za pomocą płynnego interfejsu API (określając kompozytowe PK). Istnieją pewne konfiguracje, które można wykonać`MinimumLength`tylko za pomocą atrybutów ( ). Zalecana praktyka używania płynnego interfejsu API lub atrybutów:

* Wybierz jedno z tych dwóch podejść.
* W miarę możliwości należy konsekwentnie używać wybranego podejścia.

Niektóre atrybuty używane w tym samouczku są używane do:

* Tylko sprawdzanie poprawności `MinimumLength`(na przykład ).
* Tylko konfiguracja EF Core `HasKey`(na przykład ).
* Sprawdzanie poprawności i konfiguracja `[StringLength(50)]`EF Core (na przykład ).

Aby uzyskać więcej informacji na temat atrybutów a płynnego interfejsu API, zobacz [Metody konfiguracji](/ef/core/modeling/).

## <a name="entity-diagram"></a>Diagram encji

Na poniższej ilustracji przedstawiono diagram, który ef power tools utworzyć dla ukończonego modelu szkoły.

![Diagram encji](complex-data-model/_static/diagram.png)

Na powyższym diagramie przedstawiono:

* Kilka linii relacji jeden do wielu \*(od 1 do ).
* Wiersz relacji jeden do zera lub jeden (od 1 do 0..1) między `Instructor` jednostkami a `OfficeAssignment` jednostkami.
* Linia relacji zero-lub-jeden-do-wielu (od 0..1 `Instructor` do `Department` *) między elementami i jednostkami.

## <a name="seed-the-database"></a>Seed bazy danych

Zaktualizuj kod w *pliku Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Powyższy kod zawiera dane źródłowe dla nowych jednostek. Większość tego kodu tworzy nowe obiekty jednostki i ładuje przykładowe dane. Przykładowe dane są używane do testowania. Zobacz `Enrollments` `CourseAssignments` i przykłady, ile do wielu tabel sprzężenia mogą być rozstawione.

## <a name="add-a-migration"></a>Dodawanie migracji

Skompiluj projekt.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W pmc uruchom następujące polecenie.

```powershell
Add-Migration ComplexDataModel
```

Poprzednie polecenie wyświetla ostrzeżenie o możliwej utracie danych.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

Jeśli `database update` polecenie jest uruchamiane, jest wywoływany następujący błąd:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

W następnej sekcji zobaczysz, co zrobić z tym błędem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Jeśli dodasz migrację `database update` i uruchomisz polecenie, zostanie uruchomiony następujący błąd:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

W następnej sekcji zobaczysz, jak uniknąć tego błędu.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Stosowanie migracji lub upuszczania i ponownego tworzenia

Teraz, gdy masz istniejącą bazę danych, musisz zastanowić się, jak zastosować do niej zmiany. W tym samouczku przedstawiono dwie alternatywy:

* [Upuść i ponownie utwórz bazę danych](#drop). Wybierz tę sekcję, jeśli używasz SQLite.
* [Zastosuj migrację do istniejącej bazy danych](#applyexisting). Instrukcje w tej sekcji działają tylko dla programu SQL Server, **a nie dla SQLite**. 

Każdy wybór działa dla programu SQL Server. Chociaż metoda stosowania i migracji jest bardziej złożona i czasochłonna, jest to preferowane podejście do rzeczywistych środowisk produkcyjnych. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Upuść i ponownie utwórz bazę danych

[Pomiń tę sekcję,](#apply-the-migration) jeśli używasz programu SQL Server i chcesz wykonać podejście zastosuj-migracja w poniższej sekcji.

Aby wymusić ef core do utworzenia nowej bazy danych, upuść i zaktualizuj bazę danych:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:

  ```powershell
  Drop-Database
  ```

* Usuń folder *Migracje,* a następnie uruchom następujące polecenie:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz okno polecenia i przejdź do folderu projektu. Folder projektu zawiera plik *ContosoUniversity.csproj.*

* Uruchom następujące polecenie:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* Usuń folder *Migracje,* a następnie uruchom następujące polecenie:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Uruchom aplikację. Uruchomienie aplikacji uruchamia `DbInitializer.Initialize` metodę. Wypełnia `DbInitializer.Initialize` nową bazę danych.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Otwórz bazę danych w SSOX:

* Jeśli SSOX został otwarty wcześniej, kliknij przycisk **Odśwież.**
* Rozwiń węzeł **Tabele.** Zostaną wyświetlone utworzone tabele.

  ![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

* Sprawdź tabelę **CourseAssignment:**

  * Kliknij prawym przyciskiem myszy **tabelę CourseAssignment** i wybierz polecenie **Wyświetl dane**.
  * Sprawdź **CourseAssignment tabela** zawiera dane.

  ![Dane dotyczące kursusysignment w SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Użyj narzędzia SQLite, aby sprawdzić bazę danych:

* Nowe tabele i kolumny.
* Rozstawione dane w tabelach, na przykład **CourseAssignment** tabeli.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Stosowanie migracji

Ta sekcja jest opcjonalna. Te kroki działają tylko dla sql server localdb i tylko wtedy, gdy pominięto poprzedni [drop i ponownie utworzyć sekcję bazy danych.](#drop)

Gdy migracje są uruchamiane z istniejącymi danymi, mogą istnieć ograniczenia FK, które nie są spełnione z istniejącymi danymi. W przypadku danych produkcyjnych należy podjąć kroki migracji istniejących danych. W tej sekcji przedstawiono przykład ustalania naruszeń ograniczeń FK. Nie należy wprowadzać tych zmian kodu bez kopii zapasowej. Nie należy wprowadzać tych zmian kodu, jeśli zakończono poprzedni [drop i ponownie utworzyć sekcję bazy danych.](#drop)

Plik *{timestamp}_ComplexDataModel.cs* zawiera następujący kod:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Poprzedni kod dodaje niedopuszczalny `DepartmentID` do null `Course` FK do tabeli. Baza danych z poprzedniego samouczka `Course`zawiera wiersze w , dzięki czemu tabela nie może być aktualizowana przez migracje.

Aby migracja działała `ComplexDataModel` z istniejącymi danymi:

* Zmień kod, aby nadać`DepartmentID`nowej kolumnie ( ) wartość domyślną.
* Utwórz fałszywy dział o nazwie "Temp", aby działać jako domyślny dział.

#### <a name="fix-the-foreign-key-constraints"></a>Naprawianie ograniczeń klucza obcego

W `ComplexDataModel` klasie migracji zaktualizuj `Up` metodę:

* Otwórz plik *{timestamp}_ComplexDataModel.cs.*
* Skomentuj wiersz kodu, który `Course` dodaje kolumnę `DepartmentID` do tabeli.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Dodaj poniższy wyróżniony kod. Nowy kod idzie `.CreateTable( name: "Department"` po bloku:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

Z poprzednimi zmianami `Course` istniejące wiersze będą powiązane z działem "Temp" po uruchomieniu `ComplexDataModel.Up` metody.

Sposób obsługi sytuacji pokazano tutaj jest uproszczony dla tego samouczka. Aplikacja produkcyjna:

* Dołącz kod lub skrypty, aby dodać `Department` wiersze i powiązane `Course` wiersze do nowych `Department` wierszy.
* Nie używaj działu "Temp" ani `Course.DepartmentID`wartości domyślnej dla programu .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:

  ```powershell
  Update-Database
  ```

Ponieważ `DbInitializer.Initialize` metoda jest przeznaczona do pracy tylko z pustą bazą danych, użyj SSOX, aby usunąć wszystkie wiersze w tabelach Uczeń i kurs. (Kaskadowe usuwanie zajmie się tabelą Rejestracja).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Jeśli używasz usługi SQL Server LocalDB z programem Visual Studio Code, uruchom następujące polecenie:

  ```dotnetcli
  dotnet ef database update
  ```

---

Uruchom aplikację. Uruchomienie aplikacji uruchamia `DbInitializer.Initialize` metodę. Wypełnia `DbInitializer.Initialize` nową bazę danych.

## <a name="next-steps"></a>Następne kroki

Następne dwa samouczki pokazują, jak czytać i aktualizować powiązane dane.

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/migrations)
> [Następny samouczek](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Poprzednie samouczki pracował z podstawowym modelem danych, który składał się z trzech jednostek. W tym samouczku:

* Dodano więcej jednostek i relacji.
* Model danych jest dostosowywany przez określenie reguł formatowania, sprawdzania poprawności i mapowania bazy danych.

Klasy jednostek dla ukończonego modelu danych są wyświetlane na poniższej ilustracji:

![Diagram encji](complex-data-model/_static/diagram.png)

Jeśli napotkasz problemy, których nie możesz rozwiązać, pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Dostosowywanie modelu danych za pomocą atrybutów

W tej sekcji model danych jest dostosowywany przy użyciu atrybutów.

### <a name="the-datatype-attribute"></a>Atrybut DataType

Na stronach uczniów jest aktualnie wyświetlana godzina rejestracji. Zazwyczaj pola daty pokazują tylko datę, a nie godzinę.

Aktualizuj *modele/student.cs* za pomocą następującego podświetlanym kodem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[Atrybut DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) określa typ danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych. W takim przypadku powinna być wyświetlana tylko data, a nie data i godzina. [Wyliczenie datatype](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress, itp. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu. Przykład:

* Łącze `mailto:` jest tworzone `DataType.EmailAddress`automatycznie dla pliku .
* Selektor daty jest `DataType.Date` dostępny w większości przeglądarek.

Atrybut `DataType` emituje atrybuty `data-` HTML 5 (wymawiane rozdzielenie danych), które zużywają przeglądarki HTML 5. Atrybuty `DataType` nie zapewniają sprawdzania poprawności.

`DataType.Date`nie określa formatu daty, która jest wyświetlana. Domyślnie pole daty jest wyświetlane zgodnie z domyślnymi formatami opartymi na [programie CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serwera .

Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane do interfejsu użytkownika edycji. Niektóre pola nie `ApplyFormatInEditMode`powinny używać . Na przykład symbol waluty zazwyczaj nie powinien być wyświetlany w polu tekstowym edycji.

Atrybut `DisplayFormat` może być używany przez siebie. Ogólnie warto użyć atrybutu `DataType` z atrybutem. `DisplayFormat` Atrybut `DataType` przekazuje semantyki danych, w przeciwieństwie do sposobu renderowania go na ekranie. Atrybut `DataType` zapewnia następujące korzyści, które nie `DisplayFormat`są dostępne w:

* Przeglądarka może włączyć funkcje HTML5. Na przykład pokaż formant kalendarza, odpowiedni dla ustawień regionalnych symbol waluty, łącza e-mail, sprawdzanie poprawności danych wejściowych po stronie klienta itp.
* Domyślnie przeglądarka renderuje dane przy użyciu poprawnego formatu opartego na ustawieniach regionalnych.

Aby uzyskać więcej informacji, zobacz [ \<dokumentację> Pomocnika tagów](xref:mvc/views/working-with-forms#the-input-tag-helper).

Uruchom aplikację. Przejdź do strony Indeks uczniów. Czasy nie są już wyświetlane. Każdy widok, który `Student` używa modelu wyświetla datę bez godziny.

![Indeks uczniów przedstawiający daty bez godzin](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atrybut StringLength

Reguły sprawdzania poprawności danych i komunikaty o błędach sprawdzania poprawności można określić za pomocą atrybutów. [Atrybut StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) określa minimalną i maksymalną długość znaków dozwolonych w polu danych. Atrybut `StringLength` zapewnia również weryfikacji po stronie klienta i po stronie serwera. Minimalna wartość nie ma wpływu na schemat bazy danych.

Zaktualizuj `Student` model za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Poprzedni kod ogranicza nazwy do nie więcej niż 50 znaków. Atrybut `StringLength` nie uniemożliwia użytkownikowi wprowadzania odstępu dla nazwy. [Atrybut RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) służy do stosowania ograniczeń danych wejściowych. Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki alfabetycznie:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Uruchom aplikację:

* Przejdź do strony Uczniowie.
* Wybierz **pozycję Utwórz nowy**i wprowadź nazwę dłuższą niż 50 znaków.
* Wybierz **pozycję Utwórz**, sprawdzanie poprawności po stronie klienta wyświetla komunikat o błędzie.

![Strona indeksu uczniów z błędami długości ciągu](complex-data-model/_static/string-length-errors.png)

W **programie SQL Server Object Explorer** (SSOX) otwórz projektanta tabel uczniów, klikając dwukrotnie tabelę **Student.**

![Tabela studentów w SSOX przed migracją](complex-data-model/_static/ssox-before-migration.png)

Poprzedni obraz przedstawia schemat tabeli. `Student` Pola nazw mają `nvarchar(MAX)` typ, ponieważ migracje nie zostały uruchomione w bazy danych. Gdy migracje są uruchamiane w dalszej `nvarchar(50)`części tego samouczka, pola nazw stają się .

### <a name="the-column-attribute"></a>Atrybut Kolumna

Atrybuty mogą kontrolować sposób mapowania klas i właściwości do bazy danych. W tej sekcji `Column` atrybut jest używany do mapowania nazwy `FirstMidName` właściwości do "FirstName" w db.

Podczas tworzenia bazy danych nazwy właściwości w modelu są używane dla `Column` nazw kolumn (z wyjątkiem sytuacji, gdy używany jest atrybut).

Model `Student` używa `FirstMidName` dla pola imienia, ponieważ pole może również zawierać drugie imię.

Zaktualizuj plik *Student.cs* za pomocą następującego podświetlanym kodem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Wraz z poprzednią `Student.FirstMidName` zmianą w `FirstName` aplikacji jest `Student` mapowana do kolumny tabeli.

Dodanie atrybutu `Column` zmienia model, który `SchoolContext`wspiera program . Model kopii `SchoolContext` zapasowej nie jest już zgodny z bazą danych. Jeśli aplikacja jest uruchamiana przed zastosowaniem migracji, generowany jest następujący wyjątek:

```
SqlException: Invalid column name 'FirstName'.
```

Aby zaktualizować bazę danych:

* Skompiluj projekt.
* Otwórz okno polecenia w folderze projektu. Wprowadź następujące polecenia, aby utworzyć nową migrację i zaktualizować bazę danych:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

Polecenie `migrations add ColumnFirstName` generuje następujący komunikat ostrzegawczy:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Ostrzeżenie jest generowane, ponieważ pola nazw są teraz ograniczone do 50 znaków. Jeśli nazwa wdb miał więcej niż 50 znaków, 51 do ostatniego znaku zostaną utracone.

* Testowanie aplikacji.

Otwórz tabelę uczniów w SSOX:

![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

Przed zastosowaniem migracji kolumny nazw były typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Kolumny nazw są `nvarchar(50)`teraz . Nazwa kolumny została `FirstMidName` zmieniona z na `FirstName`.

> [!Note]
> W poniższej sekcji tworzenie aplikacji na niektórych etapach generuje błędy kompilatora. Instrukcje określają, kiedy utworzyć aplikację.

## <a name="student-entity-update"></a>Aktualizacja encji ucznia

![Jednostka studencka](complex-data-model/_static/student-entity.png)

Zaktualizuj *modele/Student.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Wymagany atrybut

Atrybut `Required` sprawia, że właściwości nazwy wymagane pola. Atrybut `Required` nie jest potrzebny dla typów nienastępalnych do`DateTime` `int`wartości, takich jak typy wartości ( , , `double`itp.). Typy, których nie można mieć wartości null, są automatycznie traktowane jako pola wymagane.

Atrybut `Required` można zastąpić parametrem minimalnej długości `StringLength` w atrybucie:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atrybut Display

Atrybut `Display` określa, że podpis dla pól tekstowych powinien być "Imię", "Nazwisko", "Imię" i "Data rejestracji". Domyślne podpisy nie miały miejsca dzielącego wyrazy, na przykład "Nazwisko".

### <a name="the-fullname-calculated-property"></a>Właściwość obliczana fullname

`FullName`jest obliczoną właściwością, która zwraca wartość utworzoną przez łączenie dwóch innych właściwości. `FullName`nie można ustawić, ma tylko akcesor get. Żadna kolumna nie `FullName` jest tworzona w bazie danych.

## <a name="create-the-instructor-entity"></a>Tworzenie jednostki instruktora

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

Utwórz *modele/instructor.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Wiele atrybutów może znajdować się w jednym wierszu. Atrybuty `HireDate` mogą być zapisywane w następujący sposób:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Właściwości nawigacji w ramach kursuAssignments i OfficeAssignment

Właściwości `CourseAssignments` `OfficeAssignment` i są właściwości nawigacji.

Instruktor może uczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Jeśli właściwość nawigacji zawiera wiele jednostek:

* Musi to być typ listy, na którym można dodawać, usuwać i aktualizować wpisy.

Typy właściwości nawigacji obejmują:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Jeśli `ICollection<T>` jest określony, EF `HashSet<T>` Core tworzy kolekcję domyślnie.

Jednostka `CourseAssignment` jest wyjaśniona w sekcji dotyczącej relacji wiele do wielu.

Reguły biznesowe Contoso University stwierdzają, że instruktor może mieć co najwyżej jedno biuro. Właściwość `OfficeAssignment` posiada `OfficeAssignment` jedną jednostkę. `OfficeAssignment`jest null, jeśli nie jest przypisany żaden urząd.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Tworzenie encji OfficeAssignment

![Encja officeassignment](complex-data-model/_static/officeassignment-entity.png)

Utwórz *modele/officeassignment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atrybut Klucz

Atrybut `[Key]` jest używany do identyfikowania właściwości jako klucz podstawowy (PK), gdy nazwa właściwości jest czymś innym niż classnameID lub ID.

Istnieje relacja jeden do zera lub jeden między `Instructor` jednostkami. `OfficeAssignment` Przypisanie biura istnieje tylko w odniesieniu do instruktora, do który jest przypisany. PK `OfficeAssignment` jest również jego klucz obcy (FK) do jednostki. `Instructor` EF Core nie może `InstructorID` automatycznie rozpoznać `OfficeAssignment` jako PK, ponieważ:

* `InstructorID`nie jest zgodna z konwencją nazewnictwa identyfikatorów ani classnameID.

W związku `Key` z tym atrybut `InstructorID` jest używany do identyfikowania jako PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Domyślnie EF Core traktuje klucz jako nieogenerowana przez bazę danych, ponieważ kolumna jest dla identyfikującej relacji.

### <a name="the-instructor-navigation-property"></a>Właściwość nawigacji instruktora

Właściwość `OfficeAssignment` nawigacji `Instructor` dla jednostki jest nullable, ponieważ:

* Typy odwołań (takie jak klasy są nullable).
* Instruktor może nie mieć przypisania biura.

Jednostka `OfficeAssignment` ma właściwość nawigacji `Instructor` nienastępnącą do wartości null, ponieważ:

* `InstructorID`nie można anulować.
* Przypisanie biura nie może istnieć bez instruktora.

Gdy `Instructor` jednostka ma `OfficeAssignment` jednostkę pokrewną, każda jednostka ma odwołanie do drugiej w swojej właściwości nawigacji.

Atrybut `[Required]` może być stosowany do `Instructor` właściwości nawigacji:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Poprzedni kod określa, że musi istnieć powiązany instruktor. Poprzedni kod jest zbędne, `InstructorID` ponieważ klucz obcy (który jest również PK) jest nie można nullable.

## <a name="modify-the-course-entity"></a>Modyfikowanie jednostki kursu

![Jednostka kursu](complex-data-model/_static/course-entity.png)

Zaktualizuj *modele/Course.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Jednostka `Course` ma właściwość `DepartmentID`klucza obcego (FK). `DepartmentID`wskazuje na `Department` podmiot powiązany. Jednostka `Course` ma `Department` właściwość nawigacji.

EF Core nie wymaga właściwości FK dla modelu danych, gdy model ma właściwość nawigacji dla jednostki pokrewne.

EF Core automatycznie tworzy pliki FK w bazie danych, gdziekolwiek są potrzebne. EF Core tworzy [właściwości cienia](/ef/core/modeling/shadow-properties) dla automatycznie tworzonych FKs. Posiadanie FK w modelu danych może sprawić, że aktualizacje będą prostsze i bardziej wydajne. Rozważmy na przykład model, w `DepartmentID` którym właściwość FK *nie* jest uwzględniona. Po pobraniu jednostki kursu do edycji:

* Jednostka `Department` ma wartość null, jeśli nie jest jawnie załadowany.
* Aby zaktualizować jednostkę `Department` kursu, jednostka musi najpierw zostać pobrana.

Gdy FK `DepartmentID` właściwość znajduje się w modelu danych, nie ma `Department` potrzeby pobierania jednostki przed aktualizacją.

### <a name="the-databasegenerated-attribute"></a>Atrybut DatabaseGenerated

Atrybut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` określa, że PK jest dostarczany przez aplikację, a nie generowane przez bazę danych.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Domyślnie EF Core zakłada, że wartości PK są generowane przez bazę danych. Wartości PK generowane przez bazę danych jest na ogół najlepszym rozwiązaniem. Dla `Course` jednostek użytkownik określa PK. Na przykład numer kursu, taki jak seria 1000 dla działu matematyki, seria 2000 dla działu angielskiego.

Atrybut `DatabaseGenerated` może również służyć do generowania wartości domyślnych. Na przykład baza danych może automatycznie wygenerować pole daty, aby zarejestrować datę utworzenia lub zaktualizowania wiersza. Aby uzyskać więcej informacji, zobacz [Wygenerowane właściwości](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego (FK) i `Course` właściwości nawigacji w jednostce odzwierciedlają następujące relacje:

Kurs jest przypisany do jednego działu, `DepartmentID` więc istnieje `Department` FK i właściwości nawigacji.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurs może mieć dowolną liczbę studentów zapisanych `Enrollments` w nim, więc właściwość nawigacji jest kolekcja:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs może być prowadzony przez wielu instruktorów, więc właściwość `CourseAssignments` nawigacji jest kolekcją:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`zostanie wyjaśnione [później](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Tworzenie encji Dział

![Jednostka działu](complex-data-model/_static/department-entity.png)

Utwórz *modele/department.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atrybut Kolumna

Wcześniej `Column` atrybut był używany do zmiany mapowania nazw kolumn. W kodzie `Department` jednostki `Column` atrybut jest używany do zmiany mapowania typów danych SQL. Kolumna `Budget` jest definiowana przy użyciu typu pieniądza programu SQL Server w db:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapowanie kolumn zazwyczaj nie jest wymagane. EF Core zazwyczaj wybiera odpowiedni typ danych programu SQL Server na podstawie typu CLR dla właściwości. Typ CLR `decimal` jest mapowany `decimal` na typ programu SQL Server. `Budget`jest dla waluty, a typ danych pieniądza jest bardziej odpowiedni dla waluty.

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

FK i właściwości nawigacji odzwierciedlają następujące relacje:

* Dział może, ale nie musi mieć administratora.
* Administrator jest zawsze instruktorem. W związku `InstructorID` z tym właściwość jest `Instructor` uwzględniona jako FK do jednostki.

Właściwość nawigacji `Administrator` jest nazwany, ale posiada jednostki: `Instructor`

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Znak zapytania (?) w poprzednim kodzie określa, że właściwość jest nullable.

Dział może mieć wiele kursów, więc istnieje właściwość nawigacji Kursy:

```csharp
public ICollection<Course> Courses { get; set; }
```

Uwaga: Zgodnie z konwencją EF Core umożliwia usuwanie kaskadowe dla nienastępalnych do null FK i dla relacji wiele do wielu. Usuwanie kaskadowe może spowodować reguły usuwania kaskady kołowych. Reguły usuwania kaskadowego cyklicznego powoduje wyjątek po dodaniu migracji.

Na przykład jeśli `Department.InstructorID` właściwość została zdefiniowana jako nie można nullable:

* EF Core konfiguruje regułę usuwania kaskadowego, aby usunąć dział po usunięciu instruktora.
* Usuwanie działu po usunięciu instruktora nie jest zamierzonym zachowaniem.
* Następujący [płynny interfejs API](#fluent-api-alternative-to-attributes) ustawi regułę ograniczania zamiast kaskady.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Poprzedni kod wyłącza kaskadowe usuwanie w relacji department-instructor.

## <a name="update-the-enrollment-entity"></a>Aktualizowanie encji Rejestracja

Rekord rejestracji jest dla jednego kursu podjętego przez jednego ucznia.

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

Zaktualizuj *modele/enrollment.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości FK i właściwości nawigacji odzwierciedlają następujące relacje:

Rekord rejestracji jest dla jednego kursu, `CourseID` więc istnieje właściwość FK i właściwość `Course` nawigacji:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Rekord rejestracji jest dla jednego ucznia, `StudentID` więc istnieje `Student` właściwość FK i właściwość nawigacji:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relacje wiele do wielu

Istnieje wiele do wielu relacji między `Student` i `Course` jednostek. Jednostka `Enrollment` działa jako tabela sprzężenia wiele do wielu *z ładunkiem* w bazie danych. "Z ładunkiem" oznacza, że `Enrollment` tabela zawiera dodatkowe dane oprócz FK dla tabel `Grade`połączonych (w tym przypadku PK i ).

Na poniższej ilustracji przedstawiono, jak wyglądają te relacje na diagramie jednostek. (Ten diagram został wygenerowany przy użyciu [ef power tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) dla EF 6.x. Tworzenie diagramu nie jest częścią samouczka.)

![Student-Kurs wielu do wielu relacji](complex-data-model/_static/student-course.png)

Każda linia relacji ma 1 na jednym końcu i gwiazdkę (*) na drugim, wskazując relację jeden do wielu.

Jeśli `Enrollment` tabela nie zawiera informacji o gatunku, musiałaby zawierać`CourseID` tylko `StudentID`dwa FK (i ). Tabela sprzężeń wiele do wielu bez ładunku jest czasami nazywana tabelą czystego sprzężenia (PJT).

I `Instructor` `Course` jednostek mają relację wiele do wielu przy użyciu tabeli sprzężenia czystego.

Uwaga: EF 6.x obsługuje tabele sprzężenia niejawnego dla relacji wiele-do-wielu, ale EF Core nie. Aby uzyskać więcej informacji, zobacz [Relacje wiele do wielu w EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Jednostka CourseAssignment

![Jednostka courseAssignment](complex-data-model/_static/courseassignment-entity.png)

Utwórz *modele/courseAssignment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Instruktor-kursy

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

The Instructor-to-Kursy wiele do wielu relacji:

* Wymaga tabeli sprzężenia, która musi być reprezentowana przez zestaw jednostek.
* Jest czystą tabelą sprzężenia (tabela bez ładunku).

Często nazywa się encję `EntityName1EntityName2`sprzężenia . Na przykład tabela sprzężenia Instructor-to-Courses przy użyciu tego wzorca jest `CourseInstructor`. Jednak zaleca się użycie nazwy opisującej relację.

Modele danych zaczynają się proste i rosną. Sprzężenia bez ładowności (PJTs) często ewoluują w celu uwzględnienia ładunku. Zaczynając od opisowej nazwy encji, nazwa nie musi się zmieniać po zmianie tabeli sprzężenia. W idealnym przypadku jednostka sprzężenia miałaby własną naturalną (ewentualnie pojedynczą nazwę) w domenie biznesowej. Na przykład książki i klienci mogą być połączone z jednostką sprzężenia o nazwie Oceny. Dla instruktora do kursów wiele do wielu `CourseAssignment` relacji, `CourseInstructor`jest preferowany nad .

### <a name="composite-key"></a>Klucz złożony

FKs nie są nullable. Dwa FKs `CourseAssignment` w`InstructorID` `CourseID`( i ) razem jednoznacznie zidentyfikować każdy wiersz `CourseAssignment` tabeli. `CourseAssignment`nie wymaga dedykowanego PK. I `InstructorID` `CourseID` właściwości działają jako złożona PK. Jedynym sposobem określenia złożonych PKs do EF Core jest *płynny interfejs API*. W następnej sekcji pokazano, jak skonfigurować złożoną PK.

Klucz kompozytowy zapewnia:

* Wiele wierszy są dozwolone dla jednego kursu.
* Wiele wierszy są dozwolone dla jednego instruktora.
* Wiele wierszy dla tego samego instruktora i kursu nie jest dozwolone.

Jednostka `Enrollment` sprzężenia definiuje własną PK, więc duplikaty tego rodzaju są możliwe. Aby zapobiec takim duplikatom:

* Dodaj unikatowy indeks w polach FK lub
* Skonfiguruj `Enrollment` za pomocą `CourseAssignment`podstawowego klucza kompozytowego podobnego do . Aby uzyskać więcej informacji, zobacz [Indeksy](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Aktualizowanie kontekstu bazy danych

Dodaj następujący wyróżniony kod do *pliku Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Powyższy kod dodaje nowe jednostki i `CourseAssignment` konfiguruje złożoną PK jednostki.

## <a name="fluent-api-alternative-to-attributes"></a>Płynna alternatywa dla API dla atrybutów

Metoda `OnModelCreating` w poprzednim kodzie używa *płynnego interfejsu API* do konfigurowania zachowania EF Core. Interfejs API jest nazywany "fluent", ponieważ jest często używany przez ciągnie serię wywołań metody razem w jednej instrukcji. [Poniższy kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) jest przykładem płynnego interfejsu API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

W tym samouczku płynny interfejs API jest używany tylko dla mapowania bazy danych, których nie można wykonać za pomocą atrybutów. Jednak płynny interfejs API można określić większość reguł formatowania, sprawdzania poprawności i mapowania, które można wykonać za pomocą atrybutów.

Niektóre atrybuty, takie jak `MinimumLength` nie można zastosować za pomocą interfejsu API fluent. `MinimumLength`nie zmienia schematu, stosuje tylko regułę sprawdzania poprawności minimalnej długości.

Niektórzy deweloperzy wolą używać płynnego interfejsu API wyłącznie, aby mogli zachować swoje klasy jednostek "czyste". Atrybuty i płynny interfejs API można mieszać. Istnieją pewne konfiguracje, które można wykonać tylko za pomocą płynnego interfejsu API (określając kompozytowe PK). Istnieją pewne konfiguracje, które można wykonać`MinimumLength`tylko za pomocą atrybutów ( ). Zalecana praktyka używania płynnego interfejsu API lub atrybutów:

* Wybierz jedno z tych dwóch podejść.
* W miarę możliwości należy konsekwentnie używać wybranego podejścia.

Niektóre atrybuty używane w tym samouczku są używane do:

* Tylko sprawdzanie poprawności `MinimumLength`(na przykład ).
* Tylko konfiguracja EF Core `HasKey`(na przykład ).
* Sprawdzanie poprawności i konfiguracja `[StringLength(50)]`EF Core (na przykład ).

Aby uzyskać więcej informacji na temat atrybutów a płynnego interfejsu API, zobacz [Metody konfiguracji](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram encji przedstawiający relacje

Na poniższej ilustracji przedstawiono diagram, który ef power tools utworzyć dla ukończonego modelu szkoły.

![Diagram encji](complex-data-model/_static/diagram.png)

Na powyższym diagramie przedstawiono:

* Kilka linii relacji jeden do wielu \*(od 1 do ).
* Wiersz relacji jeden do zera lub jeden (od 1 do 0..1) między `Instructor` jednostkami a `OfficeAssignment` jednostkami.
* Linia relacji zero-lub-jeden-do-wielu (od 0..1 `Instructor` do `Department` *) między elementami i jednostkami.

## <a name="seed-the-db-with-test-data"></a>Seed db z danymi testowymi

Zaktualizuj kod w *pliku Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Powyższy kod zawiera dane źródłowe dla nowych jednostek. Większość tego kodu tworzy nowe obiekty jednostki i ładuje przykładowe dane. Przykładowe dane są używane do testowania. Zobacz `Enrollments` `CourseAssignments` i przykłady, ile do wielu tabel sprzężenia mogą być rozstawione.

## <a name="add-a-migration"></a>Dodawanie migracji

Skompiluj projekt.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

Poprzednie polecenie wyświetla ostrzeżenie o możliwej utracie danych.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Jeśli `database update` polecenie jest uruchamiane, jest wywoływany następujący błąd:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Stosowanie migracji

Teraz, gdy masz istniejącą bazę danych, musisz zastanowić się, jak zastosować do niej przyszłe zmiany. W tym samouczku przedstawiono dwa podejścia:

* [Upuść i ponownie utwórz bazę danych](#drop)
* [Zastosuj migrację do istniejącej bazy danych](#applyexisting). Chociaż ta metoda jest bardziej złożona i czasochłonna, jest to preferowane podejście do rzeczywistych środowisk produkcyjnych. **Uwaga:** Jest to opcjonalna sekcja samouczka. Możesz wykonać kroki upuszczania i ponownego tworzenia i pominąć tę sekcję. Jeśli chcesz wykonać kroki opisane w tej sekcji, nie rób kroków upuszczania i ponownego tworzenia. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Upuść i ponownie utwórz bazę danych

Kod w updated `DbInitializer` dodaje dane źródłowe dla nowych jednostek. Aby wymusić na EF Core utworzenie nowej bazy danych, upuść i zaktualizuj bazę danych:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:

```powershell
Drop-Database
Update-Database
```

Uruchom `Get-Help about_EntityFrameworkCore` z PMC, aby uzyskać informacje o pomocy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otwórz okno polecenia i przejdź do folderu projektu. Folder projektu zawiera plik *Startup.cs.*

W oknie polecenia wprowadź następujące elementy:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Uruchom aplikację. Uruchomienie aplikacji uruchamia `DbInitializer.Initialize` metodę. Wypełnia `DbInitializer.Initialize` nowy DB.

Otwórz bazę danych w SSOX:

* Jeśli SSOX został otwarty wcześniej, kliknij przycisk **Odśwież.**
* Rozwiń węzeł **Tabele.** Zostaną wyświetlone utworzone tabele.

![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

Sprawdź tabelę **CourseAssignment:**

* Kliknij prawym przyciskiem myszy **tabelę CourseAssignment** i wybierz polecenie **Wyświetl dane**.
* Sprawdź **CourseAssignment tabela** zawiera dane.

![Dane dotyczące kursusysignment w SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Stosowanie migracji do istniejącej bazy danych

Ta sekcja jest opcjonalna. Te kroki działają tylko wtedy, gdy pominięto poprzedni [drop i ponownie utworzyć sekcję bazy danych.](#drop)

Gdy migracje są uruchamiane z istniejącymi danymi, mogą istnieć ograniczenia FK, które nie są spełnione z istniejącymi danymi. W przypadku danych produkcyjnych należy podjąć kroki migracji istniejących danych. W tej sekcji przedstawiono przykład ustalania naruszeń ograniczeń FK. Nie należy wprowadzać tych zmian kodu bez kopii zapasowej. Nie należy wprowadzać tych zmian kodu, jeśli ukończono poprzednią sekcję i zaktualizowano bazę danych.

Plik *{timestamp}_ComplexDataModel.cs* zawiera następujący kod:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Poprzedni kod dodaje niedopuszczalny `DepartmentID` do null `Course` FK do tabeli. Baza danych z poprzedniego samouczka `Course`zawiera wiersze w , dzięki czemu tabela nie może być aktualizowana przez migracje.

Aby migracja działała `ComplexDataModel` z istniejącymi danymi:

* Zmień kod, aby nadać`DepartmentID`nowej kolumnie ( ) wartość domyślną.
* Utwórz fałszywy dział o nazwie "Temp", aby działać jako domyślny dział.

#### <a name="fix-the-foreign-key-constraints"></a>Naprawianie ograniczeń klucza obcego

Zaktualizuj `ComplexDataModel` metodę klas: `Up`

* Otwórz plik *{timestamp}_ComplexDataModel.cs.*
* Skomentuj wiersz kodu, który `Course` dodaje kolumnę `DepartmentID` do tabeli.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Dodaj poniższy wyróżniony kod. Nowy kod idzie `.CreateTable( name: "Department"` po bloku:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Z poprzednimi zmianami `Course` istniejące wiersze będą powiązane z działem "Temp" po `ComplexDataModel` `Up` uruchomieniu metody.

Aplikacja produkcyjna:

* Dołącz kod lub skrypty, aby dodać `Department` wiersze i powiązane `Course` wiersze do nowych `Department` wierszy.
* Nie używaj działu "Temp" ani `Course.DepartmentID`wartości domyślnej dla programu .

Następny samouczek obejmuje powiązane dane.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja YouTube tego samouczka (część 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Wersja YouTube tego samouczka (część 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/migrations)
> [następny](xref:data/ef-rp/read-related-data)

::: moniker-end
