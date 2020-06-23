---
title: RazorStrony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8
author: rick-anderson
description: Pokazuje, jak utworzyć Razor aplikację stron przy użyciu Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/intro
ms.openlocfilehash: a6915da23124b7ed4bfaa982692635f9fc75f96a
ms.sourcegitcommit: 726b8c5cf92e6f6a4d0205787b19307e889d6240
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "82967519"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>RazorStrony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8

Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) . Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso. Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora. Samouczek używa metody Code First. Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instrukcje pobierania](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Aparaty bazy danych

Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.

Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.

Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Przykładowa aplikacja

Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web. Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów. Poniżej przedstawiono kilka ekranów utworzonych w samouczku.

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów. Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.

Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu. Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka. Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Aby uruchomić aplikację po pobraniu ukończonego projektu:

* Skompiluj projekt.
* W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:

  ```powershell
  Update-Database
  ```

* Uruchom projekt, aby wypełniać bazę danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uruchomić aplikację po pobraniu ukończonego projektu:

* Usuń *ContosoUniversity. csproj*i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.
* Usuń *Startup.cs*i zmień nazwę *StartupSQLite.cs* na *Startup.cs*.
* Usuń *appSettings.js*i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.
* Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.
* Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.
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

* Uruchom projekt, aby wypełniać bazę danych.

---

## <a name="create-the-web-app-project"></a>Tworzenie projektu aplikacji sieci Web

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**.
* Nazwij projekt *ContosoUniversity*. Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.
* Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.

* Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml*:

* Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso". Istnieją trzy wystąpienia.

* Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów**, **kursów**, **instruktorów**i **działów**.

Zmiany są wyróżnione.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.

## <a name="the-data-model"></a>Model danych

W poniższych sekcjach opisano tworzenie modelu danych:

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.

## <a name="the-student-entity"></a>Jednostka ucznia

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* Utwórz folder *models* w folderze projektu. 

* Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy. Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy. W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` . Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).

`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką. W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem. Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji. 

W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta. Załóżmy na przykład, że wiersz student ma identyfikator = 1. Powiązane wiersze rejestracji będą mieć StudentID = 1. StudentID jest *kluczem obcym* w tabeli rejestracji. 

`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji. Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` . Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.

## <a name="the-enrollment-entity"></a>Jednostka rejestracji

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie. Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie. Ten samouczek używa obu tych metod, aby zilustrować, że obie działają. Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.

`Grade`Właściwość jest `enum` . Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.

`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` . `Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.

`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` . `Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.

EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` . Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` . Właściwości klucza obcego mogą być również nazwane `<primary key property name>` . Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .

## <a name="the-course-entity"></a>Jednostka kursu

![Diagram jednostek kursu](intro/_static/course-entity.png)

Utwórz *modele/kurs. cs* przy użyciu następującego kodu:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments`Właściwość jest właściwością nawigacji. `Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.

Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.

Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.

## <a name="scaffold-student-pages"></a>Strony uczniów tworzenia szkieletu

W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:

* Klasa *kontekstu* EF Core. Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych. Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.
* Razorstrony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Utwórz folder *uczniów* w folderze *strony* .
* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.
* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję ** Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.
* Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:
  * Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.
  * W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).
  * Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.
  * Wybierz pozycję **Dodaj**.

Następujące pakiety są instalowane automatycznie:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:
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

  Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów. Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.

* Utwórz folder *stron/uczniów* .

* Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.

  **W systemie Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **W systemie macOS lub Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.

Proces tworzenia szkieletu:

* Tworzy Razor strony w folderze *stron/uczniów* :
  * *Create. cshtml* i *Create.cshtml.cs*
  * *DELETE. cshtml* i *DELETE.cshtml.cs*
  * *Details. cshtml* i *details.cshtml.cs*
  * *Edit. cshtml* i *Edit.cshtml.cs*
  * *Index. cshtml* i *index.cshtml.cs*
