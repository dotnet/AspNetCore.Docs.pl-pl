---
title: 'Samouczek: wprowadzenie do EF Core w aplikacji sieci Web ASP.NET MVC'
description: Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają, jak skompilować przykładową aplikację Dr/MVC firmy Contoso.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: ef1d94ce7a0aa853336260b8d73b9d4036c907ac
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340013"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Samouczek: wprowadzenie do EF Core w aplikacji sieci Web ASP.NET MVC

Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Przykładowy punkt dostępu sieci Web firmy Contoso University demonstruje sposób tworzenia aplikacji sieci Web ASP.NET Core MVC przy użyciu narzędzia Entity Framework (EF) Core i programu Visual Studio.

Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University. Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora. Jest to pierwsza z szeregu samouczków, które opisują sposób tworzenia przykładowej aplikacji firmy Contoso University.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli dopiero zaczynasz korzystać z ASP.NET Core MVC, przejdź przez serię samouczka [wprowadzenie do ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) przed rozpoczęciem tego.

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>Aparaty bazy danych

Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>Rozwiązywanie problemów i rozwiązywanie problemów

Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Aby zapoznać się z listą typowych błędów i sposobu ich rozwiązywania, zobacz [sekcję Rozwiązywanie problemów w ostatnim samouczku w serii](advanced.md#common-errors). Jeśli nie możesz znaleźć tego, czego potrzebujesz, możesz ogłosić pytanie do StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Jest to seria 10 samouczków, z których każdy jest oparty na tym, co zostało zrobione we wcześniejszych samouczkach. Rozważ zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka. Jeśli wystąpią problemy, możesz zacząć od poprzedniego samouczka zamiast wrócić do początku całej serii.

## <a name="contoso-university-web-app"></a>Aplikacja sieci Web firmy Contoso University

Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.

Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów. Oto kilka ekranów w aplikacji:

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

1. Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.
1. W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `ContosoUniversity` w polu **Nazwa projektu**. Ważne jest, aby użyć tej dokładnej nazwy, z uwzględnieniem wielkich liter, więc każdy pasuje do tego, `namespace` kiedy kod jest kopiowany.
1. Wybierz przycisk **Utwórz**.
1. W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:
    1. **Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.
    1. **ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.
    1. **Create** 
       Utwórz ![ Nowe okno dialogowe projektu ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Kilka podstawowych zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.

Otwórz *Widok widoki/Shared/_Layout. cshtml* i wprowadź następujące zmiany:

* Zmień każde wystąpienie elementu `ContosoUniversity` na `Contoso University` . Istnieją trzy wystąpienia.
* Dodaj pozycje menu dla **informacji o** programie, **studentów** , **kursy** , **Instruktorzy** i **działy** , a następnie usuń wpis menu **prywatność** .

Powyższe zmiany są wyróżnione w następującym kodzie:

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

W obszarze *widoki/Home/index. cshtml* Zastąp zawartość pliku następującym znacznikiem:

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu. Strona główna jest wyświetlana z kartami dla stron utworzonych w tym samouczku.

![Strona główna firmy Contoso University](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>EF Core pakietów NuGet

Ten samouczek używa SQL Server, a pakiet dostawcy to [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).

Pakiet EF SQL Server i jego zależności `Microsoft.EntityFrameworkCore` oraz `Microsoft.EntityFrameworkCore.Relational` zapewnia obsługę środowiska uruchomieniowego dla EF.

Dodaj pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) oraz pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) . W konsoli Menedżera programów (PMC) wprowadź następujące polecenia, aby dodać pakiety NuGet:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Pakiet NuGet zawiera ASP.NET Core oprogramowanie pośredniczące dla EF Core stron błędów. To oprogramowanie pośredniczące pomaga wykrywać i diagnozować błędy za pomocą migracji EF Core.

Aby uzyskać informacje o innych dostawcach baz danych, które są dostępne dla EF Core, zobacz [dostawcy bazy danych](/ef/core/providers/).

## <a name="create-the-data-model"></a>Tworzenie modelu danych

Dla tej aplikacji są tworzone następujące klasy jednostek:

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

Poprzednie jednostki mają następujące relacje:

* Relacja jeden do wielu między elementami `Student` i `Enrollment` . Student może być zarejestrowany w dowolnej liczbie kursów.
* Relacja jeden do wielu między elementami `Course` i `Enrollment` . Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.

W poniższych sekcjach jest tworzona Klasa dla każdej z tych jednostek.

### <a name="the-student-entity"></a>Jednostka ucznia

![Diagram jednostek uczniów](intro/_static/student-entity.png)

W folderze *modele* Utwórz `Student` klasę o następującym kodzie:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`Właściwość jest kolumną klucza podstawowego ( **PK** ) tabeli bazy danych, która odnosi się do tej klasy. Domyślnie EF interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy. Na przykład klucz podstawowy może być nazwany, `StudentID` a nie `ID` .

`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką. `Enrollments`Właściwość `Student` jednostki:

* Zawiera wszystkie `Enrollment` jednostki, które są powiązane z tą `Student` jednostką.
* Jeśli konkretny `Student` wiersz w bazie danych ma dwa powiązane `Enrollment` wiersze:
  * `Student` `Enrollments` Właściwość nawigacji tej jednostki zawiera te dwie `Enrollment` jednostki.
  
`Enrollment` wiersze zawierają wartość klucza podstawowego ucznia w `StudentID` kolumnie klucz obcy ( **FK** ).

Jeśli właściwość nawigacji może zawierać wiele jednostek:

 * Typ musi być listą, taką jak `ICollection<T>` , `List<T>` , lub `HashSet<T>` .
 * Jednostki można dodawać, usuwać i aktualizować.

Relacje "wiele do wielu" i "jeden do wielu" mogą zawierać wiele jednostek. Gdy `ICollection<T>` jest używany, EF domyślnie tworzy `HashSet<T>` kolekcję.

### <a name="the-enrollment-entity"></a>Jednostka rejestracji

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

W folderze *modele* Utwórz `Enrollment` klasę o następującym kodzie:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Właściwość jest klucz podstawowy. Ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie. `Student`Jednostka użyła `ID` wzorca. Niektórzy deweloperzy wolą używać jednego wzorca w całym modelu danych. W tym samouczku odmiana pokazuje, że można użyć wzorca. W tym [samouczku](inheritance.md) pokazano, jak używać `ID` bez ClassName, ułatwia implementowanie dziedziczenia w modelu danych.

`Grade`Właściwość jest `enum` . `?`Po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types). Klasa, która `null` jest różna od zerowej klasy. `null` oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.

