---
title: Dodawanie modelu do ASP.NET aplikacji Core MVC
author: rick-anderson
description: Dodaj model do prostej aplikacji ASP.NET Core.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434515"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>Dodawanie modelu do ASP.NET aplikacji Core MVC

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Tom [Dykstra](https://github.com/tdykstra)

W tej sekcji należy dodać klasy do zarządzania filmami w bazie danych. Zajęcia te będą częścią aplikacji **M**VC "**M**odel".

Te klasy są używane z [entity framework core](/ef/core) (EF Core) do pracy z bazą danych. EF Core jest struktura mapowania obiektowego (ORM), która upraszcza kod dostępu do danych, który należy napisać.

Klasy modelu, które tworzysz są znane jako klasy POCO (z **P**lain **O**ld **C**LR **O**bjects), ponieważ nie mają żadnej zależności od EF Core. Po prostu definiują właściwości danych, które będą przechowywane w bazie danych.

W tym samouczku najpierw napisz klasy modelu, a EF Core tworzy bazę danych.

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Dodawanie klasy modelu danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Kliknij prawym przyciskiem myszy folder *Modele* > **Dodaj** > **klasę**. Nazwij plik *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Dodaj plik o nazwie *Movie.cs* do folderu *Modele.*

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Kliknij prawym przyciskiem myszy folder *Modele* > **Dodaj** > **nową klasę** > **pustą klasę**. Nazwij plik *Movie.cs*.

---

Zaktualizuj plik *Movie.cs* o następujący kod:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

Klasa `Movie` zawiera `Id` pole, które jest wymagane przez bazę danych dla klucza podstawowego.

Atrybut <xref:System.ComponentModel.DataAnnotations.DataType> na `ReleaseDate` określa typ danych (`Date`). Z tym atrybutem:

* Użytkownik nie musi wprowadzać informacji o godzinie w polu daty.
* Wyświetlana jest tylko data, a nie informacje o czasie.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w późniejszym samouczku.

## <a name="add-nuget-packages"></a>Dodawanie pakietów NuGet

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Z menu **Narzędzia** wybierz pozycję Konsola Menedżera **pakietów** > Programu NuGet Package **Manager** (PMC).

![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

W pmc uruchom następujące polecenie:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Poprzednie polecenie dodaje ef core dostawcy programu SQL Server. Pakiet dostawcy instaluje pakiet EF Core jako zależność. Dodatkowe pakiety są instalowane automatycznie w kroku szkieletu w dalszej części samouczka.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Z menu **Projekt** wybierz polecenie **Zarządzaj pakietami NuGet**.

W polu **Wyszukiwanie** w prawym `Microsoft.EntityFrameworkCore.SQLite` górnym rogu wprowadź i naciśnij klawisz **Return,** aby wyszukać. Wybierz pasujący pakiet NuGet i naciśnij przycisk **Dodaj pakiet.**

![Dodaj pakiet NuGet Core framework framework entity framework](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Zostanie wyświetlone okno dialogowe Wybierz `MvcMovie` **projekty** z wybranym projektem. Naciśnij przycisk **Ok.**

Zostanie wyświetlone okno dialogowe **Akceptacja licencji.** Przejrzyj licencje zgodnie z potrzebami, a następnie kliknij przycisk **Zaakceptuj.**

Powtórz powyższe kroki, aby zainstalować następujące pakiety NuGet:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Tworzenie klasy kontekstu bazy danych

Klasa kontekstu bazy danych jest potrzebna do koordynowania funkcji EF `Movie` Core (Tworzenie, Odczyt, Aktualizacja, Usuwanie) dla modelu. Kontekst bazy danych jest uzyskiwane z [microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i określa jednostki do uwzględnienia w modelu danych.

Utwórz folder *Dane.*

Dodaj plik *Data/MvcMovieContext.cs* z następującym kodem: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych. Jednostka odpowiada wierszowi w tabeli.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

ASP.NET Core jest zbudowany z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst ef core db) muszą być zarejestrowane w DI podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka. W tej sekcji można zarejestrować kontekst bazy danych z kontenerem DI.

Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Dodaj następujący wyróżniony `Startup.ConfigureServices`kod w :

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Dodawanie ciągu połączenia bazy danych

Dodaj parametry połączenia do pliku *appsettings.json:*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Skompiluj projekt jako sprawdzanie błędów kompilatora.

## <a name="scaffold-movie-pages"></a>Strony filmów rusztowania

Narzędzie rusztowania służy do tworzenia stron Tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Kontrolery* **> Dodaj > nowy element szkieletu**.

![widok powyższego kroku](adding-model/_static/add_controller21.png)

W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC z widokami, używając programu Entity Framework > Add**.

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

Wykonaj okno dialogowe **Dodawanie kontrolera:**

* **Klasa modelu:** *Film (MvcMovie.Models)*
* **Klasa kontekstu danych:** *MvcMovieContext (MvcMovie.Data)*

![Dodawanie kontekstu danych](adding-model/_static/dc3.png)

* **Widoki:** Sprawdzanie wartości domyślnej każdej opcji
* **Nazwa kontrolera:** Zachowaj domyślny *kontroler MoviesController*
* Wybierz **pozycję Dodaj**

Visual Studio tworzy:

* Kontroler filmów (*Kontrolery/MoviesController.cs*)
* Pliki widoku razor dla stron Tworzenie, Usuwanie, Szczegóły, Edycja i Indeks *(Widoki/Filmy/\*.cshtml)*

Automatyczne tworzenie tych plików jest znany jako *rusztowania*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*

* W systemie Linux wyeksportuj ścieżkę narzędzia szkieletu:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*

* Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Nie można jeszcze użyć szkieletu strony, ponieważ baza danych nie istnieje. Jeśli uruchomisz aplikację i klikniesz łącze **Movie App,** zostanie wyświetlony *komunikat o błędzie Nie można otworzyć bazy danych* lub brak takiej *tabeli:* Komunikat o błędzie filmu.

<a name="migration"></a>

## <a name="initial-migration"></a>Migracja początkowa

Użyj funkcji [Migracje rdzenia](xref:data/ef-mvc/migrations) EF, aby utworzyć bazę danych. Migracje to zestaw narzędzi, które umożliwiają tworzenie i aktualizowanie bazy danych w celu dopasowania do modelu danych.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Z menu **Narzędzia** wybierz pozycję Konsola Menedżera **pakietów** > Programu NuGet Package **Manager** (PMC).

W pmc wprowadź następujące polecenia:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Generuje plik *migracji/{timestamp}_InitialCreate.cs.* Argument `InitialCreate` jest nazwą migracji. Można użyć dowolnej nazwy, ale zgodnie z konwencją jest zaznaczona nazwa opisująca migrację. Ponieważ jest to pierwsza migracja, wygenerowana klasa zawiera kod do utworzenia schematu bazy danych. Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie.

* `Update-Database`: Aktualizuje bazę danych do najnowszej migracji, którą utworzyło poprzednie polecenie. To polecenie `Up` uruchamia metodę w pliku *Migrations/{time-stamp}_InitialCreate.cs,* który tworzy bazę danych.

  Polecenie aktualizacji bazy danych generuje następujące ostrzeżenie: 

  > Nie określono typu dla kolumny dziesiętnej "Cena" dla typu encji "Film". Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".

  Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Uruchom następujące polecenia .NET Core CLI:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Generuje plik *migracji/{timestamp}_InitialCreate.cs.* Argument `InitialCreate` jest nazwą migracji. Można użyć dowolnej nazwy, ale zgodnie z konwencją jest zaznaczona nazwa opisująca migrację. Ponieważ jest to pierwsza migracja, wygenerowana klasa zawiera kod do utworzenia schematu bazy danych. Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie (w pliku *Data/MvcMovieContext.cs).*

* `ef database update`: Aktualizuje bazę danych do najnowszej migracji, którą utworzyło poprzednie polecenie. To polecenie `Up` uruchamia metodę w pliku *Migrations/{time-stamp}_InitialCreate.cs,* który tworzy bazę danych.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>Klasa InitialCreate

Sprawdź plik *migracji/{sygnatura czasowa}_InitialCreate.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Metoda `Up` tworzy tabelę Movie `Id` i konfiguruje jako klucz podstawowy. Metoda `Down` przywraca zmiany schematu wprowadzone `Up` przez migrację.

<a name="test"></a>

## <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację i kliknij łącze **Movie App.**

  Jeśli zostanie otrzymasz wyjątek podobny do jednego z następujących:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Prawdopodobnie przegapiłeś [krok migracji](#migration).

* Przetestuj stronę **Utwórz.** Wprowadzanie i przesyłanie danych.

  > [!NOTE]
  > Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych. Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testowanie stron **Edycja,** **Szczegóły**i **Usuń.**

## <a name="dependency-injection-in-the-controller"></a>Wstrzyknięcie zależności w sterowniku

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Otwórz plik *Controllers/MoviesController.cs* i sprawdź konstruktor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) `MvcMovieContext`aby wstrzyknąć kontekst bazy danych ( ) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) `MvcMovieContext`aby wstrzyknąć kontekst bazy danych ( ) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Użyj SQLite do tworzenia, SQL Server dla produkcji

Po wybraniu SQLite, szablon wygenerowany kod jest gotowy do rozwoju. Poniższy kod pokazuje, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> jak wstrzyknąć do uruchamiania. `IWebHostEnvironment`jest wstrzykiwany, więc `ConfigureServices` można użyć SQLite w rozwoju i SQL Server w produkcji.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Silnie wpisane modele @model i słowo kluczowe

Wcześniej w tym samouczku, widziałeś, jak kontroler może `ViewData` przekazać dane lub obiekty do widoku przy użyciu słownika. Słownik `ViewData` jest obiektem dynamicznym, który zapewnia wygodny późny sposób przekazywania informacji do widoku.

MVC zapewnia również możliwość przekazywania silnie wpisane obiekty modelu do widoku. To silnie typizowane podejście umożliwia sprawdzanie kodu kompilacji. Mechanizm rusztowania używane to podejście (oznacza to, przekazując `MoviesController` model silnie typizowane) z klasy i widoków.

Sprawdź wygenerowaną `Details` metodę w pliku *Controllers/MoviesController.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Parametr `id` jest zazwyczaj przekazywany jako dane trasy. Na `https://localhost:5001/movies/details/1` przykład zestawy:

* Kontroler do `movies` kontrolera (pierwszy segment adresu URL).
* Akcja do `details` (drugi segment adresu URL).
* Identyfikator do 1 (ostatni segment adresu URL).

Można również przekazać `id` w ciągu zapytania w następujący sposób:

`https://localhost:5001/movies/details?id=1`

Parametr `id` jest zdefiniowany jako typ`int?` [nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( ) w przypadku, gdy wartość identyfikatora nie jest podana.

[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przekazywane `FirstOrDefaultAsync` do wybierania jednostek filmu, które pasują do danych trasy lub wartości ciągu zapytania.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest `Details` przekazywane do widoku:

```csharp
return View(movie);
```

Sprawdź zawartość pliku *Views/Movies/Details.cshtml:*

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Instrukcja `@model` w górnej części pliku widoku określa typ obiektu, który oczekuje widoku. Podczas tworzenia kontrolera filmu `@model` uwzględniono następującą instrukcję:

```cshtml
@model MvcMovie.Models.Movie
```

Ta `@model` dyrektywa umożliwia dostęp do filmu, który kontroler przeszedł do widoku. Obiekt `Model` jest silnie wpisywany. Na przykład w widoku *Details.cshtml* kod przekazuje każde `DisplayNameFor` pole `DisplayFor` filmu do pomocników `Model` html z silnie wpisanym obiektem. I `Create` `Edit` metody i widoki `Movie` również przekazać obiekt modelu.

Sprawdź *Index.cshtml* widoku `Index` i metody w kontrolerze filmy. Zwróć uwagę, jak `List` kod tworzy `View` obiekt, gdy wywołuje metodę. Kod przekazuje `Movies` tę listę `Index` z metody akcji do widoku:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Podczas tworzenia kontrolera filmów, rusztowania `@model` zawierały następującą instrukcję w górnej części pliku *Index.cshtml:*

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Dyrektywa `@model` umożliwia dostęp do listy filmów, które kontroler przeszedł do `Model` widoku przy użyciu obiektu, który jest silnie wpisane. Na przykład w widoku *Index.cshtml* kod pętli przez filmy `foreach` z instrukcją `Model` nad silnie wpisany obiektu:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Ponieważ `Model` obiekt jest silnie wpisany `IEnumerable<Movie>` (jako obiekt), każdy element `Movie`w pętli jest wpisywany jako . Wśród innych korzyści, oznacza to, że otrzymasz skompilować czas sprawdzania kodu.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro)
* [Globalizacja i lokalizacja](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Poprzednia dodanie widoku](adding-view.md)
> [następnej pracy z sql](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Dodawanie klasy modelu danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Kliknij prawym przyciskiem myszy folder *Modele* > **Dodaj** > **klasę**. Nazwij klasę **Movie**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Rusztowanie modelu filmu

W tej sekcji model filmu jest szkieletu. Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Kontrolery* **> Dodaj > nowy element szkieletu**.

![widok powyższego kroku](adding-model/_static/add_controller21.png)

W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC z widokami, używając programu Entity Framework > Add**.

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

Wykonaj okno dialogowe **Dodawanie kontrolera:**

* **Klasa modelu:** *Film (MvcMovie.Models)*
* **Klasa kontekstu danych:** Wybierz **+** ikonę i dodaj domyślny **MvcMovie.Models.MvcMovieContext**

![Dodawanie kontekstu danych](adding-model/_static/dc.png)

* **Widoki:** Sprawdzanie wartości domyślnej każdej opcji
* **Nazwa kontrolera:** Zachowaj domyślny *kontroler MoviesController*
* Wybierz **pozycję Dodaj**

![Okno dialogowe Dodawanie kontrolera](adding-model/_static/add_controller2.png)

Visual Studio tworzy:

* [Klasa kontekstu bazy danych](xref:data/ef-mvc/intro#create-the-database-context) core programu Entity Framework *(Data/MvcMovieContext.cs)*
* Kontroler filmów (*Kontrolery/MoviesController.cs*)
* Pliki widoku razor dla stron Tworzenie, Usuwanie, Szczegóły, Edycja i Indeks *(Widoki/Filmy/\*.cshtml)*

Automatyczne tworzenie kontekstu bazy danych i [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (tworzenie, odczytywanie, aktualizowanie i usuwanie) metody działania i widoki jest znany jako *rusztowania*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*
* Zainstaluj narzędzie rusztowania:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* W systemie Linux wyeksportuj ścieżkę narzędzia szkieletu:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Uruchom następujące polecenie:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*
* Zainstaluj narzędzie rusztowania:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Uruchom następujące polecenie:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Jeśli uruchomisz aplikację i klikniesz łącze **Mvc Movie,** zostanie wyświetlony błąd podobny do następującego:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Należy utworzyć bazę danych i użyć funkcji [migracji rdzenia](xref:data/ef-mvc/migrations) EF, aby to zrobić. Migracje umożliwia utworzenie bazy danych, która pasuje do modelu danych i zaktualizować schemat bazy danych po zmianie modelu danych.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migracja początkowa

W tej sekcji wykonywane są następujące zadania:

* Dodaj migrację początkową.
* Zaktualizuj bazę danych za pomocą migracji początkowej.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Z menu **Narzędzia** wybierz pozycję Konsola Menedżera **pakietów** > Programu NuGet Package **Manager** (PMC).

   ![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. W pmc wprowadź następujące polecenia:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Polecenie `Add-Migration` generuje kod do tworzenia początkowego schematu bazy danych.

   Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie. Argument `Initial` jest nazwą migracji. Można użyć dowolnej nazwy, ale zgodnie z konwencją używana jest nazwa opisująca migrację. Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.

   Polecenie `Update-Database` uruchamia `Up` metodę w pliku *Migrations/{time-stamp}_InitialCreate.cs,* który tworzy bazę danych.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Polecenie `ef migrations add InitialCreate` generuje kod do tworzenia początkowego schematu bazy danych.

Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie (w pliku *Data/MvcMovieContext.cs).* Argument `InitialCreate` jest nazwą migracji. Można użyć dowolnej nazwy, ale zgodnie z konwencją jest zaznaczona nazwa opisująca migrację.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Badanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest zbudowany z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst ef core db) są rejestrowane w DI podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Narzędzie rusztowania automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze DI.

Sprawdź następującą `Startup.ConfigureServices` metodę. Podświetlona linia została dodana przez rusztowanie:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

Współrzędne `MvcMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu. Kontekst danych`MvcMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych. Jednostka odpowiada wierszowi w tabeli.

Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Utworzono kontekst bazy danych i zarejestrowano go w kontenerze DI.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).

Jeśli zostanie otrzymasz wyjątek bazy danych podobny do następującego:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Pominięto [krok migracji](#pmc).

* Przetestuj łącze **Utwórz.** Wprowadzanie i przesyłanie danych.

  > [!NOTE]
  > Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych. Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

Sprawdź `Startup` klasę:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Powyższy wyróżniony kod pokazuje kontekst bazy danych filmu dodawany do [kontenera iniekcji zależności:](xref:fundamentals/dependency-injection)

* `services.AddDbContext<MvcMovieContext>(options =>`określa bazę danych do użycia i parametry połączenia.
* `=>`jest [operatorem lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Otwórz plik *Controllers/MoviesController.cs* i sprawdź konstruktor:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) `MvcMovieContext`aby wstrzyknąć kontekst bazy danych ( ) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Silnie wpisane modele @model i słowo kluczowe

Wcześniej w tym samouczku, widziałeś, jak kontroler może `ViewData` przekazać dane lub obiekty do widoku przy użyciu słownika. Słownik `ViewData` jest obiektem dynamicznym, który zapewnia wygodny późny sposób przekazywania informacji do widoku.

MVC zapewnia również możliwość przekazywania silnie wpisane obiekty modelu do widoku. To silnie typizowane podejście umożliwia lepsze sprawdzanie czasu kompilacji kodu. Mechanizm szkieletu używane to podejście (oznacza to, przekazując model `MoviesController` silnie typizowane) z klasy i widoków podczas tworzenia metod i widoków.

Sprawdź wygenerowaną `Details` metodę w pliku *Controllers/MoviesController.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Parametr `id` jest zazwyczaj przekazywany jako dane trasy. Na `https://localhost:5001/movies/details/1` przykład zestawy:

* Kontroler do `movies` kontrolera (pierwszy segment adresu URL).
* Akcja do `details` (drugi segment adresu URL).
* Identyfikator do 1 (ostatni segment adresu URL).

Można również przekazać `id` w ciągu zapytania w następujący sposób:

`https://localhost:5001/movies/details?id=1`

Parametr `id` jest zdefiniowany jako typ`int?` [nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( ) w przypadku, gdy wartość identyfikatora nie jest podana.

[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przekazywane `FirstOrDefaultAsync` do wybierania jednostek filmu, które pasują do danych trasy lub wartości ciągu zapytania.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest `Details` przekazywane do widoku:

```csharp
return View(movie);
   ```

Sprawdź zawartość pliku *Views/Movies/Details.cshtml:*

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Dołączając `@model` instrukcję w górnej części pliku widoku, można określić typ obiektu, którego oczekuje widok. Podczas tworzenia kontrolera filmów `@model` następująca instrukcja została automatycznie uwzględniona w górnej części pliku *Details.cshtml:*

```cshtml
@model MvcMovie.Models.Movie
```

Ta `@model` dyrektywa umożliwia dostęp do filmu, który kontroler przeszedł `Model` do widoku przy użyciu obiektu, który jest silnie wpisane. Na przykład w widoku *Details.cshtml* kod przekazuje każde `DisplayNameFor` pole `DisplayFor` filmu do pomocników `Model` html z silnie wpisanym obiektem. I `Create` `Edit` metody i widoki `Movie` również przekazać obiekt modelu.

Sprawdź *Index.cshtml* widoku `Index` i metody w kontrolerze filmy. Zwróć uwagę, jak `List` kod tworzy `View` obiekt, gdy wywołuje metodę. Kod przekazuje `Movies` tę listę `Index` z metody akcji do widoku:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Podczas tworzenia kontrolera filmów, rusztowania automatycznie `@model` zawarte następujące instrukcje w górnej części pliku *Index.cshtml:*

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Dyrektywa `@model` umożliwia dostęp do listy filmów, które kontroler przeszedł do `Model` widoku przy użyciu obiektu, który jest silnie wpisane. Na przykład w widoku *Index.cshtml* kod pętli przez filmy `foreach` z instrukcją `Model` nad silnie wpisany obiektu:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Ponieważ `Model` obiekt jest silnie wpisany `IEnumerable<Movie>` (jako obiekt), każdy element `Movie`w pętli jest wpisywany jako . Wśród innych korzyści, oznacza to, że można uzyskać skompilować czas sprawdzania kodu:

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro)
* [Globalizacja i lokalizacja](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Poprzednia Dodawanie widoku](adding-view.md)
> [następnej pracy z bazą danych](working-with-sql.md)

::: moniker-end