* Tworzy *dane/SchoolContext. cs*.
* Dodaje kontekst do iniekcji zależności w *Startup.cs*.
* Dodaje parametry połączenia z bazą danych do *appsettings.js*.

## <a name="database-connection-string"></a>Parametry połączenia z bazą danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym. Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualizowanie klasy kontekstu bazy danych

Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych. Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst określa, które jednostki są uwzględnione w modelu danych. W tym projekcie Klasa ma nazwę `SchoolContext` .

Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek. W EF Core terminologii:

* Zestaw jednostek zwykle odpowiada tabeli bazy danych.
* Jednostka odnosi się do wiersza w tabeli.

Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej. Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` . 

Aby Razor kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .  Istnieją 8 wystąpień.

Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji. Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.

Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.

## <a name="create-the-database"></a>Tworzenie bazy danych

Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji. Jeśli baza danych nie istnieje, tworzy bazę danych i schemat. `EnsureCreated`umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:

* Usuń bazę danych. Wszystkie istniejące dane zostaną utracone.
* Zmień model danych. Na przykład Dodaj `EmailAddress` pole.
* Uruchom aplikację.
* `EnsureCreated`tworzy bazę danych z nowym schematem.

Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych. Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane. W takim przypadku należy użyć migracji.

W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja. Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację.
* Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.
* Przetestuj linki Edytuj, Szczegóły i Usuń.

## <a name="seed-the-database"></a>Wypełnianie bazy danych

`EnsureCreated`Metoda tworzy pustą bazę danych. Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.

Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kod sprawdza, czy w bazie danych istnieją uczniowie. Jeśli nie ma uczniów, dodaje dane testowe do bazy danych. Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.

* W *program.cs*Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .

---

* Uruchom ponownie aplikację.

* Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.

## <a name="view-the-database"></a>Wyświetlanie bazy danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.
* W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**. Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.
* Rozwiń węzeł **tabele** .
* Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.
* Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne. Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.

---

## <a name="asynchronous-code"></a>Kod asynchroniczny

Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.

Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy. W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań. W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.

Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania. W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.

W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async`Słowo kluczowe instruuje kompilator, aby:
  * Generuj wywołania zwrotne dla części treści metody.
  * Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.
* `Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.
* `await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części. Pierwsza część jest zakończona operacją uruchomioną asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.
* `ToListAsync`jest asynchroniczną wersją `ToList` metody rozszerzenia.

Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:

* Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` . Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.
* Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Następny samouczek](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji ASP.NET Core Razor stronach przy użyciu programu Entity Framework (EF) Core.

Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University. Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora. Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają sposób tworzenia przykładowej aplikacji firmy Contoso University.

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instrukcje pobierania](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Znajomość [ Razor stron](xref:razor-pages/index). Nowi programiści powinni zakończyć pracę [z Razor stronami](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Aplikacja sieci Web firmy Contoso University

Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.

Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów. Poniżej przedstawiono kilka ekranów utworzonych w samouczku.

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

Styl interfejsu użytkownika tej witryny jest zbliżony do zawartości wygenerowanej przez wbudowane szablony. Fokus samouczka znajduje się na EF Core ze Razor stronami, a nie interfejsem użytkownika.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Tworzenie Razor aplikacji sieci Web ContosoUniversity Pages

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.
* Utwórz nową aplikację sieci Web ASP.NET Core. Nazwij projekt **ContosoUniversity**. Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.
* Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.

Aby poznać obrazy powyższych kroków, zobacz [Tworzenie Razor aplikacji sieci Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Uruchom aplikację.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Konfigurowanie stylu witryny

Kilka zmian powoduje skonfigurowanie menu witryny, układu i strony głównej. Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:

* Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso". Istnieją trzy wystąpienia.

* Dodaj pozycje menu dla **studentów**, **kursów**, **instruktorów**i **działów**, a następnie usuń wpis menu **kontakt** .

Zmiany są wyróżnione. (Wszystkie znaczniki *nie* są wyświetlane).

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Tworzenie modelu danych

Utwórz klasy jednostek dla aplikacji firmy Contoso University. Zacznij od następujących trzech jednostek:

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

Istnieje relacja jeden do wielu między elementami `Student` i `Enrollment` . Istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` . Student może zarejestrować się w dowolnej liczbie kursów. Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.

