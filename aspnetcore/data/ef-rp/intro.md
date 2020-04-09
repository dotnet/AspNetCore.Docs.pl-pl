---
title: Strony brzytwy z entity framework core w ASP.NET Core - Samouczek 1 z 8
author: rick-anderson
description: Pokazuje, jak utworzyć aplikację Razor Pages przy użyciu entity framework core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656822"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Strony brzytwy z entity framework core w ASP.NET Core - Samouczek 1 z 8

Przez [Tom Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Jest to pierwszy z serii samouczków, które pokazują, jak korzystać z entity framework (EF) Core w [aplikacji ASP.NET Core Razor Pages.](xref:razor-pages/index) Samouczki kompilacji witryny sieci web dla fikcyjnego Contoso University. Witryna zawiera funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora. Samouczek używa pierwszego podejścia kodu. Aby uzyskać informacje na temat po tym samouczku przy użyciu pierwszej bazy danych podejście, zobacz [ten problem Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pobierz instrukcje](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli dopiero zaczynasz pracę na stronach Razor Pages, przed rozpoczęciem tej serii przejdź do serii samouczków Wprowadzenie do [stron razor.](xref:tutorials/razor-pages/razor-pages-start)

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Aparaty baz danych

Instrukcje programu Visual Studio używają [programu SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji programu SQL Server Express, która działa tylko w systemie Windows.

Instrukcje kodu programu Visual Studio używają [SQLite](https://www.sqlite.org/), międzyplatformowego aparatu bazy danych.

Jeśli zdecydujesz się korzystać z SQLite, pobierz i zainstaluj narzędzie innej firmy do zarządzania i przeglądania bazy danych SQLite, takiej jak [DB Browser for SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz problem, którego nie możesz rozwiązać, porównaj kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com, użycie [tagu core ASP.NET](https://stackoverflow.com/questions/tagged/asp.net-core) lub [znacznika EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Przykładowa aplikacja

Aplikacja wbudowana w te samouczki jest podstawową stroną uniwersytecką. Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach. Oto kilka ekranów utworzonych w samouczku.

![Strona Indeks uczniów](intro/_static/students-index30.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit30.png)

Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów. Samouczek koncentruje się na tym, jak używać EF Core, a nie jak dostosować interfejs użytkownika.

Kliknij łącze w górnej części strony, aby uzyskać kod źródłowy ukończonego projektu. Folder *cu30* zawiera kod ASP.NET wersji Core 3.0 samouczka. Pliki, które odzwierciedlają stan kodu dla tutoriali 1-7 można znaleźć w folderze *cu30snapshots.*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Aby uruchomić aplikację po pobraniu ukończonego projektu:

* Usuń trzy pliki i jeden folder z *elementem SQLite* w nazwie.
* Skompiluj projekt.
* W konsoli Menedżera pakietów (PMC) uruchom następujące polecenie:

  ```powershell
  Update-Database
  ```

* Uruchom projekt do wysiewu bazy danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uruchomić aplikację po pobraniu ukończonego projektu:

* Usuń *plik ContosoUniversity.csproj*i zmień nazwę *pliku ContosoUniversitySQLite.csproj* na *ContosoUniversity.csproj*.
* Usuń *Startup.cs*i zmień nazwę *StartupSQLite.cs* *na Startup.cs*.
* Usuń *appSettings.json*i zmień nazwę *aplikacjiSettingsSQLite.json* na *appSettings.json*.
* Usuń folder *Migracje* i zmień nazwę *MigrationsSQL* na *Migracje*.
* Skompiluj projekt.
* W wierszu polecenia w folderze projektu uruchom następujące polecenia:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* W narzędziu SQLite uruchom następującą instrukcję SQL:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Uruchom projekt do wysiewu bazy danych.

---

## <a name="create-the-web-app-project"></a>Tworzenie projektu aplikacji sieci Web

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* Wybierz **ASP.NET podstawową aplikację sieci Web**.
* Nazwij projekt *ContosoUniversity*. Ważne jest, aby użyć tej dokładnej nazwy, w tym wielkich liter, więc obszary nazw są zgodne, gdy kod jest kopiowany i wklejany.
* Wybierz pozycję **.NET Core** i **ASP.NET Core 3.0** w menu rozwijanym, a następnie wybierz pozycję **Aplikacja sieci Web**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* W terminalu przejdź do folderu, w którym należy utworzyć folder projektu.

* Uruchom następujące polecenia, aby utworzyć projekt `cd` Razor Pages i do nowego folderu projektu:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Skonfiguruj nagłówek witryny, stopkę i menu, aktualizując *pages/shared/_Layout.cshtml*:

* Zmień każde wystąpienie "ContosoUniversity" na "Contoso University". Istnieją trzy zdarzenia.

* Usuń wpisy menu **Strona główna** i **Prywatność** oraz dodaj wpisy dla **informacji**, **Studenci**, **Kursy,** **Instruktorzy**i **Działy**.

Zmiany są wyróżnione.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

W *Pages/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET Core tekstem o tej aplikacji:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Uruchom aplikację, aby sprawdzić, czy jest wyświetlana strona główna.

## <a name="the-data-model"></a>Model danych

Następujące sekcje tworzą model danych:

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

Uczeń może zapisać się na dowolną liczbę kursów, a kurs może mieć dowolną liczbę studentów.

## <a name="the-student-entity"></a>Jednostka Student

![Diagram encji ucznia](intro/_static/student-entity.png)

* Utwórz folder *Modele* w folderze projektu. 

* Utwórz *modele/student.cs* z następującym kodem:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Właściwość `ID` staje się kolumną klucza podstawowego tabeli bazy danych, która odpowiada tej klasie. Domyślnie EF Core interpretuje właściwość, `ID` `classnameID` która jest nazwany lub jako klucz podstawowy. Tak więc alternatywą automatycznie `Student` rozpoznawaną nazwę `StudentID`dla klasy klucz podstawowy jest .

Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji przechowują inne jednostki, które są powiązane z tą encją. W takim przypadku `Enrollments` właściwość `Student` jednostki posiada `Enrollment` wszystkie jednostki, które są związane z tym Student. Na przykład jeśli wiersz studenta w bazie danych ma `Enrollments` dwa powiązane wiersze rejestracji, właściwość nawigacji zawiera te dwie jednostki rejestracji. 

W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora ucznia. Załóżmy na przykład, że wiersz studenta ma identyfikator=1. Powiązane wiersze rejestracji będą miały StudentID = 1. StudentID jest *kluczem obcym* w tabeli Rejestracja. 

Właściwość `Enrollments` jest `ICollection<Enrollment>` zdefiniowana jako ponieważ może istnieć wiele powiązanych jednostek rejestracji. Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>`. Gdy `ICollection<Enrollment>` jest używany, EF `HashSet<Enrollment>` Core tworzy kolekcję domyślnie.

## <a name="the-enrollment-entity"></a>Jednostka Rejestracji

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

Utwórz *modele/enrollment.cs* z następującym kodem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Właściwość `EnrollmentID` jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca `ID` zamiast samego. W przypadku modelu danych produkcyjnych wybierz jeden wzorzec i użyj go konsekwentnie. Ten samouczek używa zarówno tylko do zilustrowania, że oba działają. Korzystanie `ID` `classname` bez ułatwia implementowanie niektórych rodzajów zmian modelu danych.

Obiekt `Grade` jest `enum`własnością . Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Stopień, który jest null różni się&mdash;od zerowej klasy null oznacza, że klasa nie jest znana lub nie została jeszcze przypisana.

Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest . Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, `Student` więc właściwość zawiera jedną jednostkę.

Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest . Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.

EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwany `<navigation property name><primary key property name>`. Na przykład`StudentID` jest kluczem `Student` obcym właściwości `Student` nawigacji, ponieważ kluczem podstawowym jednostki jest `ID`. Właściwości klucza obcego `<primary key property name>`mogą być również nazwane . Na przykład, `CourseID` `Course` ponieważ kluczem podstawowym `CourseID`jednostki jest .

## <a name="the-course-entity"></a>Jednostka Kurs

![Diagram encji kursu](intro/_static/course-entity.png)

Utwórz *modele/course.cs* z następującym kodem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Właściwość `Enrollments` jest właściwością nawigacji. Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.

Atrybut `DatabaseGenerated` umożliwia aplikacji określić klucz podstawowy, a nie o bazy danych wygenerować go.

Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilatora.

## <a name="scaffold-student-pages"></a>Strony dla uczniów rusztowania

W tej sekcji służy ASP.NET narzędzie do tworzenia rusztowań rdzenia do generowania:

* Klasa *kontekstu* EF Core. Kontekst jest klasą główną, która koordynuje funkcjonalność entity framework dla danego modelu danych. Pochodzi z `Microsoft.EntityFrameworkCore.DbContext` klasy.
* Strony maszynki do golenia obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` encji.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Tworzenie folderu *Uczniowie* w folderze *Strony.*
* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Strony/Uczniowie* i wybierz pozycję **Dodaj** > **nowy element rusztowania**.
* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **ADD**.
* W oknie dialogowym **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):**
  * Z **listy** rozwijanej Klasa Model wybierz pozycję **Uczeń (ContosoUniversity.Models).**
  * W wierszu **klasy Kontekst** **+** danych wybierz znak (plus).
  * Zmień nazwę kontekstu danych z *ContosoUniversity.Models.ContosoUniversityContext* na *ContosoUniversity.Data.SchoolContext*.
  * Wybierz pozycję **Dodaj**.

Następujące pakiety są instalowane automatycznie:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom następujące polecenia .NET Core CLI, aby zainstalować wymagane pakiety NuGet:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  Pakiet Microsoft.VisualStudio.Web.CodeGeneration.Design jest wymagany do tworzenia rusztowań. Mimo że aplikacja nie będzie używać programu SQL Server, narzędzie do tworzenia szkieletów wymaga pakietu programu SQL Server.

* Tworzenie folderu *Strony/Uczniowie.*

* Uruchom następujące polecenie, aby zainstalować [narzędzie do tworzenia rusztowań aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Uruchom następujące polecenie do tworzenia szkieletów stron uczniów.

  **W systemie Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **W systemie macOS lub Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Jeśli masz problem z poprzednim krokiem, skompiluj projekt i ponów próbę kroku szkieletu.

Proces rusztowania:

* Tworzy strony Razor w folderze *Strony/Uczniowie:*
  * *Create.cshtml* i *Create.cshtml.cs*
  * *Usuń.cshtml* i *Delete.cshtml.cs*
  * *Details.cshtml* i *Details.cshtml.cs*
  * *Edit.cshtml* i *Edit.cshtml.cs*
  * *Index.cshtml* i *Index.cshtml.cs*
* Tworzy *dane/SchoolContext.cs*.
* Dodaje kontekst do iniekcji zależności w *Startup.cs*.
* Dodaje ciąg połączenia bazy danych do *pliku appsettings.json*.

## <a name="database-connection-string"></a>Parametry połączenia bazy danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Parametry połączenia określają [usługę SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego. Domyślnie LocalDB tworzy pliki *mdf* w `C:/Users/<user>` katalogu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zmień parametry połączenia, aby wskazać plik bazy danych SQLite o nazwie *CU.db:*

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualizowanie klasy kontekstu bazy danych

Klasą główną, która koordynuje funkcje EF Core dla danego modelu danych jest klasą kontekstu bazy danych. Kontekst pochodzi od pliku [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst określa, które jednostki są uwzględnione w modelu danych. W tym projekcie klasa `SchoolContext`nosi nazwę .

Zaktualizuj *SchoolContext.cs* następującym kodem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Wyróżniony kod tworzy [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwości dla każdego zestawu jednostek. W terminologii EF Core:

* Zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.
* Jednostka odpowiada wierszowi w tabeli.

Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości DBSet powinny być nazwami mnogimi. Ponieważ narzędzie rusztowania`Student` utworzyło zestaw DBSet, ten `Students`krok zmienia go na mnogą . 

Aby kod Razor Pages był zgodny z nową nazwą DBSet, `_context.Student` dokonaj globalnej zmiany w całym projekcie na `_context.Students`.  Istnieje 8 zdarzeń.

Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilatora.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.

Narzędzie szkieletu automatycznie zarejestrował klasę kontekstu z kontenerem iniekcji zależności.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W `ConfigureServices`, wyróżnione linie zostały dodane przez rusztowania:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* W `ConfigureServices`, upewnij się, że kod dodany `UseSqlite`przez wywołanie rusztowania .

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

## <a name="create-the-database"></a>Tworzenie bazy danych

Zaktualizuj *Program.cs,* aby utworzyć bazę danych, jeśli nie istnieje:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) Metoda nie wykonuje żadnych akcji, jeśli baza danych dla kontekstu istnieje. Jeśli nie istnieje baza danych, tworzy bazę danych i schemat. `EnsureCreated`umożliwia następujące przepływ pracy do obsługi zmian modelu danych:

* Usuń bazę danych. Wszystkie istniejące dane są tracone.
* Zmień model danych. Na przykład dodaj `EmailAddress` pole.
* Uruchom aplikację.
* `EnsureCreated`tworzy bazę danych z nowym schematem.

Ten przepływ pracy działa dobrze na wczesnym etapie rozwoju, gdy schemat szybko ewoluuje, o ile nie trzeba zachować danych. Sytuacja jest inna, gdy dane, które zostały wprowadzone do bazy danych musi zostać zachowana. W takim przypadku należy użyć migracji.

W dalszej części serii samouczków można `EnsureCreated` usunąć bazę danych, która została utworzona przez i zamiast tego używać migracji. Nie można zaktualizować `EnsureCreated` bazy danych utworzonej przy użyciu migracji.

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację.
* Wybierz łącze **Uczniowie,** a następnie **utwórz nowy**.
* Przetestuj linki Edytuj, Szczegóły i Usuń.

## <a name="seed-the-database"></a>Seed bazy danych

Metoda `EnsureCreated` tworzy pustą bazę danych. W tej sekcji dodano kod, który wypełnia bazę danych danymi testowymi.

Utwórz *plik Data/DbInitializer.cs* za pomocą następującego kodu:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kod sprawdza, czy w bazie danych są uczniowie. Jeśli nie ma żadnych studentów, dodaje dane testowe do bazy danych. Tworzy dane testowe w tablicach, `List<T>` a nie w kolekcjach w celu optymalizacji wydajności.

* W *Program.cs*, zastąp `EnsureCreated` `DbInitializer.Initialize` połączenie połączeniem:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zatrzymaj aplikację, jeśli jest uruchomiona, i uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zatrzymaj aplikację, jeśli jest uruchomiona, i usuń plik *CU.db.*

---

* Uruchom ponownie aplikację.

* Wybierz stronę Uczniowie, aby wyświetlić rozstawione dane.

## <a name="view-the-database"></a>Wyświetlanie bazy danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Otwórz **program SQL Server Object Explorer** (SSOX) z menu **Widok** w programie Visual Studio.
* W obszarze SSOX wybierz **(localdb)\MSSQLLocalDB > databases > SchoolContext-{GUID}**. Nazwa bazy danych jest generowana na podstawie nazwy kontekstu podanej wcześniej oraz myślnika i identyfikatora GUID.
* Rozwiń węzeł **Tabele.**
* Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.
* Kliknij prawym przyciskiem myszy tabelę **Student** `Student` i kliknij `Student` polecenie **Wyświetl kod,** aby zobaczyć, jak model jest mapowany do schematu tabeli.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane rozstawione. Plik bazy danych nosi nazwę *CU.db* i znajduje się w folderze projektu.

---

## <a name="asynchronous-code"></a>Kod asynchroniczne

Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.

Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć. Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań. W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer może obsługiwać większy ruch bez opóźnień.

Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania. W sytuacjach o niskim natężeniu ruchu osiągi są znikome, podczas gdy w przypadku sytuacji o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.

W poniższym kodzie [asynchroniczne](/dotnet/csharp/language-reference/keywords/async) słowo kluczowe, `Task<T>` zwracana wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Słowo `async` kluczowe mówi kompilatorowi:
  * Generowanie wywołań zwrotnych dla części treści metody.
  * Utwórz obiekt [Zadanie,](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) który jest zwracany.
* Typ `Task<T>` zwracania reprezentuje bieżącą pracę.
* Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części. Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.
* `ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.

Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa EF Core:

* Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje `ToListAsync`to `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, i . Nie zawiera instrukcji, które po `IQueryable`prostu `var students = context.Students.Where(s => s.LastName == "Davolio")`zmienić , takich jak .
* Kontekst EF Core nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.
* Aby skorzystać z zalet wydajności kodu asynchronii, sprawdź, czy pakiety biblioteki (takie jak do stronicowania) używają asynchronii, jeśli wywołują metody EF Core, które wysyłają zapytania do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne za pomocą asynchronii i await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Następny samouczek](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Przykładowa aplikacja internetowa Contoso University pokazuje, jak utworzyć aplikację ASP.NET Core Razor Pages przy użyciu programu Entity Framework (EF) Core.

Przykładowa aplikacja jest witryną sieci web fikcyjnego Uniwersytetu Contoso. Obejmuje funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora. Ta strona jest pierwszą z serii samouczków, które wyjaśniają, jak utworzyć przykładową aplikację Uniwersytetu Contoso.

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pobierz instrukcje](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Znajomość [stron Razor](xref:razor-pages/index). Nowi programiści powinni [ukończyć pracę z razor Pages](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli napotkasz problem, którego nie możesz rozwiązać, zazwyczaj możesz znaleźć rozwiązanie, porównując kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) Dobrym sposobem na uzyskanie pomocy jest opublikowanie pytania do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Aplikacja internetowa Uniwersytetu Contoso

Aplikacja wbudowana w te samouczki jest podstawową stroną uniwersytecką.

Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach. Oto kilka ekranów utworzonych w samouczku.

![Strona Indeks uczniów](intro/_static/students-index.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit.png)

Styl interfejsu użytkownika tej witryny jest zbliżony do tego, co jest generowane przez wbudowane szablony. Samouczek koncentruje się na EF Core ze stronami Razor, a nie interfejsem użytkownika.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Tworzenie aplikacji internetowej Strony razorów ContosoUniversity

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* Utwórz nową ASP.NET podstawową aplikację sieci Web. Nazwij projekt **ContosoUniversity**. Ważne jest, aby nadać nazwę projektowi *ContosoUniversity,* aby obszary nazw były zgodne, gdy kod jest kopiowany/wklejany.
* Wybierz **ASP.NET Core 2.1** w menu rozwijanym, a następnie wybierz pozycję **Aplikacja sieci Web**.

Aby zapoznać się z obrazami powyższych kroków, zobacz [Tworzenie aplikacji internetowej Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Uruchom aplikację.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Kilka zmian skonfigurować menu witryny, układ i stronę główną. Aktualizuj *strony/udostępnione/_Layout.cshtml* o następujące zmiany:

* Zmień każde wystąpienie "ContosoUniversity" na "Contoso University". Istnieją trzy zdarzenia.

* Dodaj pozycje menu dla **studentów,** **kursów,** **instruktorów**i **działów**i usuń wpis menu **Kontakt.**

Zmiany są wyróżnione. (Wszystkie znaczniki *nie* są wyświetlane).

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

W *Pages/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET i MVC tekstem o tej aplikacji:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Tworzenie modelu danych

Tworzenie klas jednostek dla aplikacji Contoso University. Zacznij od następujących trzech jednostek:

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

Istnieje relacja jeden do wielu między `Student` `Enrollment` jednostkami i jednostkami. Istnieje relacja jeden do wielu między `Course` `Enrollment` jednostkami i jednostkami. Student może zapisać się na dowolną liczbę kursów. Kurs może mieć dowolną liczbę studentów zapisanych w nim.

W poniższych sekcjach tworzona jest klasa dla każdej z tych jednostek.

### <a name="the-student-entity"></a>Jednostka Student

![Diagram encji ucznia](intro/_static/student-entity.png)

Utwórz folder *Modele.* W folderze *Modele* utwórz plik klasy o nazwie *Student.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Właściwość `ID` staje się kolumną klucza podstawowego tabeli bazy danych (DB), która odpowiada tej klasie. Domyślnie EF Core interpretuje właściwość, `ID` `classnameID` która jest nazwany lub jako klucz podstawowy. W `classnameID` `classname` , jest nazwą klasy. Alternatywny automatycznie rozpoznawany klucz `StudentID` podstawowy znajduje się w poprzednim przykładzie.

Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą encją. W takim przypadku `Enrollments` właściwość `Student entity` posiada wszystkie `Enrollment` podmioty, które są `Student`z tym powiązane . Na przykład jeśli wiersz studenta w bazy danych ma `Enrollments` dwa powiązane wiersze rejestracji, właściwość nawigacji zawiera te dwie `Enrollment` jednostki. Pokrewny `Enrollment` wiersz to wiersz zawierający podstawową `StudentID` wartość klucza ucznia w kolumnie. Załóżmy na przykład, że uczeń z identyfikatorem=1 ma dwa wiersze w `Enrollment` tabeli. Tabela `Enrollment` ma dwa `StudentID` wiersze z = 1. `StudentID`jest kluczem obcym w `Enrollment` tabeli, `Student` który określa ucznia w tabeli.

Jeśli właściwość nawigacji może pomieścić wiele jednostek, właściwość nawigacji `ICollection<T>`musi być typem listy, na przykład . `ICollection<T>`można określić lub typ, `List<T>` `HashSet<T>`taki jak lub . Gdy `ICollection<T>` jest używany, EF `HashSet<T>` Core tworzy kolekcję domyślnie. Właściwości nawigacji, które przechowują wiele jednostek pochodzą z relacji wiele do wielu i jeden do wielu.

### <a name="the-enrollment-entity"></a>Jednostka Rejestracji

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

W folderze *Modele* utwórz *Enrollment.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Właściwość `EnrollmentID` jest kluczem podstawowym. Ta jednostka używa `classnameID` wzorca, `ID` a `Student` nie jak jednostki. Zazwyczaj deweloperzy wybrać jeden wzorzec i używać go w całym modelu danych. W późniejszym samouczku przy użyciu identyfikatora bez nazwy klasy jest wyświetlany, aby ułatwić implementowanie dziedziczenia w modelu danych.

Obiekt `Grade` jest `enum`własnością . Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest nullable. Stopień, który ma wartość null, różni się od klasy zerowej — wartość null oznacza, że ocena nie jest znana lub nie została jeszcze przypisana.

Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest . Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, `Student` więc właściwość zawiera jedną jednostkę. Jednostka `Student` różni się `Student.Enrollments` od właściwości nawigacji, `Enrollment` która zawiera wiele jednostek.

Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest . Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.

EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwany `<navigation property name><primary key property name>`. Na przykład`StudentID` dla `Student` właściwości nawigacji, `Student` ponieważ kluczem `ID`podstawowym jednostki jest . Właściwości klucza obcego `<primary key property name>`mogą być również nazwane . Na przykład, `CourseID` `Course` ponieważ kluczem podstawowym `CourseID`jednostki jest .

### <a name="the-course-entity"></a>Jednostka Kurs

![Diagram encji kursu](intro/_static/course-entity.png)

W folderze *Modele* utwórz *Course.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Właściwość `Enrollments` jest właściwością nawigacji. Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.

Atrybut `DatabaseGenerated` umożliwia aplikacji określić klucz podstawowy, a nie o db wygenerować go.

## <a name="scaffold-the-student-model"></a>Rusztowanie modelu studenta

W tej sekcji model studenta jest szkieletu. Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu studenta.

* Skompiluj projekt.
* Utwórz folder *Strony/Uczniowie.*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Strony/Uczniowie* > **Dodaj** > **nowy element rusztowania**.
* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **ADD**.

Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:

* Z **listy** rozwijanej Klasa Model wybierz pozycję **Uczeń (ContosoUniversity.Models).**
* W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zmień nazwę wygenerowaną na **ContosoUniversity.Models.SchoolContext**.
* W **klasie kontekstu danych wybierz** pozycję **ContosoUniversity.Models.SchoolContext**
* Wybierz pozycję **Dodaj**.

![Okno dialogowe CRUD](intro/_static/s1.png)

Zobacz [Szkielet modelu filmu,](xref:tutorials/razor-pages/model#scaffold-the-movie-model) jeśli masz problem z poprzednim krokiem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenia, aby s rusztowania modelu studenta.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Proces rusztowania utworzył i zmienił następujące pliki:

### <a name="files-created"></a>Utworzone pliki

* *Strony/studenci* Tworzenie, usuwanie, szczegóły, edycja, indeks.
* *Dane/SzkołaContext.cs*

### <a name="file-updates"></a>Aktualizacje plików

* *Startup.cs* : Zmiany w tym pliku są szczegółowo opisane w następnej sekcji.
* *appsettings.json* : Ciąg połączenia używany do łączenia się z lokalną bazą danych jest dodawany.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Badanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu db jest wyświetlany w dalszej części samouczka.

Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.

Sprawdź `ConfigureServices` metodę w *Startup.cs*. Podświetlona linia została dodana przez rusztowanie:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

## <a name="update-main"></a>Aktualizacja główna

W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołanie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Zutylizuj `EnsureCreated` kontekst po zakończeniu metody.

Poniższy kod przedstawia zaktualizowany plik *Program.cs.*

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`zapewnia, że baza danych dla kontekstu istnieje. Jeśli istnieje, nie zostaną podjęte żadne działania. Jeśli nie istnieje, a następnie bazy danych i wszystkie jego schematu są tworzone. `EnsureCreated`nie używa migracji do tworzenia bazy danych. Baza danych, która `EnsureCreated` jest tworzona z nie można później zaktualizować przy użyciu migracji.

`EnsureCreated`jest wywoływana na uruchomienie aplikacji, co pozwala na następujący przepływ pracy:

* Usuń bazę danych.
* Zmień schemat bazy danych (na przykład `EmailAddress` dodaj pole).
* Uruchom aplikację.
* `EnsureCreated`tworzy bazę danych`EmailAddress` z kolumną.

`EnsureCreated`jest wygodne na wczesnym etapie rozwoju, gdy schemat szybko ewoluuje. W dalszej części samouczka baza danych jest usuwana i używane są migracje.

### <a name="test-the-app"></a>Testowanie aplikacji

Uruchom aplikację i zaakceptuj politykę plików cookie. Ta aplikacja nie przechowuje danych osobowych. Informacje na temat polityki dotyczącej plików cookie można znaleźć na [stronie pomocy w ogólnym rozporządzeniu o ochronie danych (RODO).](xref:security/gdpr)

* Wybierz łącze **Uczniowie,** a następnie **utwórz nowy**.
* Przetestuj linki Edytuj, Szczegóły i Usuń.

## <a name="examine-the-schoolcontext-db-context"></a>Badanie kontekstu bazy danych SchoolContext

Klasą główną, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych. Kontekst danych pochodzi z pliku [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych. W tym projekcie klasa `SchoolContext`nosi nazwę .

Zaktualizuj *SchoolContext.cs* następującym kodem:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Wyróżniony kod tworzy [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwości dla każdego zestawu jednostek. W terminologii EF Core:

* Zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.
* Jednostka odpowiada wierszowi w tabeli.

`DbSet<Enrollment>`i `DbSet<Course>` mogą zostać pominięte. EF Core zawiera je `Student` niejawnie, `Enrollment` ponieważ jednostka `Enrollment` odwołuje się `Course` do jednostki, a jednostka odwołuje się do jednostki. W tym samouczku, zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Parametry połączenia określają [usługę SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji. Domyślnie LocalDB tworzy pliki *.mdf* `C:/Users/<user>` DB w katalogu.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Dodaj kod, aby zainicjować bazę danych za pomocą danych testowych

EF Core tworzy pustą bazę danych. W tej sekcji `Initialize` metoda jest zapisywana, aby wypełnić go danymi testowymi.

W folderze *Dane* utwórz nowy plik klasy o nazwie *DbInitializer.cs* i dodaj następujący kod:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Uwaga: Poprzedni kod używa `Models` dla obszaru`namespace ContosoUniversity.Models`nazw ( `Data`) zamiast . `Models`jest zgodny z kodem wygenerowanym przez szkielety. Aby uzyskać więcej informacji, zobacz [ten problem z rusztowaniami GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Kod sprawdza, czy w pb. Jeśli w db nie ma uczniów, baza danych jest inicjowana przy obliczu danych testowych. Ładuje dane testowe do `List<T>` tablic, a nie kolekcji w celu optymalizacji wydajności.

Metoda `EnsureCreated` automatycznie tworzy bazę danych dla kontekstu bazy danych. Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikowania bazy danych.

W *Program.cs*, zmodyfikuj `Main` metodę wywoływania: `Initialize`

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zatrzymaj aplikację, jeśli jest uruchomiona, i uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zatrzymaj aplikację, jeśli jest uruchomiona, i usuń plik *CU.db.*

---

## <a name="view-the-db"></a>Wyświetlanie bazy danych

Nazwa bazy danych jest generowana na podstawie nazwy kontekstu podanej wcześniej oraz myślnika i identyfikatora GUID. W związku z tym nazwa bazy danych będzie "SchoolContext-{GUID}". Identyfikator GUID będzie inny dla każdego użytkownika.
Otwórz **program SQL Server Object Explorer** (SSOX) z menu **Widok** w programie Visual Studio.
W obszarze SSOX kliknij **(localdb)\MSSQLLocalDB > databases > SchoolContext-{GUID}**.

Rozwiń węzeł **Tabele.**

Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.

## <a name="asynchronous-code"></a>Kod asynchroniczne

Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.

Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć. Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań. W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer jest włączony do obsługi większej liczby ruchu bez opóźnień.

Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania. W sytuacjach o niskim natężeniu ruchu osiągi są znikome, podczas gdy w przypadku sytuacji o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.

W poniższym kodzie [asynchroniczne](/dotnet/csharp/language-reference/keywords/async) słowo kluczowe, `Task<T>` zwracana wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Słowo `async` kluczowe mówi kompilatorowi:
  * Generowanie wywołań zwrotnych dla części treści metody.
  * Automatycznie utwórz obiekt [Zadanie,](/dotnet/api/system.threading.tasks.task) który jest zwracany. Aby uzyskać więcej informacji, zobacz [Typ zwrotu zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Niejawny `Task` typ zwracany reprezentuje bieżącą pracę.
* Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części. Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.
* `ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.

Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa EF Core:

* Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje to `ToListAsync` `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`, i . Nie zawiera instrukcji, które po `IQueryable`prostu `var students = context.Students.Where(s => s.LastName == "Davolio")`zmienić , takich jak .
* Kontekst EF Core nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.
* Aby skorzystać z zalet wydajności kodu asynchronii, sprawdź, czy pakiety biblioteki (takie jak do stronicowania) używają asynchronii, jeśli wywołują metody EF Core, które wysyłają zapytania do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne za pomocą asynchronii i await](/dotnet/csharp/programming-guide/concepts/async/).

W następnym samouczku sprawdzane są podstawowe operacje CRUD (tworzenie, odczyt, aktualizacja, usuwanie).



## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Dalej](xref:data/ef-rp/crud)

::: moniker-end
