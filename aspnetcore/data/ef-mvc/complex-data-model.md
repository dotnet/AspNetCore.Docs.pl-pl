---
title: 'Samouczek: Tworzenie złożonego modelu danych - ASP.NET MVC z EF Core'
description: W tym samouczku dodaj więcej jednostek i relacji i dostosuj model danych, określając reguły formatowania, sprawdzania poprawności i mapowania.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657431"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Samouczek: Tworzenie złożonego modelu danych - ASP.NET MVC z EF Core

W poprzednich samouczkach pracowałeś z prostym modelem danych, który składał się z trzech jednostek. W tym samouczku dodasz więcej jednostek i relacji, a model danych zostanie dostosowany, określając reguły formatowania, sprawdzania poprawności i mapowania bazy danych.

Po zakończeniu klasy jednostek będą stanowić ukończony model danych, który jest wyświetlany na poniższej ilustracji:

![Diagram encji](complex-data-model/_static/diagram.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dostosowywanie modelu danych
> * Wprowadzanie zmian w encji Student
> * Tworzenie jednostki Instruktor
> * Tworzenie encji OfficeAssignment
> * Modify Course entity
> * Tworzenie encji Dział
> * Modyfikowanie jednostki rejestracji
> * Aktualizowanie kontekstu bazy danych
> * Baza danych materiału siewnego z danymi testowymi
> * Dodawanie migracji
> * Zmienianie ciągu połączenia
> * Aktualizowanie bazy danych

## <a name="prerequisites"></a>Wymagania wstępne

* [Korzystanie z migracji EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Dostosowywanie modelu danych

W tej sekcji zobaczysz, jak dostosować model danych przy użyciu atrybutów określających reguły formatowania, sprawdzania poprawności i mapowania bazy danych. Następnie w kilku poniższych sekcjach utworzysz kompletny model danych szkoły, dodając atrybuty do klas, które zostały już utworzone i tworząc nowe klasy dla pozostałych typów jednostek w modelu.

### <a name="the-datatype-attribute"></a>Atrybut DataType

W przypadku dat rejestracji uczniów na wszystkich stronach internetowych jest obecnie wyświetlana godzina wraz z datą, chociaż wszystko, na czym Ci zależy, to data. Za pomocą atrybutów adnotacji danych, można wprowadzić jedną zmianę kodu, który naprawi format wyświetlania w każdym widoku, który pokazuje dane. Aby zobaczyć przykład, jak to zrobić, należy dodać atrybut `EnrollmentDate` do `Student` właściwości w klasie.

W *models/student.cs*dodaj `using` instrukcję `System.ComponentModel.DataAnnotations` dla obszaru `DataType` `DisplayFormat` nazw i `EnrollmentDate` dodaj i przypisuje do właściwości, jak pokazano w poniższym przykładzie:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Atrybut `DataType` jest używany do określenia typu danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych. W takim przypadku chcemy tylko śledzić datę, a nie datę i godzinę. Wyliczenie `DataType` zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress i inne. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu. Na przykład `mailto:` można utworzyć łącze dla `DataType.EmailAddress`, a selektor `DataType.Date` daty może być podany w przeglądarkach obsługujących HTML5. Atrybut `DataType` emituje atrybuty `data-` HTML 5 (wymawiane rozdzielenie danych), które przeglądarki HTML 5 mogą zrozumieć. Atrybuty `DataType` nie zapewniają sprawdzania poprawności.

`DataType.Date`nie określa formatu daty, która jest wyświetlana. Domyślnie pole danych jest wyświetlane zgodnie z domyślnymi formatami opartymi na cultureinfo serwera.

Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane, gdy wartość jest wyświetlana w polu tekstowym do edycji. (W przypadku niektórych pól może nie być odpowiedni symbol waluty do edycji).

Możesz użyć `DisplayFormat` tego atrybutu samodzielnie, ale ogólnie warto użyć tego `DataType` atrybutu. Atrybut `DataType` przekazuje semantykę danych, w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, `DisplayFormat`które nie są dostarczane z:

* Przeglądarka może włączyć funkcje HTML5 (na przykład, aby wyświetlić formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, linki e-mail, niektóre sprawdzanie poprawności danych wejściowych po stronie klienta, itp.).

* Domyślnie przeglądarka będzie renderować dane przy użyciu odpowiedniego formatu na podstawie ustawień regionalnych.

Aby uzyskać więcej informacji, zobacz [ \<dokumentację pomocnika> tagów wprowadzania](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Uruchom aplikację, przejdź do strony Indeks uczniów i zwróć uwagę, że godziny nie są już wyświetlane dla dat rejestracji. To samo będzie dotyczyć każdego widoku, który używa modelu Student.

![Indeks uczniów przedstawiający daty bez godzin](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atrybut StringLength

Można również określić reguły sprawdzania poprawności danych i komunikaty o błędach sprawdzania poprawności przy użyciu atrybutów. Atrybut `StringLength` ustawia maksymalną długość w bazie danych i zapewnia sprawdzanie poprawności po stronie klienta i po stronie serwera dla ASP.NET Core MVC. Można również określić minimalną długość ciągu w tym atrybucie, ale minimalna wartość nie ma wpływu na schemat bazy danych.

Załóżmy, że chcesz mieć pewność, że użytkownicy nie wejdą więcej niż 50 znaków dla nazwy. Aby dodać to `StringLength` ograniczenie, `LastName` dodaj `FirstMidName` atrybuty do właściwości i, jak pokazano w poniższym przykładzie:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Atrybut `StringLength` nie uniemożliwia użytkownikowi wprowadzania odstępu dla nazwy. Za pomocą `RegularExpression` atrybutu można zastosować ograniczenia do danych wejściowych. Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki alfabetycznie:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Atrybut `MaxLength` zapewnia funkcje podobne `StringLength` do atrybutu, ale nie zapewnia sprawdzania poprawności po stronie klienta.

Model bazy danych został zmieniony w sposób, który wymaga zmiany w schemacie bazy danych. Migracji użyjesz do aktualizacji schematu bez utraty żadnych danych, które mogą być dodane do bazy danych przy użyciu interfejsu użytkownika aplikacji.

Zapisz swoje zmiany i zbuduj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

Polecenie `migrations add` ostrzega, że może wystąpić utrata danych, ponieważ zmiana sprawia, że maksymalna długość krótsza dla dwóch kolumn.  Migracje tworzy plik o nazwie * \<timestamp>_MaxLengthOnNames.cs*. Ten plik zawiera `Up` kod w metodzie, która zaktualizuje bazę danych, aby dopasować bieżący model danych. Polecenie `database update` uruchomiło ten kod.

Sygnatura czasowa przedpremierowana do nazwy pliku migracji jest używana przez entity framework do zamawiania migracji. Przed uruchomieniem polecenia update-database można utworzyć wiele migracji, a następnie wszystkie migracje są stosowane w kolejności, w jakiej zostały utworzone.

Uruchom aplikację, wybierz kartę **Uczniowie,** kliknij przycisk **Utwórz nowy**i spróbuj wprowadzić nazwę dłuższą niż 50 znaków. Aplikacja powinna uniemożliwić wykonanie tego. 

### <a name="the-column-attribute"></a>Atrybut Kolumna

Można również użyć atrybutów, aby kontrolować, jak klasy i właściwości są mapowane do bazy danych. Załóżmy, że `FirstMidName` użyto nazwy pola imienia, ponieważ pole może również zawierać drugie imię. Ale chcesz, aby kolumna `FirstName`bazy danych ma być nazwany , ponieważ użytkownicy, którzy będą pisać zapytania ad hoc względem bazy danych są przyzwyczajeni do tej nazwy. Aby wykonać to mapowanie, `Column` można użyć atrybutu.

Atrybut `Column` określa, że podczas tworzenia bazy danych kolumna `Student` tabeli, `FirstMidName` która mapuje właściwość, zostanie nazwana `FirstName`. Innymi słowy, gdy kod odnosi `Student.FirstMidName`się do , dane będą `FirstName` pochodzić `Student` z lub być aktualizowane w kolumnie tabeli. Jeśli nie określisz nazw kolumn, otrzymasz taką samą nazwę jak nazwa właściwości.

W pliku *Student.cs* dodaj instrukcję `using` i `System.ComponentModel.DataAnnotations.Schema` dodaj atrybut nazwy kolumny `FirstMidName` do właściwości, jak pokazano w poniższym wyróżnionym kodzie:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Dodanie atrybutu `Column` zmienia model kopii `SchoolContext`zapasowej , więc nie będzie zgodny z bazą danych.

Zapisz swoje zmiany i zbuduj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia, aby utworzyć kolejną migrację:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

W **Eksploratorze obiektów programu SQL Server**otwórz projektanta tabeli ucznia, klikając dwukrotnie tabelę **Student.**

![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

Przed zastosowaniem pierwszych dwóch migracji kolumny nazw były typu nvarchar(MAX). Są one teraz nvarchar(50) i nazwa kolumny została zmieniona z FirstMidName na FirstName.

> [!Note]
> Jeśli spróbujesz skompilować przed zakończeniem tworzenia wszystkich klas jednostek w poniższych sekcjach, mogą pojawić się błędy kompilatora.

## <a name="changes-to-student-entity"></a>Zmiany w jednostce Student

![Jednostka studencka](complex-data-model/_static/student-entity.png)

W *Models/Student.cs*zastąp kod dodany wcześniej następującym kodem. Zmiany są wyróżnione.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Wymagany atrybut

Atrybut `Required` sprawia, że właściwości nazwy wymagane pola. Atrybut `Required` nie jest potrzebny dla typów niedopuszczania do wartości, takich jak typy wartości (DateTime, int, double, float itp.). Typy, których nie można mieć wartości null, są automatycznie traktowane jako pola wymagane.

Atrybut `Required` musi być używany `MinimumLength` z `MinimumLength` do wymuszania.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atrybut Display

Atrybut `Display` określa, że podpis dla pól tekstowych powinien być "Imię", "Nazwisko", "Imię" i "Data rejestracji" zamiast nazwy właściwości w każdym wystąpieniu (który nie ma miejsca dzielącego wyrazy).

### <a name="the-fullname-calculated-property"></a>Właściwość obliczana fullname

`FullName`jest obliczoną właściwością, która zwraca wartość utworzoną przez łączenie dwóch innych właściwości. W związku z tym ma tylko `FullName` get akcesor i żadna kolumna nie zostaną wygenerowane w bazie danych.

## <a name="create-instructor-entity"></a>Tworzenie jednostki Instruktor

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

Utwórz *Models/Instructor.cs*, zastępując kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Należy zauważyć, że kilka właściwości są takie same w student i instruktora jednostek. W samouczku [implementacji dziedziczenia](inheritance.md) w dalszej części tej serii, będziesz refaktoryzuje ten kod, aby wyeliminować nadmiarowości.

Można umieścić wiele atrybutów w jednym wierszu, dzięki czemu można również napisać atrybuty w `HireDate` następujący sposób:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Właściwości nawigacji w ramach kursuAssignments i OfficeAssignment

Właściwości `CourseAssignments` `OfficeAssignment` i są właściwości nawigacji.

Instruktor może uczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Jeśli właściwość nawigacji może pomieścić wiele jednostek, jej typ musi być listą, na której można dodawać, usuwać i aktualizować wpisy.  Można określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`. Jeśli określisz `ICollection<T>`, `HashSet<T>` EF tworzy kolekcję domyślnie.

Powód, dla `CourseAssignment` którego są to jednostki jest wyjaśnione poniżej w sekcji o relacji wiele do wielu.

Reguły biznesowe contoso University twierdzą, że instruktor może `OfficeAssignment` mieć tylko w większości jednego biura, więc właściwość posiada jedną jednostkę OfficeAssignment (która może mieć wartość null, jeśli nie jest przypisane żadne biuro).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Tworzenie encji OfficeAssignment

![Encja officeassignment](complex-data-model/_static/officeassignment-entity.png)

Utwórz *modele/officeassignment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atrybut Klucz

Istnieje relacja jeden do zera lub jeden między instructor i OfficeAssignment jednostek. Przypisanie biura istnieje tylko w odniesieniu do instruktora, który jest przypisany do, a zatem jego klucz podstawowy jest również jego klucz obcy do instructor jednostki. Ale Entity Framework nie może automatycznie rozpoznać InstructorID jako klucz podstawowy tej jednostki, ponieważ jego nazwa nie jest zgodna z konwencją nazewnictwa identyfikatora lub classnameID. W związku `Key` z tym atrybut jest używany do identyfikowania go jako klucz:

```csharp
[Key]
public int InstructorID { get; set; }
```

Można również użyć `Key` atrybutu, jeśli jednostka ma swój własny klucz podstawowy, ale chcesz nazwać właściwość czymś innym niż classnameID lub ID.

Domyślnie EF traktuje klucz jako nie-bazy danych generowane, ponieważ kolumna jest dla relacji identyfikującej.

### <a name="the-instructor-navigation-property"></a>Właściwość nawigacji instruktora

Instructor Jednostka ma nullable `OfficeAssignment` właściwości nawigacji (ponieważ instruktor może nie mieć przypisania biura), a OfficeAssignment jednostki ma nienastępne `Instructor` null `InstructorID` właściwości nawigacji (ponieważ przypisanie biura nie może istnieć bez instruktora -- jest nie można anulować). Gdy Instructor jednostki ma powiązaną jednostkę OfficeAssignment, każda jednostka będzie miała odwołanie do drugiej w jego właściwości nawigacji.

Można umieścić `[Required]` atrybut na Instructor navigation właściwości, aby określić, że musi istnieć powiązany instruktor, ale nie trzeba tego robić, ponieważ klucz `InstructorID` obcy (który jest również klucz do tej tabeli) jest nie można anulować.

## <a name="modify-course-entity"></a>Modify Course entity

![Jednostka kursu](complex-data-model/_static/course-entity.png)

W *Models/Course.cs*zastąp kod dodany wcześniej następującym kodem. Zmiany są wyróżnione.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Jednostka kursu ma właściwość `DepartmentID` klucza obcego, która wskazuje `Department` na powiązaną jednostkę Działu i ma właściwość nawigacji.

Entity Framework nie wymaga, aby dodać właściwość klucza obcego do modelu danych, gdy masz właściwość nawigacji dla jednostki pokrewne.  EF automatycznie tworzy klucze obce w bazie danych, gdziekolwiek są one potrzebne i tworzy [właściwości cienia](/ef/core/modeling/shadow-properties) dla nich. Ale posiadanie klucza obcego w modelu danych może ułatwić i bardziej efektywne aktualizacje. Na przykład podczas pobierania jednostki kursu do edycji, Dział jednostki jest null, jeśli nie załadować, więc podczas aktualizowania jednostki kursu, trzeba najpierw pobrać jednostki Dział. Gdy właściwość `DepartmentID` klucza obcego jest uwzględniona w modelu danych, nie trzeba pobierać jednostki Dział przed aktualizacją.

### <a name="the-databasegenerated-attribute"></a>Atrybut DatabaseGenerated

Atrybut `DatabaseGenerated` z parametrem `None` we `CourseID` właściwości określa, że wartości klucza podstawowego są dostarczane przez użytkownika, a nie generowane przez bazę danych.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Domyślnie entity framework zakłada, że wartości klucza podstawowego są generowane przez bazę danych. To jest to, co chcesz w większości scenariuszy. Jednak dla jednostek Course użyjesz numeru kursu określonego przez użytkownika, takiego jak seria 1000 dla jednego działu, seria 2000 dla innego działu i tak dalej.

Atrybut `DatabaseGenerated` może również służyć do generowania wartości domyślnych, jak w przypadku kolumn bazy danych używanych do rejestrowania daty wiersz został utworzony lub zaktualizowany.  Aby uzyskać więcej informacji, zobacz [Wygenerowane właściwości](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego i właściwości nawigacji w Course jednostki odzwierciedlają następujące relacje:

Kurs jest przypisany do jednego działu, `DepartmentID` więc istnieje `Department` klucz obcy i właściwość nawigacji z powodów wymienionych powyżej.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurs może mieć dowolną liczbę studentów zapisanych `Enrollments` w nim, więc właściwość nawigacji jest kolekcja:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs może być prowadzony przez wielu instruktorów, więc właściwość `CourseAssignments` `CourseAssignment` nawigacji jest kolekcją (typ jest wyjaśniony [później):](#many-to-many-relationships)

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Tworzenie encji Dział

![Jednostka działu](complex-data-model/_static/department-entity.png)

Utwórz *modele/department.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atrybut Kolumna

Wcześniej użyto `Column` atrybutu do zmiany mapowania nazw kolumn. W kodzie jednostki Dział `Column` atrybut jest używany do zmiany mapowania typów danych SQL, tak aby kolumna była definiowana przy użyciu typu pieniądza programu SQL Server w bazie danych:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapowanie kolumn zazwyczaj nie jest wymagane, ponieważ Entity Framework wybiera odpowiedni typ danych programu SQL Server na podstawie typu CLR zdefiniowanego dla właściwości. Typ CLR `decimal` jest mapowany `decimal` na typ programu SQL Server. Ale w tym przypadku wiesz, że kolumna będzie trzymania kwot walutowych, a typ danych pieniądza jest bardziej odpowiedni do tego.

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Klucz obcy i właściwości nawigacji odzwierciedlają następujące relacje:

Dział może lub nie może mieć administratora, a administrator jest zawsze instruktorem. W związku `InstructorID` z tym właściwość jest uwzględniona jako klucz obcy do `int` Instructor jednostki i znak zapytania jest dodawany po oznaczeniu typu, aby oznaczyć właściwość jako nullable. Właściwość nawigacji `Administrator` jest nazwany, ale posiada Instructor jednostki:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Dział może mieć wiele kursów, więc istnieje właściwość nawigacji Kursy:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Zgodnie z konwencją Entity Framework umożliwia kaskadowe usuwanie dla kluczy obcych nienastępnych do wartości null i dla relacji wiele-do-wielu. Może to spowodować reguły usuwania kaskadowego cyklicznego, co spowoduje wyjątek podczas próby dodania migracji. Na przykład jeśli nie zdefiniowano Department.InstructorID właściwość jako nullable, EF skonfiguruje regułę usuwania kaskadowego, aby usunąć dział po usunięciu instruktora, który nie jest to, co chcesz mieć miejsce. Jeśli reguły biznesowe `InstructorID` wymagały, aby właściwość była nienależna do wartości null, należy użyć następującej instrukcji interfejsu API fluent, aby wyłączyć usuwanie kaskadowe w relacji:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Modyfikowanie jednostki rejestracji

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

W *Models/Enrollment.cs*zastąp kod dodany wcześniej następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Właściwości klucza obcego i nawigacji

Właściwości klucza obcego i właściwości nawigacji odzwierciedlają następujące relacje:

Rekord rejestracji jest dla jednego kursu, więc `CourseID` istnieje właściwość `Course` klucza obcego i właściwości nawigacji:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Rekord rejestracji jest dla jednego ucznia, `StudentID` więc istnieje właściwość klucza obcego `Student` i właściwość nawigacji:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relacje wiele do wielu

Istnieje wiele do wielu relacji między jednostkami Student i Course, a jednostka Enrollment działa jako tabela sprzężenia wiele do wielu *z ładunkiem* w bazie danych. "Z ładunkiem" oznacza, że enrollment tabela zawiera dodatkowe dane oprócz kluczy obcych dla tabel połączonych (w tym przypadku klucz podstawowy i Grade właściwości).

Na poniższej ilustracji przedstawiono, jak wyglądają te relacje na diagramie jednostek. (Ten diagram został wygenerowany przy użyciu entity framework power tools dla EF 6.x; tworzenie diagramu nie jest częścią samouczka, jest po prostu używany w tym miejscu jako ilustracja.)

![Student-Kurs wielu do wielu relacji](complex-data-model/_static/student-course.png)

Każda linia relacji ma 1 na jednym końcu i gwiazdkę (*) na drugim, wskazując relację jeden do wielu.

Jeśli enrollment tabeli nie zawiera informacji o klasie, to tylko musi zawierać dwa klucze obce CourseID i StudentID. W takim przypadku będzie to tabela sprzężenia wiele do wielu bez ładunku (lub tabela czystego sprzężenia) w bazie danych. Instruktor i kurs jednostek mają tego rodzaju relacji wiele do wielu, a następnym krokiem jest utworzenie klasy jednostki do działania jako tabela sprzężenia bez ładunku.

(EF 6.x obsługuje niejawne tabele sprzężenia dla relacji wiele do wielu, ale EF Core nie. Aby uzyskać więcej informacji, zobacz [dyskusję w repozytorium EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>Jednostka CourseAssignment

![Jednostka courseAssignment](complex-data-model/_static/courseassignment-entity.png)

Utwórz *modele/courseAssignment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Dołączanie nazw encji

Tabela sprzężenia jest wymagana w bazie danych dla relacji Instructor-to-Courses wiele-do-wielu i musi być reprezentowana przez zestaw jednostek. Często nazywa się jednostkę `EntityName1EntityName2`sprzężenia, która `CourseInstructor`w tym przypadku byłaby . Zaleca się jednak wybranie nazwy opisującej relację. Modele danych zaczynają się proste i rosną, a bez ładowności łączy często coraz ładunków później. Jeśli zaczniesz od opisowej nazwy encji, nie trzeba będzie później zmieniać nazwy. W idealnym przypadku jednostka sprzężenia miałaby własną naturalną (ewentualnie pojedynczą nazwę) w domenie biznesowej. Na przykład książki i klienci mogą być połączone za pośrednictwem ocen. Dla tej `CourseAssignment` relacji, jest `CourseInstructor`lepszym wyborem niż .

### <a name="composite-key"></a>Klucz złożony

Ponieważ klucze obce nie są możliwe do anulowania i razem jednoznacznie identyfikują każdy wiersz tabeli, nie ma potrzeby oddzielnego klucza podstawowego. *InstructorID* i *CourseID* właściwości powinny działać jako złożony klucz podstawowy. Jedynym sposobem identyfikowania złożonych kluczy podstawowych do EF jest przy użyciu *płynnego interfejsu API* (nie można tego zrobić przy użyciu atrybutów). Zobaczysz, jak skonfigurować złożony klucz podstawowy w następnej sekcji.

Klucz złożony zapewnia, że chociaż można mieć wiele wierszy dla jednego kursu i wiele wierszy dla jednego instruktora, nie można mieć wiele wierszy dla tego samego instruktora i kursu. Jednostka `Enrollment` sprzężenia definiuje własny klucz podstawowy, więc duplikaty tego rodzaju są możliwe. Aby zapobiec takim duplikatom, można dodać unikatowy indeks `Enrollment` w polach klucza `CourseAssignment`obcego lub skonfigurować przy pomocy podstawowego klucza złożonego podobnego do . Aby uzyskać więcej informacji, zobacz [Indeksy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizowanie kontekstu bazy danych

Dodaj następujący wyróżniony kod do pliku *Data/SchoolContext.cs:*

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Ten kod dodaje nowe jednostki i konfiguruje złożony klucz podstawowy jednostki CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>O płynnej alternatywie API

Kod w `OnModelCreating` metodzie `DbContext` klasy używa *płynnego interfejsu API* do konfigurowania zachowania EF. Interfejs API jest nazywany "fluent", ponieważ jest często używany przez ciągowanie serii wywołań metody razem w jednej instrukcji, jak w tym przykładzie z [dokumentacji EF Core:](/ef/core/modeling/#use-fluent-api-to-configure-a-model)

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

W tym samouczku używasz płynnego interfejsu API tylko do mapowania bazy danych, którego nie można zrobić z atrybutami. Jednak można również użyć płynnego interfejsu API, aby określić większość reguł formatowania, sprawdzania poprawności i mapowania, które można wykonać przy użyciu atrybutów. Niektóre atrybuty, takie jak `MinimumLength` nie można zastosować za pomocą interfejsu API fluent. Jak wspomniano wcześniej, `MinimumLength` nie zmienia schematu, stosuje tylko regułę sprawdzania poprawności po stronie klienta i serwera.

Niektórzy deweloperzy wolą używać płynnego interfejsu API wyłącznie, aby mogli zachować swoje klasy jednostek "czyste". Można mieszać atrybuty i płynne interfejsu API, jeśli chcesz, a istnieje kilka dostosowań, które można wykonać tylko przy użyciu płynnego interfejsu API, ale ogólnie zalecana praktyka jest wybrać jedną z tych dwóch metod i używać, że konsekwentnie jak najwięcej. Jeśli używasz obu, należy pamiętać, że wszędzie tam, gdzie występuje konflikt, Fluent INTERFEJSU API zastępuje atrybuty.

Aby uzyskać więcej informacji na temat atrybutów a płynnego interfejsu API, zobacz [Metody konfiguracji](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram encji przedstawiający relacje

Na poniższej ilustracji przedstawiono diagram, który power tools entity framework utworzyć dla ukończonego modelu szkoły.

![Diagram encji](complex-data-model/_static/diagram.png)

Oprócz wierszy relacji jeden do wielu \*(od 1 do ), można zobaczyć w tym miejscu wiersz relacji jeden do zera lub jeden (od 1 do 0..1) między jednostkami Instructor i OfficeAssignment i wiersz relacji zero-lub-jeden-do-wielu (od 0..1 do *) między jednostkami Instruktor i Dział.

## <a name="seed-database-with-test-data"></a>Baza danych materiału siewnego z danymi testowymi

Zastąp kod w pliku *Data/DbInitializer.cs* następującym kodem, aby zapewnić dane źródłowe dla nowo utworzonych jednostek.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Jak widać w pierwszym samouczku, większość tego kodu po prostu tworzy nowe obiekty jednostki i ładuje przykładowe dane do właściwości wymaganych do testowania. Zwróć uwagę, jak relacje wiele do wielu są obsługiwane: kod tworzy relacje `Enrollments` przez `CourseAssignment` tworzenie jednostek w zestawach jednostek i sprzężenia.

## <a name="add-a-migration"></a>Dodawanie migracji

Zapisz swoje zmiany i zbuduj projekt. Następnie otwórz okno polecenia w folderze `migrations add` projektu i wprowadź polecenie (nie rób jeszcze polecenia update-database):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Otrzymasz ostrzeżenie o możliwej utracie danych.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Jeśli w tym `database update` momencie próbowano uruchomić polecenie (nie rób tego jeszcze), zostanie wyświetlony następujący błąd:

> Instrukcja ALTER TABLE była sprzeczna z ograniczeniem FOREIGN KEY "FK_dbo. Course_dbo. Department_DepartmentID". Konflikt wystąpił w bazie danych "ContosoUniversity", tabeli "dbo. departamentu", kolumna "DepartmentID".

Czasami podczas wykonywania migracji z istniejącymi danymi, należy wstawić dane skrótowe do bazy danych, aby spełnić ograniczenia klucza obcego. Wygenerowany kod `Up` w metodzie dodaje klucz obcy Departamentu nienależy do wartości null do tabeli Course. Jeśli istnieją już wiersze w tabeli Course `AddColumn` po uruchomieniu kodu, operacja kończy się niepowodzeniem, ponieważ SQL Server nie wie, jaką wartość umieścić w kolumnie, która nie może mieć wartości null. W tym samouczku uruchomisz migrację w nowej bazie danych, ale w aplikacji produkcyjnej musisz wykonać migrację do obsługi istniejących danych, więc poniższe wskazówki pokazują przykład, jak to zrobić.

Aby ta migracja działała z istniejącymi danymi, należy zmienić kod, aby nadać nowej kolumnie wartość domyślną i utworzyć dział skrótowy o nazwie "Temp", aby działał jako dział domyślny. W rezultacie istniejące wiersze kursu będą powiązane z działem `Up` "Temp" po uruchomieniu metody.

* Otwórz plik *{timestamp}_ComplexDataModel.cs.*

* Skomentuj wiersz kodu, który dodaje kolumnę DepartmentID do tabeli Course.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Dodaj następujący wyróżniony kod po kodzie, który tworzy tabelę Dział:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

W aplikacji produkcyjnej należy napisać kod lub skrypty, aby dodać wiersze działu i powiązać wiersze kursu do nowych wierszy działu. Wtedy nie będzie już potrzebny dział "Temp" ani wartość domyślna w kolumnie Course.DepartmentID.

Zapisz swoje zmiany i zbuduj projekt.

## <a name="change-the-connection-string"></a>Zmienianie ciągu połączenia

Masz teraz nowy kod `DbInitializer` w klasie, który dodaje dane źródłowe dla nowych jednostek do pustej bazy danych. Aby ef utworzyć nową pustą bazę danych, należy zmienić nazwę bazy danych w ciągu połączenia w *appsettings.json* contosoUniversity3 lub innej nazwy, która nie została użyta na komputerze, którego używasz.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Zapisz zmiany do *appsettings.json*.

> [!NOTE]
> Jako alternatywę dla zmiany nazwy bazy danych można usunąć bazę danych. Użyj **programu SQL Server Object Explorer** `database drop` (SSOX) lub polecenia CLI:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Aktualizowanie bazy danych

Po zmianie nazwy bazy danych lub usunięciu `database update` bazy danych uruchom polecenie w oknie polecenia, aby wykonać migracje.

```dotnetcli
dotnet ef database update
```

Uruchom aplikację, aby `DbInitializer.Initialize` spowodować, że metoda do uruchomienia i wypełnić nową bazę danych.

Otwórz bazę danych w SSOX, tak jak wcześniej, i rozwiń węzeł **Tabele,** aby zobaczyć, że wszystkie tabele zostały utworzone. (Jeśli nadal masz otwarty SSOX od wcześniejszego czasu, kliknij przycisk **Odśwież).**

![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

Uruchom aplikację, aby wyzwolić kod inicjatora, który nasiona bazy danych.

Kliknij prawym przyciskiem myszy **tabelę CourseAssignment** i wybierz polecenie **Wyświetl dane,** aby sprawdzić, czy zawiera ona dane.

![Dane dotyczące kursusysignment w SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dostosowano model danych
> * Wprowadzone zmiany w encji Student
> * Utworzono jednostkę Instructor
> * Utworzono encję OfficeAssignment
> * Zmodyfikowana jednostka kursu
> * Utworzono encję Dział
> * Zmodyfikowana jednostka rejestracji
> * Zaktualizowano kontekst bazy danych
> * Rozstawiona baza danych z danymi testowymi
> * Dodano migrację
> * Zmieniono parametry połączenia
> * Zaktualizowano bazę danych

Przejdź do następnego samouczka, aby dowiedzieć się więcej o tym, jak uzyskać dostęp do powiązanych danych.

> [!div class="nextstepaction"]
> [Dalej: Dostęp do powiązanych danych](read-related-data.md)
