---
title: 'Samouczek: Wprowadzenie do EF Core w ASP.NET aplikacji sieci Web MVC'
description: Jest to pierwszy z serii samouczków, które wyjaśniają, jak utworzyć contoso University przykładowej aplikacji od podstaw.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511304"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Samouczek: Wprowadzenie do EF Core w ASP.NET aplikacji sieci Web MVC

Ten samouczek **nie** został zaktualizowany do ASP.NET Core 3.0. [Zaktualizowano wersję Razor Pages.](xref:data/ef-rp/intro) Większość zmian kodu dla ASP.NET Core 3.0 i nowszej wersji tego samouczka:

* Znajdują się w *plikach Startup.cs* i *Program.cs.*
* Można znaleźć w [wersji Razor Pages](xref:data/ef-rp/intro). 

Aby uzyskać informacje o tym, kiedy może to być zaktualizowane, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/13920).

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Przykładowa aplikacja sieci web contoso University pokazuje, jak tworzyć ASP.NET core 2.2 MVC aplikacji sieci web przy użyciu entity framework (EF) Core 2.2 i Visual Studio 2017 lub 2019.

Przykładowa aplikacja jest witryną sieci web fikcyjnego Uniwersytetu Contoso. Obejmuje funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora. Jest to pierwszy z serii samouczków, które wyjaśniają, jak utworzyć contoso University przykładowej aplikacji od podstaw.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji sieci Web ASP.NET Core MVC
> * Konfigurowanie stylu witryny
> * Dowiedz się więcej o pakietach EF Core NuGet
> * Tworzenie modelu danych
> * Tworzenie kontekstu bazy danych
> * Rejestrowanie kontekstu iniekcji zależności
> * Inicjowanie bazy danych za pomocą danych testowych
> * Tworzenie kontrolera i widoków
> * Wyświetlanie bazy danych