W poniższych sekcjach zostanie utworzona Klasa dla każdej z tych jednostek.

### <a name="the-student-entity"></a>Jednostka ucznia

![Diagram jednostek uczniów](intro/_static/student-entity.png)

Utwórz folder *models* . W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy. Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy. W `classnameID` , `classname` jest nazwą klasy. Alternatywny automatycznie rozpoznany klucz podstawowy znajduje się `StudentID` w poprzednim przykładzie.

`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships). Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą jednostką. W tym przypadku `Enrollments` Właściwość a `Student entity` zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym `Student` . Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie `Enrollment` jednostki. Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w `StudentID` kolumnie. Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w `Enrollment` tabeli. `Enrollment`Tabela ma dwa wiersze z `StudentID` = 1. `StudentID`jest kluczem obcym w `Enrollment` tabeli, który określa student w `Student` tabeli.

Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, na przykład `ICollection<T>` . `ICollection<T>`można określić typ, taki jak `List<T>` lub `HashSet<T>` . Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy `HashSet<T>` kolekcję. Właściwości nawigacji, które przechowują wiele jednostek, pochodzą z relacji "wiele do wielu" i "jeden do wielu".

### <a name="the-enrollment-entity"></a>Jednostka rejestracji

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Właściwość jest kluczem podstawowym. Ta jednostka używa `classnameID` wzorca zamiast `ID` `Student` obiektu. Zazwyczaj deweloperzy wybierają jeden wzorzec i używają go w całym modelu danych. W późniejszym samouczku, użycie identyfikatora bez ClassName jest pokazane, aby ułatwić implementację dziedziczenia w modelu danych.

`Grade`Właściwość jest `enum` . Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null. Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.

`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` . `Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę. `Student`Jednostka różni się od `Student.Enrollments` właściwości nawigacji, która zawiera wiele `Enrollment` jednostek.

`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` . `Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.

EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` . Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` . Właściwości klucza obcego mogą być również nazwane `<primary key property name>` . Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .

### <a name="the-course-entity"></a>Jednostka kursu

![Diagram jednostek kursu](intro/_static/course-entity.png)

W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Właściwość jest właściwością nawigacji. `Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.

Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.

## <a name="scaffold-the-student-model"></a>Tworzenie szkieletu modelu ucznia

W tej sekcji model ucznia jest szkieletem. Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu ucznia.

* Skompiluj projekt.
* Utwórz folder *strony/uczniowie* .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.
* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję ** Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.

Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :

* Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.
* W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.
* Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**
* Wybierz pozycję **Dodaj**.

![Okno dialogowe CRUD](intro/_static/s1.png)

Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenia, aby uzyskać szkielet modelu ucznia.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Proces szkieletu utworzył i zmienił następujące pliki:

### <a name="files-created"></a>Utworzone pliki

* *Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.
* *Data/SchoolContext. cs*

### <a name="file-updates"></a>Aktualizacje plików

* *Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.
* *appsettings.json* : dodano parametry połączenia używane do nawiązywania połączenia z lokalną bazą danych.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji. Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.

Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.

Zapoznaj się z `ConfigureServices` metodą w *Startup.cs*. Podświetlony wiersz został dodany przez program do tworzenia szkieletu:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.

## <a name="update-main"></a>Aktualizuj główne

W *program.cs*Zmień metodę, `Main` Aby wykonać następujące czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Usuń kontekst, gdy `EnsureCreated` Metoda zostanie zakończona.