`StudentID`Właściwość jest kluczem obcym (FK), a odpowiednia właściwość nawigacji to `Student` . `Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc właściwość może zawierać tylko jedną `Student` jednostkę. Różni się to od `Student.Enrollments` właściwości nawigacji, która może zawierać wiele `Enrollment` jednostek.

`CourseID`Właściwość jest FK, a odpowiednia właściwość nawigacji to `Course` . `Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.

Entity Framework interpretuje właściwość jako właściwość klucza obcego, jeśli ma nazwę właściwości `<` nawigacyjnej nazwa `><` właściwości klucza podstawowego `>` . Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` . Właściwości klucza obcego mogą również mieć  `<` nazwę właściwości klucz podstawowy `>` . Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .

### <a name="the-course-entity"></a>Jednostka kursu

![Diagram jednostek kursu](intro/_static/course-entity.png)

W folderze *modele* Utwórz `Course` klasę o następującym kodzie:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Właściwość jest właściwością nawigacji. `Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.

Atrybut [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) został wyjaśniony w [późniejszym samouczku](complex-data-model.md). Ten atrybut umożliwia wprowadzenie klucza podstawowego dla kursu, a nie jego wygenerowanie.

## <a name="create-the-database-context"></a>Tworzenie kontekstu bazy danych

Klasa główna, która koordynuje funkcje EF dla danego modelu danych, jest <xref:Microsoft.EntityFrameworkCore.DbContext> klasą kontekstu bazy danych. Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy. `DbContext`Klasa pochodna określa, które jednostki są uwzględnione w modelu danych. Niektóre zachowania EF można dostosować. W tym projekcie Klasa ma nazwę `SchoolContext` .

W folderze projektu Utwórz folder o nazwie `Data` .

W folderze *dane* Utwórz `SchoolContext` klasę o następującym kodzie:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Poprzedni kod tworzy `DbSet` Właściwość dla każdego zestawu jednostek. W terminologii EF:

* Zestaw jednostek zwykle odpowiada tabeli bazy danych.
* Jednostka odnosi się do wiersza w tabeli.

`DbSet<Enrollment>`Instrukcje i `DbSet<Course>` mogą być pominięte i mogą działać tak samo. EF uwzględni je niejawnie, ponieważ:

* `Student`Jednostka odwołuje się do `Enrollment` jednostki.
* `Enrollment`Jednostka odwołuje się do `Course` jednostki.

Po utworzeniu bazy danych EF tworzy tabele, które mają nazwy takie same jak `DbSet` nazwy właściwości. Nazwy właściwości dla kolekcji są zwykle plural. Na przykład `Students` zamiast `Student` . Deweloperzy nie zgadzają się, czy nazwy tabel powinny być w liczbie mnogiej. W przypadku tych samouczków domyślne zachowanie jest przesłonięte przez określenie pojedynczej nazwy tabeli w `DbContext` . Aby to zrobić, Dodaj następujący wyróżniony kod po ostatniej właściwości Nieogólnymi.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zarejestruj `SchoolContext`