## <a name="prerequisites"></a>Wymagania wstępne

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:
  * **obciążenie ASP.NET i tworzeniem stron internetowych**
  * **Wieloplatformowe obciążenie programistyczne .NET Core**

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz problem, którego nie możesz rozwiązać, zazwyczaj możesz znaleźć rozwiązanie, porównując kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final) Aby uzyskać listę typowych błędów i jak je rozwiązać, zobacz [sekcję Rozwiązywanie problemów z ostatnim samouczkiem z serii](advanced.md#common-errors). Jeśli nie znajdziesz tam tego, czego potrzebujesz, możesz zadać pytanie, aby StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Jest to seria 10 samouczków, z których każdy opiera się na tym, co zostało zrobione we wcześniejszych samouczkach. Należy rozważyć zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka. Następnie, jeśli napotkasz problemy, możesz zacząć od początku poprzedniego samouczka zamiast wracać do początku całej serii.

## <a name="contoso-university-web-app"></a>Aplikacja internetowa Uniwersytetu Contoso

Aplikacja, którą będziesz budować w tych samouczkach jest prosta strona internetowa uczelni.

Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach. Oto kilka ekranów, które utworzysz.

![Strona Indeks uczniów](intro/_static/students-index.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

* Otwórz program Visual Studio.

* Z menu **Plik** wybierz polecenie **Nowy > project**.

* W lewym okienku wybierz pozycję **Zainstalowana > visual C# > Web**.

* Wybierz szablon projektu **ASP.NET Core Web Application.**

* Wprowadź **ContosoUniversity** jako nazwę i kliknij przycisk **OK**.

  ![Okno dialogowe Nowy projekt](intro/_static/new-project2.png)

* Poczekaj na wyświetlenie okna dialogowego **Nowa ASP.NET Core Aplikacji Sieci Web.**

* Wybierz **opcję .NET Core**, ASP.NET Core **2.2** i szablon aplikacji sieci Web **(Model-View-Controller).**

* Upewnij **się,** że uwierzytelnianie jest ustawione na **Brak uwierzytelniania**.

* Wybierz **przycisk OK**

  ![Nowe okno dialogowe projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Kilka prostych zmian skonfiguruje menu witryny, układ i stronę główną.

*Otwórz widoki/udostępnione/_Layout.cshtml* i wprowadzać następujące zmiany:

* Zmień każde wystąpienie "ContosoUniversity" na "Contoso University". Istnieją trzy zdarzenia.

* Dodaj pozycje menu **Informacje**, **Studenci**, **Kursy,** **Instruktorzy**i **Działy**i usuń wpis menu **Prywatność.**

Zmiany są wyróżnione.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

W *Views/Home/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET i MVC tekstem o tej aplikacji:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Naciśnij klawisze CTRL+F5, aby uruchomić projekt, lub wybierz polecenie **Debugowanie > rozpocznij bez debugowania** z menu. Zobaczysz stronę główną z kartami dla stron, które utworzysz w tych samouczkach.

![Strona główna Uniwersytetu Contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informacje o pakietach EF Core NuGet

Aby dodać obsługę EF Core do projektu, zainstaluj dostawcę bazy danych, który chcesz kierować reklamy. Ten samouczek używa programu SQL Server, a pakietem dostawcy jest [Microsoft.EntityFrameCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Ten pakiet znajduje się w [metapakietie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)więc nie trzeba odwoływać się do pakietu.

Pakiet EF SQL Server i`Microsoft.EntityFrameworkCore` jego `Microsoft.EntityFrameworkCore.Relational`zależności ( i ) zapewniają obsługę środowiska uruchomieniowego dla EF. Pakiet narzędzi zostanie dodany później w samouczku [Migracje.](migrations.md)

Aby uzyskać informacje o innych dostawcach baz danych dostępnych dla entity framework core, zobacz [Dostawców bazy danych](/ef/core/providers/).

## <a name="create-the-data-model"></a>Tworzenie modelu danych

Następnie utworzysz klasy jednostek dla aplikacji Contoso University. Zaczniesz od następujących trzech jednostek.

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

Istnieje relacja jeden do wielu między `Student` `Enrollment` i jednostek i istnieje relacja jeden do `Course` wielu `Enrollment` między i jednostek. Innymi słowy, student może być zapisany na dowolną liczbę kursów, a kurs może mieć dowolną liczbę studentów zapisanych w nim.

W poniższych sekcjach utworzysz klasę dla każdej z tych jednostek.

### <a name="the-student-entity"></a>Jednostka Student

![Diagram encji ucznia](intro/_static/student-entity.png)

W folderze *Modele* utwórz plik klasy o nazwie *Student.cs* i zastąp kod szablonu następującym kodem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Właściwość `ID` stanie się kolumną klucza podstawowego tabeli bazy danych, która odpowiada tej klasie. Domyślnie platforma Entity Framework interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.

Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji przechowują inne jednostki, które są powiązane z tą encją. W takim przypadku `Enrollments` właściwość `Student entity` a będzie `Enrollment` zawierać wszystkie jednostki, `Student` które są związane z tej jednostki. Innymi słowy, jeśli dany wiersz studenta w bazie danych ma dwa powiązane wiersze rejestracji (wiersze, które `Student` zawierają wartość `Enrollments` klucza podstawowego `Enrollment` tego ucznia w kolumnie klucza obcego StudentID), właściwość nawigacji tej jednostki będzie zawierać te dwie jednostki.

Jeśli właściwość nawigacji może zawierać wiele jednostek (jak w relacjach wiele do wielu lub jeden do wielu), jej typem musi być `ICollection<T>`lista, na której można dodawać, usuwać i aktualizować wpisy, takie jak . Można określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`. Jeśli określisz `ICollection<T>`, `HashSet<T>` EF tworzy kolekcję domyślnie.

### <a name="the-enrollment-entity"></a>Jednostka Rejestracji

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

W folderze *Modele* utwórz *Enrollment.cs* i zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Właściwość `EnrollmentID` będzie kluczem podstawowym; ta jednostka używa `classnameID` wzorca `ID` zamiast sam, jak `Student` widać w jednostce. Zwykle można wybrać jeden wzorzec i używać go w całym modelu danych. W tym miejscu odmiana ilustruje, że można użyć albo wzorzec. W [późniejszym samouczku](inheritance.md)zobaczysz, jak przy użyciu identyfikatora bez nazwy klasy ułatwia implementowanie dziedziczenia w modelu danych.

Obiekt `Grade` jest `enum`własnością . Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest nullable. Stopień, który ma wartość null, różni się od klasy zerowej — wartość null oznacza, że ocena nie jest znana lub nie została jeszcze przypisana.

Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest . Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, więc `Student` właściwość może `Student.Enrollments` zawierać tylko jedną jednostkę `Enrollment` (w przeciwieństwie do właściwości nawigacji, która została wcześniej osobowa, która może zawierać wiele jednostek).

Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest . Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.

Entity Framework interpretuje właściwość jako właściwość klucza obcego, `<navigation property name><primary key property name>` jeśli jest nazwana (na przykład dla właściwości `StudentID` `Student` nawigacji, ponieważ klucz podstawowy `Student` jednostki jest `ID`). Właściwości klucza obcego można `<primary key property name>` również nazwać po prostu (na przykład, `CourseID` ponieważ klucz podstawowy `Course` jednostki jest `CourseID`).

### <a name="the-course-entity"></a>Jednostka Kurs

![Diagram encji kursu](intro/_static/course-entity.png)

W folderze *Modele* utwórz *Course.cs* i zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Właściwość `Enrollments` jest właściwością nawigacji. Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.

Więcej informacji na temat `DatabaseGenerated` atrybutu powiemy w [późniejszym samouczku](complex-data-model.md) z tej serii. Zasadniczo ten atrybut umożliwia wprowadzenie klucza podstawowego kursu, a nie generowanie go przez bazę danych.

## <a name="create-the-database-context"></a>Tworzenie kontekstu bazy danych

Klasą główną, która koordynuje funkcjonalność entity framework dla danego modelu danych jest klasą kontekstu bazy danych. Tę klasę można utworzyć, `Microsoft.EntityFrameworkCore.DbContext` wywodząc się z klasy. W kodzie można określić, które jednostki są uwzględnione w modelu danych. Można również dostosować pewne zachowanie struktury encji. W tym projekcie klasa `SchoolContext`nosi nazwę .

W folderze projektu utwórz folder o nazwie *Dane*.

W folderze *Dane* utwórz nowy plik klasy o nazwie *SchoolContext.cs*i zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ten kod `DbSet` tworzy właściwość dla każdego zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

Można było pominąć `DbSet<Enrollment>` i `DbSet<Course>` oświadczenia i będzie działać tak samo. Struktura jednostek obejmowałaby je `Student` w sposób `Enrollment` dorozumiany, ponieważ jednostka odwołuje się do jednostki, a `Enrollment` jednostka odwołuje się do jednostki. `Course`

Po utworzeniu bazy danych EF tworzy tabele, `DbSet` które mają nazwy takie same jak nazwy właściwości. Nazwy właściwości dla kolekcji są zazwyczaj w liczbie mnogiej (Studenci, a nie Student), ale deweloperzy nie zgadzają się co do tego, czy nazwy tabel powinny być pluralizowane, czy nie. W przypadku tych samouczków należy zastąpić domyślne zachowanie, określając nazwy poszczególnych tabel w DbContext. Aby to zrobić, należy dodać następujący wyróżniony kod po ostatniej właściwości DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zarejestrujcie schoolcontext

ASP.NET Core domyślnie implementuje [iniekcję zależności.](../../fundamentals/dependency-injection.md) Usługi (takie jak kontekst bazy danych EF) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak kontrolery MVC) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Zobaczysz kod konstruktora kontrolera, który pobiera wystąpienie kontekstu w dalszej części tego samouczka.

Aby `SchoolContext` zarejestrować się jako usługa, otwórz *Startup.cs*i dodaj `ConfigureServices` wyróżnione wiersze do metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Nazwa ciągu połączenia jest przekazywana do kontekstu przez `DbContextOptionsBuilder` wywołanie metody na obiekcie. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

Dodaj `using` instrukcje dla `ContosoUniversity.Data` i `Microsoft.EntityFrameworkCore` przestrzeni nazw, a następnie skompiluj projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otwórz plik *appsettings.json* i dodaj parametry połączenia, jak pokazano w poniższym przykładzie.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Parametry połączenia określają bazę danych usługi SQL Server LocalDB. LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji. Domyślnie LocalDB tworzy pliki bazy danych `C:/Users/<user>` *.mdf* w katalogu.

## <a name="initialize-db-with-test-data"></a>Inicjowanie bazy danych za pomocą danych testowych

Entity Framework utworzy pustą bazę danych dla Ciebie. W tej sekcji napisz metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnić go danymi testowymi.

W tym miejscu `EnsureCreated` użyjesz tej metody do automatycznego utworzenia bazy danych. W [późniejszym samouczku](migrations.md) zobaczysz, jak obsługiwać zmiany modelu przy użyciu code first migracji, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.

W folderze *Dane* utwórz nowy plik klasy o nazwie *DbInitializer.cs* i zastąp kod szablonu następującym kodem, co powoduje utworzenie bazy danych w razie potrzeby i wczytywanie danych testowych do nowej bazy danych.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kod sprawdza, czy istnieją studenci w bazie danych, a jeśli nie, zakłada, że baza danych jest nowa i musi być rozstawiony z danymi testowymi. Ładuje dane testowe do `List<T>` tablic, a nie kolekcji w celu optymalizacji wydajności.

W *Program.cs*, zmodyfikuj `Main` metodę, aby wykonać następujące czynności podczas uruchamiania aplikacji:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę seed, przekazując do niej kontekst.
* Zutylizuj kontekst, gdy metoda inicjatora jest wykonywana.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Dodaj `using` instrukcje:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

W starszych samouczkach podobny kod `Configure` można zobaczyć w metodzie *Startup.cs*. Zaleca się, aby `Configure` użyć tej metody tylko do skonfigurowania potoku żądania. Kod startowy aplikacji `Main` należy do metody.

Teraz przy pierwszym uruchomieniu aplikacji baza danych zostanie utworzona i rozstawiona z danymi testowymi. Za każdym razem, gdy zmieniasz model danych, możesz usunąć bazę danych, zaktualizować metodę inicjatora i zacząć od nowa z nową bazą danych w ten sam sposób. W późniejszych samouczkach zobaczysz, jak zmodyfikować bazę danych po zmianie modelu danych, bez usuwania i ponownego tworzenia.

## <a name="create-controller-and-views"></a>Tworzenie kontrolera i widoków

Następnie użyjesz aparatu szkieletu w programie Visual Studio, aby dodać kontroler MVC i widoki, które będą używać EF do wykonywania zapytań i zapisywania danych.

Automatyczne tworzenie metod akcji CRUD i widoków jest znany jako rusztowania. Szkielety różni się od generowania kodu, ponieważ szkieletowy kod jest punktem wyjścia, który można zmodyfikować zgodnie z własnymi wymaganiami, podczas gdy zazwyczaj nie modyfikuje wygenerowany kod. Gdy trzeba dostosować wygenerowany kod, należy użyć klas częściowych lub ponownie wygenerować kod, gdy coś się zmieni.

* Kliknij prawym przyciskiem myszy folder **Kontrolery** w **Eksploratorze rozwiązań** i wybierz polecenie **Dodaj > nowy element szkieletu**.

* W oknie dialogowym **Dodawanie rusztowania:**

  * Wybierz **kontroler MVC z widokami, używając entity framework**.

  * Kliknij przycisk **Dodaj**. Zostanie wyświetlone okno dialogowe **Dodawanie kontrolera MVC z widokami przy użyciu programu Entity Framework.**

    ![Uczeń rusztowania](intro/_static/scaffold-student2.png)

  * W **klasie Model** wybierz **Uczeń**.

  * W **klasie kontekstu danych** wybierz **SchoolContext**.

  * Zaakceptuj domyślny **StudentsController** jako nazwę.

  * Kliknij przycisk **Dodaj**.

  Po kliknięciu **przycisku Dodaj**aparat rusztowań programu Visual Studio tworzy plik *StudentsController.cs* i zestaw widoków (*.cshtml* plików), które współpracują z kontrolerem.

(Aparat szkieletu można również utworzyć kontekst bazy danych dla Ciebie, jeśli nie utworzyć go ręcznie najpierw, jak to miało miejsce wcześniej w tym samouczku. Można określić nową klasę kontekstu w polu **Dodaj kontroler,** klikając znak plus po prawej stronie **klasy kontekstu dane**.  Visual Studio utworzy `DbContext` klasę, a także kontroler i widoki.)

Można zauważyć, że kontroler `SchoolContext` przyjmuje jako parametr konstruktora.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Iniekcja zależności core zajmuje się `SchoolContext` przekazywanie wystąpienia do kontrolera. Skonfigurowano to wcześniej w pliku *Startup.cs.*

Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych. Metoda pobiera listę studentów z Student jednostki `Students` ustawionej przez odczytywanie właściwości wystąpienia kontekstu bazy danych:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Dowiesz się o asynchronicznych elementów programowania w tym kodzie w dalszej części samouczka.

*Widok/Studenci/Index.cshtml* wyświetla tę listę w tabeli:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Naciśnij klawisze CTRL+F5, aby uruchomić projekt, lub wybierz polecenie **Debugowanie > rozpocznij bez debugowania** z menu.

Kliknij kartę Uczniowie, aby wyświetlić dane testowe wstawione przez `DbInitializer.Initialize` metodę. W zależności od tego, jak wąskie `Students` jest okno przeglądarki, zobaczysz łącze karty u góry strony lub musisz kliknąć ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.

![Contoso University strona główna wąska](intro/_static/home-page-narrow.png)

![Strona Indeks uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a>Wyświetlanie bazy danych

Po uruchomieniu aplikacji `DbInitializer.Initialize` metoda wywołuje `EnsureCreated`. EF zobaczył, że nie ma bazy danych i tak `Initialize` utworzono jedną, a następnie pozostałą część kodu metody zapełnił bazę danych danymi. Za pomocą **programu SQL Server Object Explorer** (SSOX) można wyświetlić bazę danych w programie Visual Studio.

Zamknij okno przeglądarki.

Jeśli okno SSOX nie jest jeszcze otwarte, wybierz je z menu **Widok** w programie Visual Studio.

W polu SSOX kliknij **pozycję (localdb)\MSSQLLocalDB > Databases**, a następnie kliknij wpis nazwy bazy danych znajdującej się w ciągu połączenia w pliku *appsettings.json.*

Rozwiń węzeł **Tabele,** aby wyświetlić tabele w bazie danych.

![Tabele w SSOX](intro/_static/ssox-tables.png)

Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.

![Stół studencki w SSOX](intro/_static/ssox-student-table.png)

Pliki bazy danych *.mdf* i *.ldf* znajdują się w folderze *C:\Users\\\<yourusername>.*

Ponieważ wywołujesz `EnsureCreated` w metodzie inicjatora, która działa w uruchomieniu aplikacji, `Student` można teraz wprowadzić zmiany do klasy, usunąć bazę danych, uruchomić aplikację ponownie, a baza danych zostanie automatycznie utworzona ponownie, aby dopasować zmiany. Na przykład jeśli dodasz `EmailAddress` właściwość do `Student` klasy, zobaczysz `EmailAddress` nową kolumnę w tabeli ponownie utworzone.

## <a name="conventions"></a>Konwencja

Ilość kodu trzeba było napisać, aby entity framework, aby móc utworzyć pełną bazę danych dla Ciebie jest minimalna ze względu na użycie konwencji lub założeń, które framework jednostek sprawia, że.

* Nazwy `DbSet` właściwości są używane jako nazwy tabel. W przypadku jednostek, `DbSet` do których nie odwołuje się właściwość, nazwy klas jednostek są używane jako nazwy tabel.

* Nazwy właściwości encji są używane dla nazw kolumn.

* Właściwości encji o nazwie ID lub ClassnameID są rozpoznawane jako właściwości klucza podstawowego.

* Właściwość jest interpretowana jako właściwość klucza obcego, jeśli nosi * \<nazwę właściwości nawigacji>\<nazwa właściwości klucza podstawowego>* (na przykład dla właściwości `StudentID` `Student` nawigacji, ponieważ klucz podstawowy `Student` jednostki jest `ID`). Właściwości klucza obcego można również nazwać po prostu `EnrollmentID` `Enrollment` `EnrollmentID` * \<nazwą właściwości klucza podstawowego>* (na przykład, ponieważ klucz podstawowy jednostki jest ).

Konwencjonalne zachowanie może zostać zastąpione. Na przykład można jawnie określić nazwy tabel, jak widzieliśmy wcześniej w tym samouczku. Możesz też ustawić nazwy kolumn i ustawić dowolną właściwość jako klucz podstawowy lub klucz obcy, co zostanie wyświetlene w [późniejszym samouczku](complex-data-model.md) z tej serii.

## <a name="asynchronous-code"></a>Kod asynchroniczne

Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.

Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć. Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań. W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer jest włączony do obsługi większej liczby ruchu bez opóźnień.

Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania, ale w sytuacjach o małym natężeniu ruchu trafienie wydajności jest znikome, podczas gdy w sytuacjach o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.

W poniższym kodzie `async` `Task<T>` słowo kluczowe, zwraca wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Słowo `async` kluczowe mówi kompilatorowi do generowania wywołań zwrotnych dla `Task<IActionResult>` części treści metody i automatycznie utworzyć obiekt, który jest zwracany.

* Typ `Task<IActionResult>` zwracania reprezentuje bieżącą pracę `IActionResult`z wynikiem typu .

* Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części. Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.

* `ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.

Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa entity framework:

* Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje to na `ToListAsync`przykład `SingleOrDefaultAsync`, `SaveChangesAsync`i . Nie zawiera, na przykład, stwierdzeń, `IQueryable`które `var students = context.Students.Where(s => s.LastName == "Davolio")`po prostu zmienić , takich jak .

* Kontekst EF nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle. Po wywołaniu dowolnej metody asynchronii EF, zawsze używaj słowa kluczowego. `await`

* Jeśli chcesz skorzystać z zalet wydajności kodu asynchronii, upewnij się, że wszystkie pakiety biblioteki, których używasz (na przykład do stronicowania), należy również użyć asynchronii, jeśli wywołają one wszelkie metody entity framework, które powodują kwerendy mają być wysyłane do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utworzono ASP.NET core aplikacji internetowej MVC
> * Konfigurowanie stylu witryny
> * Dowiedz się więcej o pakietach EF Core NuGet
> * Utworzono model danych
> * Utworzono kontekst bazy danych
> * Zarejestrowany SchoolContext
> * Zainicjowana baza danych z danymi testowymi
> * Utworzony kontroler i widoki
> * Wyświetlanie bazy danych

W poniższym samouczku dowiesz się, jak wykonać podstawowe operacje CRUD (tworzenie, czytanie, aktualizowanie, usuwanie).

Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonać podstawowe operacje CRUD (tworzenie, czytanie, aktualizowanie, usuwanie).

> [!div class="nextstepaction"]
> [Wdrażanie podstawowych funkcji CRUD](crud.md)