Poniższy kod przedstawia zaktualizowany plik *program.cs* .

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`zapewnia, że baza danych dla kontekstu istnieje. Jeśli istnieje, nie zostanie podjęta żadna akcja. Jeśli nie istnieje, zostanie utworzona baza danych i jej wszystkie schematy. `EnsureCreated`Program nie korzysta z migracji w celu utworzenia bazy danych. Baza danych utworzona za pomocą programu `EnsureCreated` nie może zostać zaktualizowana później przy użyciu migracji.

`EnsureCreated`jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:

* Usuń bazę danych.
* Zmień schemat bazy danych (na przykład Dodaj `EmailAddress` pole).
* Uruchom aplikację.
* `EnsureCreated`tworzy bazę danych z `EmailAddress` kolumną.

`EnsureCreated`jest wygodnie wczesny podczas opracowywania, gdy schemat jest szybko zmieniany. W dalszej części tego samouczka baza danych została usunięta, a migracja jest używana.

### <a name="test-the-app"></a>Testowanie aplikacji

Uruchom aplikację i zaakceptuj zasady dotyczące plików cookie. Ta aplikacja nie przechowuje informacji osobistych. Informacje o zasadach dotyczących plików cookie można znaleźć na stronie [pomocy technicznej ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).

* Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.
* Przetestuj linki Edytuj, Szczegóły i Usuń.

## <a name="examine-the-schoolcontext-db-context"></a>Sprawdzanie kontekstu bazy danych SchoolContext DB

Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych. Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych. W tym projekcie Klasa ma nazwę `SchoolContext` .

Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek. W EF Core terminologii:

* Zestaw jednostek zwykle odpowiada tabeli bazy danych.
* Jednostka odnosi się do wiersza w tabeli.

`DbSet<Enrollment>`i można `DbSet<Course>` je pominąć. EF Core obejmuje te niejawnie `Student` , ponieważ jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki. W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext` .

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja. Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Dodawanie kodu w celu zainicjowania bazy danych z danymi testowymi

EF Core tworzy pustą bazę danych. W tej sekcji `Initialize` Metoda jest zapisywana w celu wypełnienia jej danymi testowymi.

W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw ( `namespace ContosoUniversity.Models` ), a nie `Data` . `Models`jest spójny z kodem generowanym przez program rusztowaer. Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Kod sprawdza, czy w bazie danych istnieją uczniowie. Jeśli w bazie danych nie ma studentów, baza danych zostanie zainicjowana z danymi testowymi. Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.

`EnsureCreated`Metoda automatycznie tworzy bazę danych dla kontekstu bazy danych. Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikacji bazy danych.

W *program.cs*Zmień metodę, `Main` Aby wywołać `Initialize` :

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .

---

## <a name="view-the-db"></a>Wyświetlanie bazy danych

Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID. W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}". Identyfikator GUID będzie różny dla każdego użytkownika.
Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.
W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.

Rozwiń węzeł **tabele** .

Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.

## <a name="asynchronous-code"></a>Kod asynchroniczny

Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.

Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki. W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione. W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy. W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań. W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.

Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania. W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.

W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async`Słowo kluczowe instruuje kompilator, aby:
  * Generuj wywołania zwrotne dla części treści metody.
  * Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany. Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Niejawny typ zwracany `Task` reprezentuje bieżącą liczbę zadań.
* `await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części. Pierwsza część jest zakończona operacją uruchomioną asynchronicznie. Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.
* `ToListAsync`jest asynchroniczną wersją `ToList` metody rozszerzenia.

Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:

* Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie. Obejmuje, `ToListAsync` ,, `SingleOrDefaultAsync` `FirstOrDefaultAsync` i `SaveChangesAsync` . Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.
* Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.

Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).

W następnym samouczku są badane podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie).



## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Dalej](xref:data/ef-rp/crud)

::: moniker-end