ASP.NET Core obejmuje [iniekcję zależności](../../fundamentals/dependency-injection.md). Usługi, takie jak kontekst bazy danych EF, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług, takich jak kontrolery MVC, są dostarczane przez parametry konstruktora. Kod konstruktora kontrolera, który pobiera wystąpienie kontekstu, jest przedstawiony w dalszej części tego samouczka.

Aby zarejestrować się `SchoolContext` jako usługa, otwórz *Startup.cs* i Dodaj wyróżnione wiersze do `ConfigureServices` metody.

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w `DbContextOptionsBuilder` obiekcie. W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.

Otwórz *appsettings.json* plik i Dodaj parametry połączenia, jak pokazano w następującej postaci:

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>Dodaj filtr wyjątku bazy danych

Dodaj <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> do `ConfigureServices` , jak pokazano w poniższym kodzie:

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

`AddDatabaseDeveloperPageExceptionFilter`Zapewnia przydatne informacje o błędzie w [środowisku programistycznym](xref:fundamentals/environments).

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja. Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.

## <a name="initialize-db-with-test-data"></a>Zainicjuj bazę danych z danymi testowymi

EF tworzy pustą bazę danych. W tej sekcji dodano metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnienia jej danymi testowymi.

`EnsureCreated`Metoda jest używana do automatycznego tworzenia bazy danych. W [późniejszym samouczku](migrations.md)zobaczysz, jak obsługiwać zmiany modelu przy użyciu migracje Code First, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.

W folderze *dane* Utwórz nową klasę o nazwie `DbInitializer` przy użyciu następującego kodu:

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

Poprzedni kod sprawdza, czy baza danych istnieje:

* Jeśli baza danych nie zostanie znaleziona,
  * Jest on tworzony i ładowany z danymi testowymi. Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.
* Jeśli baza danych zostanie znaleziona, nie podejmuje żadnych działań.

Zaktualizuj *program.cs* przy użyciu następującego kodu:

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

*Program.cs* wykonuje następujące czynności podczas uruchamiania aplikacji:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj `DbInitializer.Initialize` metodę.
* Usuń kontekst, gdy `Initialize` Metoda zostanie ukończona, jak pokazano w poniższym kodzie:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

Gdy aplikacja jest uruchamiana po raz pierwszy, baza danych zostanie utworzona i załadowana z danymi testowymi. Za każdym razem, gdy zmienia się model danych:

* Usuń bazę danych.
* Zaktualizuj metodę inicjatora i zacznij od nowa bazę danych.

 W kolejnych samouczkach baza danych jest modyfikowana, gdy model danych ulegnie zmianie, bez usuwania i ponownego tworzenia. Po zmianie modelu danych nie są tracone żadne dane.

## <a name="create-controller-and-views"></a>Tworzenie kontrolera i widoków

Użyj aparatu tworzenia szkieletów w programie Visual Studio, aby dodać kontroler MVC i widoki, które będą używać EF do wykonywania zapytań i zapisywania danych.

Automatyczne tworzenie metod i widoków akcji [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) jest znane jako rusztowania.

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `Controllers` folder, a następnie wybierz pozycję **Dodaj > nowy element szkieletowy**.
* W oknie dialogowym **Dodawanie szkieletu** :
  * Wybierz **kontroler MVC z widokami, używając Entity Framework**.
  * Kliknij pozycję **Dodaj**. Pojawia się okno dialogowe **Dodawanie kontrolera MVC z widokami z użyciem Entity Framework** : ![ studenta dla szkieletu](intro/_static/scaffold-student2.png)
  * W obszarze **Klasa modelu** wybierz pozycję **student**.
  * W obszarze **Klasa kontekstu danych** wybierz pozycję **SchoolContext**.
  * Zaakceptuj domyślną **StudentsController** jako nazwę.
  * Kliknij pozycję **Dodaj**.

Aparat szkieletu programu Visual Studio tworzy `StudentsController.cs` plik i zestaw widoków ( `*.cshtml` plików), które współpracują z kontrolerem.

Zwróć uwagę, że kontroler przyjmuje `SchoolContext` jako parametr konstruktora.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core iniekcja zależności ma zadbać o przekazanie wystąpienia `SchoolContext` do kontrolera. Skonfigurowano tę wartość w `Startup` klasie.

Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych. Metoda pobiera listę studentów z zestawu jednostek studentów, odczytując `Students` Właściwość wystąpienia kontekstu bazy danych:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Asynchroniczne elementy programowania w tym kodzie są wyjaśnione w dalszej części tego samouczka.

Widok *widoki/uczniowie/index. cshtml* wyświetla tę listę w tabeli:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.

Kliknij kartę studenci, aby zobaczyć dane testowe, które zostały `DbInitializer.Initialize` wstawione przez metodę. W zależności od tego, jak Zawężasz okno przeglądarki, zobaczysz `Students` link do karty w górnej części strony lub kliknij ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.

![Strona główna firmy Contoso University Narrow](intro/_static/home-page-narrow.png)

![Strona indeksu uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a>Wyświetlanie bazy danych

Gdy aplikacja zostanie uruchomiona, `DbInitializer.Initialize` Metoda wywołuje metodę `EnsureCreated` . EF wykryto, że nie ma bazy danych:

* W związku z tym utworzono bazę danych.
* `Initialize`Kod metody wypełniał bazę danych danymi.

Użyj **Eksplorator obiektów SQL Server** (SSOX), aby wyświetlić bazę danych w programie Visual Studio:

* Wybierz **Eksplorator obiektów SQL Server** z menu **Widok** w programie Visual Studio.
* W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych**.
* Wybierz pozycję `ContosoUniversity1` Nazwa bazy danych, która znajduje się w parametrach połączenia w *appsettings.json* pliku.
* Rozwiń węzeł **tabele** , aby wyświetlić tabele w bazie danych.

![Tabele w SSOX](intro/_static/ssox-tables.png)

Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić dane w tabeli.

![Tabela uczniów w SSOX](intro/_static/ssox-student-table.png)

`*.mdf` `*.ldf` Pliki bazy danych i znajdują się w folderze *C:\Users \\ \<username>* .

Ponieważ `EnsureCreated` jest wywoływana w metodzie inicjatora, która jest uruchamiana podczas uruchamiania aplikacji, można:

* Wprowadź zmianę w `Student` klasie.
* Usuń bazę danych.
* Zatrzymaj, a następnie uruchom aplikację. Baza danych zostanie automatycznie utworzona w celu dopasowania do zmiany.

Na przykład, jeśli `EmailAddress` do klasy zostanie dodana Właściwość `Student` , Nowa `EmailAddress` kolumna w nowo utworzonej tabeli. Nie zostanie wyświetlona nowa właściwość widoku z klasą `EmailAddress` .

## <a name="conventions"></a>Konwencje

Ilość kodu zapisywana w celu utworzenia kompletnej bazy danych przez EF, jest minimalna ze względu na użycie konwencji EF korzysta z:

* Nazwy `DbSet` właściwości są używane jako nazwy tabel. W przypadku jednostek, do których nie odwołuje się `DbSet` Właściwość, nazwy klas jednostek są używane jako nazwy tabel.
* Nazwy właściwości jednostki są używane w nazwach kolumn.
* Właściwości jednostki o nazwach `ID` lub `classnameID` są rozpoznawane jako właściwości klucza podstawowego.
* Właściwość jest interpretowana jako właściwość klucza obcego, jeśli ma nazwę właściwość `<` nawigacji nazwa `><` klucza podstawowego `>` . Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` . Właściwości klucza obcego mogą również mieć `<` nazwę właściwości klucz podstawowy `>` . Na przykład, `EnrollmentID` ponieważ `Enrollment` klucz podstawowy jednostki to `EnrollmentID` .

Zachowanie konwencjonalne można zastąpić. Na przykład nazwy tabel można jawnie określić, jak pokazano wcześniej w tym samouczku. Nazwy kolumn i dowolnej właściwości można ustawić jako PK lub FK.

## <a name="asynchronous-code"></a>Kod asynchroniczny

Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.

Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy. W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań. W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.

Kod asynchroniczny wprowadza niewielką ilość narzutu w czasie wykonywania, ale w przypadku niskiego natężenia ruchu, gdy wydajność jest niewielka, w przypadku dużych sytuacji związanych z ruchem jest istotna poprawa wydajności.

W poniższym kodzie,, `async` , `Task<T>` `await` i sprawić, że `ToListAsync` kod jest wykonywany asynchronicznie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async`Słowo kluczowe instruuje kompilator, aby generował wywołania zwrotne dla części treści metody i automatycznie utworzyć `Task<IActionResult>` zwracany obiekt.
* Typ zwracany `Task<IActionResult>` reprezentuje bieżącą współpracę z wynikiem typu `IActionResult` .
* `await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części. Pierwsza część jest zakończona operacją uruchomioną asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.
* `ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.

Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF:

* Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje to, na przykład, `ToListAsync` , `SingleOrDefaultAsync` i `SaveChangesAsync` . Nie zawiera na przykład instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* Kontekst EF nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji. Gdy wywoływana jest metoda async EF, zawsze używaj `await` słowa kluczowego.
* Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy się upewnić, że wszystkie pakiety biblioteczne używają również metody asynchronicznej, jeśli wywołają one wszelkie metod EF, które powodują wysłanie zapytań do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [asynchroniczne Omówienie](/dotnet/articles/standard/async).

## <a name="limit-entities-fetched"></a>Pobrano limit liczby jednostek

Zobacz [zagadnienia dotyczące wydajności](xref:data/ef-rp/intro) , aby uzyskać informacje na temat ograniczania liczby lub jednostek zwróconych z zapytania.

Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonywać podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).

> [!div class="nextstepaction"]
> [Zaimplementuj podstawowe funkcje CRUD](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji sieci Web ASP.NET Core 2,2 MVC przy użyciu Entity Framework (EF) Core 2,2 i Visual Studio 2017 lub 2019.

Ten samouczek nie został zaktualizowany do ASP.NET Core 3,1. Została zaktualizowana [ASP.NET Core 5,0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).

Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University. Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora. Jest to pierwsza z szeregu samouczków, które wyjaśniają, jak skompilować przykładową aplikację firmy Contoso University od podstaw.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw .NET Core SDK 2,2](https://dotnet.microsoft.com/download)
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:
  * **ASP.NET i programowanie aplikacji sieci Web**
  * **Tworzenie aplikacji dla wielu platform w środowisku .NET Core**

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Aby zapoznać się z listą typowych błędów i sposobu ich rozwiązywania, zobacz [sekcję Rozwiązywanie problemów w ostatnim samouczku w serii](advanced.md#common-errors). Jeśli nie możesz znaleźć tego, czego potrzebujesz, możesz ogłosić pytanie do StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Jest to seria 10 samouczków, z których każdy jest oparty na tym, co zostało zrobione we wcześniejszych samouczkach. Rozważ zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka. Jeśli wystąpią problemy, możesz zacząć od poprzedniego samouczka zamiast wrócić do początku całej serii.

## <a name="contoso-university-web-app"></a>Aplikacja sieci Web firmy Contoso University

Aplikacja, którą tworzysz w tych samouczkach, jest prostą witryną sieci Web.

Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów. Poniżej przedstawiono kilka ekranów, które zostaną utworzone.

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

* Otwórz program Visual Studio.

* Z menu **plik** wybierz pozycję **Nowy projekt >**.

* W lewym okienku wybierz pozycję **zainstalowane > Visual C# > sieci Web**.

* Wybierz szablon projektu **aplikacji sieci Web ASP.NET Core** .

* Wprowadź **ContosoUniversity** jako nazwę, a następnie kliknij przycisk **OK**.

  ![Okno dialogowe Nowy projekt](intro/_static/new-project2.png)

* Zaczekaj, aż pojawi się okno dialogowe **Nowa aplikacja sieci Web ASP.NET Core** .

* Wybierz pozycję **.NET Core** , **ASP.NET Core 2,2** i szablon **aplikacji sieci Web (Model-View-Controller)** .

* Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.

* Wybierz przycisk **OK**.

  ![Nowe okno dialogowe projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Kilka prostych zmian spowoduje skonfigurowanie menu witryny, układu i strony głównej.

Otwórz *Widok widoki/Shared/_Layout. cshtml* i wprowadź następujące zmiany:

* Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso". Istnieją trzy wystąpienia.

* Dodaj pozycje menu dla **informacji o** programie, **studentów** , **kursy** , **Instruktorzy** i **działy** , a następnie usuń wpis menu **prywatność** .

Zmiany są wyróżnione.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

W obszarze *widoki/Home/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu. Zostanie wyświetlona strona główna z kartami dla stron, które zostaną utworzone w tych samouczkach.

![Strona główna firmy Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informacje o pakietach NuGet EF Core

Aby dodać obsługę EF Core do projektu, zainstaluj dostawcę bazy danych, który ma być celem. Ten samouczek używa SQL Server, a pakiet dostawcy to [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie musisz się odwoływać do pakietu.

Pakiet EF SQL Server i jego zależności ( `Microsoft.EntityFrameworkCore` oraz `Microsoft.EntityFrameworkCore.Relational` ) zapewniają obsługę środowiska uruchomieniowego dla EF. Pakiet narzędzi zostanie dodany później, w samouczku [migracji](migrations.md) .

Aby uzyskać informacje o innych dostawcach baz danych, które są dostępne dla Entity Framework Core, zobacz [dostawcy bazy danych](/ef/core/providers/).

## <a name="create-the-data-model"></a>Tworzenie modelu danych

Następnie utworzysz klasy jednostek dla aplikacji firmy Contoso University. Zaczniesz od następujących trzech jednostek.

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

Istnieje relacja jeden do wielu między `Student` jednostkami i i `Enrollment` istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` . Innymi słowy, student może być zarejestrowany w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę uczniów zarejestrowanych w nim.

W poniższych sekcjach utworzysz klasę dla każdej z tych jednostek.

### <a name="the-student-entity"></a>Jednostka ucznia

![Diagram jednostek uczniów](intro/_static/student-entity.png)

W folderze *modele* Utwórz plik klasy o nazwie *student.cs* i Zastąp kod szablonu poniższym kodem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`Właściwość stanie się kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy. Domyślnie platforma Entity Framework interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.

`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką. W tym przypadku `Enrollments` Właściwość a `Student entity` będzie zawierać wszystkie `Enrollment` jednostki, które są powiązane z tą `Student` jednostką. Innymi słowy, jeśli `Student` wiersz w bazie danych ma dwa powiązane `Enrollment` wiersze (wiersze zawierające wartość klucza podstawowego tego ucznia w kolumnie klucza obcego StudentID), `Student` `Enrollments` Właściwość nawigacji tej jednostki będzie zawierać te dwie `Enrollment` jednostki.

Jeśli właściwość nawigacji może zawierać wiele jednostek (tak jak w przypadku relacji "wiele do wielu" lub "jeden do wielu"), jej typem musi być lista, w której można dodawać, usuwać i aktualizować wpisy, na przykład `ICollection<T>` . Możesz określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>` . Jeśli określisz `ICollection<T>` , EF domyślnie tworzy `HashSet<T>` kolekcję.

### <a name="the-enrollment-entity"></a>Jednostka rejestracji

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

W folderze *modele* Utwórz *Enrollment.cs* i Zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Właściwość będzie kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie, jak pokazano w `Student` jednostce. Zwykle należy wybrać jeden wzorzec i używać go w całym modelu danych. Tutaj, odmiana ilustruje, że można użyć dowolnego wzorca. W [późniejszym samouczku](inheritance.md)zobaczysz, jak używać identyfikatora bez ClassName, ułatwia implementowanie dziedziczenia w modelu danych.

`Grade`Właściwość jest `enum` . Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null. Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.

`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` . `Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc właściwość może zawierać tylko jedną `Student` jednostkę (w przeciwieństwie do `Student.Enrollments` wytoczonej wcześniej właściwości nawigacji, która może zawierać wiele `Enrollment` jednostek).

`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` . `Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.

Entity Framework interpretuje właściwość jako właściwość klucza obcego, jeśli jest nazwana `<navigation property name><primary key property name>` (na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` ). Właściwości klucza obcego mogą być również nazywane po prostu `<primary key property name>` (na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` ).

### <a name="the-course-entity"></a>Jednostka kursu

![Diagram jednostek kursu](intro/_static/course-entity.png)

W folderze *modele* Utwórz *Course.cs* i Zastąp istniejący kod następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Właściwość jest właściwością nawigacji. `Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.

Dowiesz się więcej na temat `DatabaseGenerated` atrybutu w [kolejnym samouczku](complex-data-model.md) w tej serii. Zasadniczo ten atrybut umożliwia wprowadzenie klucza podstawowego dla kursu, a nie jego wygenerowanie.

## <a name="create-the-database-context"></a>Tworzenie kontekstu bazy danych

Klasa główna, która koordynuje funkcje Entity Framework dla danego modelu danych, jest klasą kontekstu bazy danych. Tę klasę można utworzyć, wyprowadzając ją z `Microsoft.EntityFrameworkCore.DbContext` klasy. W kodzie możesz określić, które jednostki zostaną uwzględnione w modelu danych. Można również dostosować pewne zachowanie Entity Framework. W tym projekcie Klasa ma nazwę `SchoolContext` .

W folderze projektu Utwórz folder o nazwie *dane*.

W folderze *dane* Utwórz nowy plik klasy o nazwie *SchoolContext.cs* i Zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ten kod tworzy `DbSet` Właściwość dla każdego zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

Można pominąć `DbSet<Enrollment>` `DbSet<Course>` instrukcje i i będzie on działał tak samo. Entity Framework będzie zawierać je niejawnie, ponieważ `Student` Jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.

Po utworzeniu bazy danych EF tworzy tabele, które mają nazwy takie same jak `DbSet` nazwy właściwości. Nazwy właściwości dla kolekcji są zwykle plural (studenci zamiast uczniów), ale deweloperzy zgadzają się na to, czy nazwy tabel powinny być wyrzucane. Te samouczki zastąpią domyślne zachowanie, określając pojedyncze nazwy tabel w kontekście DbContext. Aby to zrobić, Dodaj następujący wyróżniony kod po ostatniej właściwości Nieogólnymi.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

Kompiluj projekt jako sprawdzenie błędów kompilatora.

## <a name="register-the-schoolcontext"></a>Zarejestruj SchoolContext

ASP.NET Core domyślnie implementuje [iniekcję zależności](../../fundamentals/dependency-injection.md) . Usługi (takie jak kontekst bazy danych EF) są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak kontrolery MVC), są dostarczane przez parametry konstruktora. Zobaczysz kod konstruktora kontrolera, który pobiera wystąpienie kontekstu w dalszej części tego samouczka.

Aby zarejestrować się `SchoolContext` jako usługa, otwórz *Startup.cs* i Dodaj wyróżnione wiersze do `ConfigureServices` metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w `DbContextOptionsBuilder` obiekcie. W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.

Dodaj `using` instrukcje dla `ContosoUniversity.Data` i `Microsoft.EntityFrameworkCore` przestrzeni nazw, a następnie Skompiluj projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otwórz *appsettings.json* plik i Dodaj parametry połączenia, jak pokazano w poniższym przykładzie.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Parametry połączenia określają SQL Server bazy danych LocalDB. LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja. Domyślnie LocalDB tworzy pliki bazy danych *MDF* w `C:/Users/<user>` katalogu.

## <a name="initialize-db-with-test-data"></a>Zainicjuj bazę danych z danymi testowymi

Entity Framework utworzy pustą bazę danych. W tej sekcji napiszesz metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnienia jej danymi testowymi.

W tym miejscu będziesz używać `EnsureCreated` metody do automatycznego tworzenia bazy danych. W [późniejszym samouczku](migrations.md) zobaczysz, jak obsłużyć zmiany modelu przy użyciu migracje Code First, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.

W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Zastąp kod szablonu następującym kodem, co spowoduje utworzenie bazy danych w razie potrzeby i załadowanie danych testowych do nowej bazy danych.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kod sprawdza, czy w bazie danych znajdują się uczniowie i czy nie, zakłada, że baza danych jest nowa i należy ją umieścić w danych testowych. Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.

W *program.cs* Zmień metodę, `Main` Aby wykonać następujące czynności podczas uruchamiania aplikacji:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę inicjatora, przekazując ją do kontekstu.
* Usuń kontekst, gdy metoda inicjatora jest gotowa.

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

Przy pierwszym uruchomieniu aplikacji baza danych zostanie utworzona i roztworzona przy użyciu danych testowych. Za każdym razem, gdy zmienisz model danych:

 * Usuń bazę danych.
 * Zaktualizuj metodę inicjatora i zacznij od nowa bazę danych w ten sam sposób.
 
W kolejnych samouczkach zobaczysz, jak zmodyfikować bazę danych, gdy zmieni się model danych, bez usuwania i ponownego tworzenia.

## <a name="create-controller-and-views"></a>Tworzenie kontrolera i widoków

W tej sekcji aparat tworzenia szkieletów w programie Visual Studio służy do dodawania kontrolera MVC i widoków, które będą używać EF do wykonywania zapytań i zapisywania danych.

Automatyczne tworzenie metod i widoków akcji CRUD jest znane jako rusztowania. Tworzenie szkieletu różni się od generowania kodu, ponieważ kod szkieletowy jest punktem wyjścia, który można zmodyfikować zgodnie z własnymi wymaganiami, ale zazwyczaj nie jest modyfikowany kod wygenerowany. Jeśli musisz dostosować wygenerowany kod, użyj klas częściowych lub ponownie Wygeneruj kod, gdy zmienią się.

* Kliknij prawym przyciskiem myszy folder **controllers** w **Eksplorator rozwiązań** a następnie wybierz pozycję **Dodaj > nowy element szkieletowy**.
* W oknie dialogowym **Dodawanie szkieletu** :
  * Wybierz **kontroler MVC z widokami, używając Entity Framework**.
  * Kliknij pozycję **Dodaj**. Pojawia się okno dialogowe **Dodawanie kontrolera MVC z widokami z użyciem Entity Framework** : ![ studenta dla szkieletu](intro/_static/scaffold-student2.png)
  * W obszarze **Klasa modelu** wybierz **ucznia**.
  * W obszarze **Klasa kontekstu danych** wybierz pozycję **SchoolContext**.
  * Zaakceptuj domyślną **StudentsController** jako nazwę.
  * Kliknij pozycję **Dodaj**.

Aparat szkieletu programu Visual Studio tworzy plik *StudentsController.cs* i zestaw widoków (pliki *. cshtml* ), które współpracują z kontrolerem.

Zwróć uwagę, że kontroler przyjmuje `SchoolContext` jako parametr konstruktora.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core iniekcja zależności ma zadbać o przekazanie wystąpienia `SchoolContext` do kontrolera. Który został skonfigurowany w pliku *Startup.cs* .

Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych. Metoda pobiera listę studentów z zestawu jednostek studentów, odczytując `Students` Właściwość wystąpienia kontekstu bazy danych:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Dowiesz się więcej na temat asynchronicznych elementów programowania w tym kodzie w dalszej części tego samouczka.

Widok *widoki/uczniowie/index. cshtml* wyświetla tę listę w tabeli:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.

Kliknij kartę studenci, aby zobaczyć dane testowe, które zostały `DbInitializer.Initialize` wstawione przez metodę. W zależności od tego, jak Zawężasz okno przeglądarki, zobaczysz `Students` link do karty w górnej części strony lub kliknij ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.

![Strona główna firmy Contoso University Narrow](intro/_static/home-page-narrow.png)

![Strona indeksu uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a>Wyświetlanie bazy danych

Po uruchomieniu aplikacji `DbInitializer.Initialize` Metoda wywołuje metodę `EnsureCreated` . EF wykryto, że nie istniała baza danych i dlatego została utworzona, a następnie pozostała część `Initialize` kodu metody wypełnił bazę danych danymi. Aby wyświetlić bazę danych w programie Visual Studio, można użyć **Eksplorator obiektów SQL Server** (SSOX).

Zamknij okno przeglądarki.

Jeśli okno SSOX nie jest jeszcze otwarte, wybierz je z menu **Widok** w programie Visual Studio.

W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych** , a następnie kliknij pozycję Nazwa bazy danych, która znajduje się w parametrach połączenia w *appsettings.json* pliku.

Rozwiń węzeł **tabele** , aby wyświetlić tabele w bazie danych.

![Tabele w SSOX](intro/_static/ssox-tables.png)

Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze, które zostały wstawione do tabeli.

![Tabela uczniów w SSOX](intro/_static/ssox-student-table.png)

Pliki *. mdf* i *. ldf* znajdują się w folderze *C:\Users \\ \<username>* .

Ponieważ wywołujesz `EnsureCreated` metodę inicjatora, która jest uruchamiana podczas uruchamiania aplikacji, możesz teraz wprowadzić zmianę `Student` klasy, usunąć bazę danych, ponownie uruchomić aplikację, a baza danych zostanie automatycznie utworzona ponownie w celu dopasowania do zmiany. Na przykład, jeśli dodasz `EmailAddress` Właściwość do `Student` klasy, zobaczysz nową `EmailAddress` kolumnę w nowo utworzonej tabeli.

## <a name="conventions"></a>Konwencje

Ilość kodu, który miał zostać zapisany w celu Entity Framework być w stanie utworzyć kompletną bazę danych, jest minimalny ze względu na stosowanie Konwencji lub zaEntity Framework łożeń.

* Nazwy `DbSet` właściwości są używane jako nazwy tabel. W przypadku jednostek, do których nie odwołuje się `DbSet` Właściwość, nazwy klas jednostek są używane jako nazwy tabel.
* Nazwy właściwości jednostki są używane w nazwach kolumn.
* Właściwości jednostki o nazwach ID lub classnameID są rozpoznawane jako właściwości klucza podstawowego.
* Właściwość jest interpretowana jako właściwość klucza obcego, jeśli jest nazwana *\<navigation property name>\<primary key property name>* (na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` ). Właściwości klucza obcego mogą być również nazywane po prostu *\<primary key property name>* (na przykład, `EnrollmentID` ponieważ `Enrollment` klucz podstawowy jednostki to `EnrollmentID` ).

Zachowanie konwencjonalne można zastąpić. Na przykład można jawnie określić nazwy tabel, jak zostało to opisane wcześniej w tym samouczku. I można ustawić nazwy kolumn i ustawić dowolną właściwość jako klucz podstawowy lub klucz obcy, jak widać w [późniejszym samouczku](complex-data-model.md) w tej serii.

## <a name="asynchronous-code"></a>Kod asynchroniczny

Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.

Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy. W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań. W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.

Kod asynchroniczny wprowadza niewielką ilość narzutu w czasie wykonywania, ale w przypadku niskiego natężenia ruchu, gdy wydajność jest niewielka, w przypadku dużych sytuacji związanych z ruchem jest istotna poprawa wydajności.

W poniższym kodzie `async` słowo kluczowe, `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async`Słowo kluczowe instruuje kompilator, aby generował wywołania zwrotne dla części treści metody i automatycznie utworzyć `Task<IActionResult>` zwracany obiekt.
* Typ zwracany `Task<IActionResult>` reprezentuje bieżącą współpracę z wynikiem typu `IActionResult` .
* `await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części. Pierwsza część jest zakończona operacją uruchomioną asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.
* `ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.

Niektóre kwestie, o których należy wiedzieć, gdy piszesz kod asynchroniczny, który używa Entity Framework:

* Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje to, na przykład, `ToListAsync` , `SingleOrDefaultAsync` i `SaveChangesAsync` . Nie zawiera na przykład instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* Kontekst EF nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji. Gdy wywoływana jest metoda async EF, zawsze używaj `await` słowa kluczowego.
* Jeśli chcesz wykorzystać zalety wydajności w kodzie asynchronicznym, upewnij się, że wszystkie używane pakiety biblioteki (na przykład na potrzeby stronicowania) używają również metody asynchronicznej, jeśli wywołują wszelkie Entity Framework metod, które powodują wysłanie zapytań do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [asynchroniczne Omówienie](/dotnet/articles/standard/async).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonywać podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).

> [!div class="nextstepaction"]
> [Zaimplementuj podstawowe funkcje CRUD](crud.md)

::: moniker-end
