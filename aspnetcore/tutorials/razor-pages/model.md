---
title: Część 2, Dodawanie modelu
author: rick-anderson
description: Część 2 serii samouczków na Razor stronach. W tej sekcji dodawane są klasy modelu.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 3677cd6fe5c2ff901a17c9dccdc749d8eb2709f2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588669"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

W tej sekcji są dodawane klasy do zarządzania filmami w bazie danych. Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych. EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych. Najpierw należy napisać klasy modelu, a EF Core utworzyć bazę danych.

Klasy modelu są znane jako klasy POCO (od "**P** Lain-**O** LD **k** LR **O** biekty"), ponieważ nie mają zależności od EF Core. Definiują właściwości danych przechowywanych w bazie danych programu.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Dodawanie modelu danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt *Razor PagesMovie* > **Dodaj**  >  **Nowy folder**. Nazwij *modele* folderów.
1. Kliknij prawym przyciskiem myszy folder *modele* . Wybierz pozycję **Dodaj**  >  **klasę**. Nazwij *film* klasy.
1. Dodaj następujące właściwości do `Movie` klasy:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dodaj folder o nazwie *models*.
1. Dodaj klasę do folderu *models* o nazwie *Movie.cs*.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Dodawanie pakietów NuGet i narzędzi EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Dodaj klasę kontekstu bazy danych

1. W projekcie *Razor PagesMovie* Utwórz folder o nazwie *dane*.
1. W folderze *dane* Dodaj plik o nazwie *Razor PagesMovieContext.cs* z następującym kodem:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli. Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodaj parametry połączenia z bazą danych

Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

1. Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.
1. Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik.**...
1. W oknie dialogowym **nowy plik** :
   1. W lewym okienku wybierz pozycję **Ogólne** .
   1. Wybierz **pustą klasę** w środkowym okienku.
   1. Nazwij **film** klasy i wybierz pozycję **Nowy**.

1. Dodaj następujące właściwości do `Movie` klasy:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

---

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.

## <a name="scaffold-the-movie-model"></a>Tworzenie szkieletu modelu filmu

W tej sekcji model filmu jest szkieletem. Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Utwórz folder *stron/filmów* :
   1. Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.
   1. Nazwij foldery *filmów*.

1. Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.

   ![Obraz z poprzednich instrukcji.](model/_static/5/sca.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

1. Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :
   1. Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.
   1. W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).
      1. W oknie dialogowym **Dodawanie kontekstu danych** `RazorPagesMovie.Data.RazorPagesMovieContext` jest generowana nazwa klasy.
   1. Wybierz pozycję **Dodaj**.

   ![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otwórz powłokę poleceń w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .

* **Dla systemu Windows**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcji generatora kodu.

| Opcja               | Opis|
| ----------------- | ------------ |
| `-m`  | Nazwa modelu. |
| `-dc`  | `DbContext`Klasa, która ma zostać użyta. |
| `-udl` | Użyj układu domyślnego. |
| `-outDir` | Ścieżka względna folderu wyjściowego do tworzenia widoków. |
| `--referenceScriptLibraries` | Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron |

Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Używanie oprogramowania SQLite do programowania, SQL Server do produkcji

Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania. Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` . `IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Utwórz folder *stron/filmów* :
   1. Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.
   1. Nazwij foldery *filmów*.

1. Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..

   ![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

1. W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

1. Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :
   1. W **klasie DbContext, aby użyć:** Row, nadaj jej nazwę Class `RazorPagesMovie.Data.RazorPagesMovieContext` .
   1. Wybierz pozycję **Zakończ**.

   ![Obraz z poprzednich instrukcji.](model/_static/5/arpMac.png)

*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Używanie oprogramowania SQLite do programowania, SQL Server do produkcji

Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania. Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` . `IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Utworzone pliki

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .
* *Dane/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Po aktualizacji

* *Startup.cs*

Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Proces tworzenia szkieletu tworzy następujące pliki:

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .

Utworzone pliki zostały wyjaśnione w następnej sekcji.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .
* *Dane/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Po aktualizacji

* *Startup.cs*

Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a>Utwórz początkowy schemat bazy danych przy użyciu funkcji migracji EF

Funkcja migracji w Entity Framework Core zapewnia sposób:

* Utwórz początkowy schemat bazy danych.
* Uaktualnij przyrostowo schemat bazy danych, aby zachować synchronizację z modelem danych aplikacji.  Istniejące dane w bazie danych są zachowywane.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W tej sekcji okno **konsoli Menedżera pakietów** (PMC) służy do:

* Dodawanie początkowej migracji.
* Zaktualizuj bazę danych przy użyciu początkowej migracji.

1. W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.

   ![Menu PMC](model/_static/5/pmc.png)

1. W obszarze PMC wprowadź następujące polecenia:

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ". Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".

Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.

`migrations`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych. Schemat jest oparty na modelu określonym w `DbContext` . `InitialCreate`Argument jest używany do nazwy migracji. Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.

`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane. W takim przypadku program `update` uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection). Usługi, takie jak kontekst bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji. Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.

Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.

Zapoznaj się z tą `Startup.ConfigureServices` metodą. Podświetlony wiersz został dodany przez program do tworzenia szkieletu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu. Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) dla zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych. Jednostka odnosi się do wiersza w tabeli.

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) . W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Zapoznaj się z tą `Up` metodą.

---

<a name="test"></a>

## <a name="test-the-app"></a>Testowanie aplikacji

1. Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).

   Jeśli pojawi się następujący komunikat o błędzie:

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   Pominięto [krok migracji](#pmc).

1. Przetestuj link **tworzenia** .

   ![Tworzenie strony](model/_static/conan5.png)

   > [!NOTE]
   > W polu nie można wprowadzać przecinków dziesiętnych `Price` . Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.

1. Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.

## <a name="additional-resources"></a>Dodatkowe zasoby

> [!div class="step-by-step"]
> [Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

W tej sekcji dodano klasy do zarządzania filmami. Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych. EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.

Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core. Definiują właściwości danych przechowywanych w bazie danych programu.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Dodawanie modelu danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**. Nazwij *modele* folderów.

Kliknij prawym przyciskiem myszy folder *modele* . Wybierz pozycję **Dodaj**  >  **klasę**. Nazwij **film** klasy.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodaj folder o nazwie *models*.
* Dodaj klasę do folderu *models* o nazwie *Movie.cs*.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Dodawanie pakietów NuGet i narzędzi EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Dodaj klasę kontekstu bazy danych

* W projekcie *Razor PagesMovie* Utwórz nowy folder o nazwie *Data*.
* Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli. Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodaj parametry połączenia z bazą danych

Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W **oknie narzędzia rozwiązania** kliknij projekt **Razor PagesMovie** , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.
* Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik.**...
* W oknie dialogowym **nowy plik** :

  * W lewym okienku wybierz pozycję **Ogólne** .
  * Wybierz **pustą klasę** w środkowym okienku.
  * Nazwij **film** klasy i wybierz pozycję **Nowy**.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

---

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.

## <a name="scaffold-the-movie-model"></a>Tworzenie szkieletu modelu filmu

W tej sekcji model filmu jest szkieletem. Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utwórz folder *stron/filmów* :

* Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.
* Nazwij foldery *filmów*.

Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :

* Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.
* W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę z Razor PagesMovie.**Modele**. Razor PagesMovieContext do Razor PagesMovie.**Dane**. Razor PagesMovieContext. [Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana. Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .

* **Dla systemu Windows**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:

| Opcja               | Opis|
| ----------------- | ------------ |
| `-m`  | Nazwa modelu. |
| `-dc`  | `DbContext`Klasa, która ma zostać użyta. |
| `-udl` | Użyj układu domyślnego. |
| `-outDir` | Ścieżka względna folderu wyjściowego do tworzenia widoków. |
| `--referenceScriptLibraries` | Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron |

Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Używanie oprogramowania SQLite do programowania, SQL Server do produkcji

Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania. Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia. `IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Utwórz folder *stron/filmów* :

* Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.
* Nazwij foldery *filmów*.

Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :

* W menu rozwijanym **Klasa modelu** wybierz lub wpisz **film ( Razor PagesMovie. models)**.
* W wierszu **klasy kontekstu danych** wpisz nazwę nowej klasy, Razor PagesMovie.**Dane**. Razor PagesMovieContext. [Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana. Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.

### <a name="add-ef-tools"></a>Dodawanie narzędzi EF

Uruchom następujące interfejs wiersza polecenia platformy .NET Core polecenie:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Poprzednie polecenie dodaje Entity Framework Core narzędzia dla interfejs wiersza polecenia platformy .NET Core. Aby uzyskać więcej informacji, zobacz [Entity Framework Core Tools Reference-interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Używanie oprogramowania SQLite do programowania, SQL Server do produkcji

Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania. Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia. `IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Utworzone pliki

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .
* *Dane/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Po aktualizacji

* *Startup.cs*

Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .
* *Dane/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Po aktualizacji

* *Startup.cs*

Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Proces tworzenia szkieletu tworzy następujące pliki:

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .

Utworzone pliki zostały wyjaśnione w następnej sekcji.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migracja początkowa

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W tej sekcji konsola Menedżera pakietów (PMC) służy do:

* Dodawanie początkowej migracji.
* Zaktualizuj bazę danych przy użyciu początkowej migracji.

W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

W obszarze PMC wprowadź następujące polecenia:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ". Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".

Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.

Polecenie migrations generuje kod, aby utworzyć początkowy schemat bazy danych. Schemat jest oparty na modelu określonym w `DbContext` . `InitialCreate`Argument jest używany do nazwy migracji. Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.

`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane. W takim przypadku program `update` uruchamia `Up` metodę w pliku  *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection). Usługi, takie jak kontekst kontekstu bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.

Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.

Zapoznaj się z tą `Startup.ConfigureServices` metodą. Podświetlony wiersz został dodany przez program do tworzenia szkieletu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu. Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych. Jednostka odnosi się do wiersza w tabeli.

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) . W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Zapoznaj się z tą `Up` metodą.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).

Jeśli wystąpi błąd:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Pominięto [krok migracji](#pmc).

* Przetestuj link **tworzenia** .

  ![Tworzenie strony](model/_static/conan5.png)

  > [!NOTE]
  > W polu nie można wprowadzać przecinków dziesiętnych `Price` . Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.

* Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.

## <a name="additional-resources"></a>Dodatkowe zasoby

> [!div class="step-by-step"]
> [Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

W tej sekcji są dodawane klasy do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html). Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych programu SQLite. Klasy modelu aplikacji są używane z [Entity Framework Core (Ef Core)](/ef/core) ([dostawca bazy danych programu SQLite EF Core](/ef/core/providers/sqlite)) do pracy z bazą danych. EF Core to struktura obiektów mapowania relacyjnego (ORM), która upraszcza dostęp do danych.

Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core. Definiują właściwości danych przechowywanych w bazie danych programu.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Dodawanie modelu danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**. Nazwij *modele* folderów.

Kliknij prawym przyciskiem myszy folder *modele* . Wybierz pozycję **Dodaj**  >  **klasę**. Nazwij **film** klasy.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodaj folder o nazwie *models*.
* Dodaj klasę do folderu *models* o nazwie *Movie.cs*.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Dodawanie pakietów NuGet i narzędzi EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Dodaj klasę kontekstu bazy danych

W Razor projekcie PagesMovie Utwórz nowy folder o nazwie *Data*. 
Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli. Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodaj parametry połączenia z bazą danych

Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Dodaj wymagane pakiety NuGet

Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Kompiluj projekt jako sprawdzenie pod kątem błędów.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij *modele* folderów.
* Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik**.
* W oknie dialogowym **nowy plik** :

  * W lewym okienku wybierz pozycję **Ogólne** .
  * Wybierz **pustą klasę** w środkowym okienku.
  * Nazwij **film** klasy i wybierz pozycję **Nowy**.

Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `ID`Pole jest wymagane przez bazę danych klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.

---

Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.

## <a name="scaffold-the-movie-model"></a>Tworzenie szkieletu modelu filmu

W tej sekcji model filmu jest szkieletem. Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utwórz folder *stron/filmów* :

* Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.
* Nazwij foldery *filmów*.

Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.
* W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zaakceptuj wygenerowaną nazwę **Razor PagesMovie. models. Razor PagesMovieContext**.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .

* **Dla systemu Windows**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:

| Opcja               | Opis|
| ----------------- | ------------ |
| `-m`  | Nazwa modelu. |
| `-dc`  | `DbContext`Klasa, która ma zostać użyta. |
| `-udl` | Użyj układu domyślnego. |
| `-outDir` | Ścieżka względna folderu wyjściowego do tworzenia widoków. |
| `--referenceScriptLibraries` | Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron |

Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Utwórz folder *stron/filmów* :

* Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.
* Nazwij foldery *filmów*.

Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

W oknie dialogowym **Dodawanie nowej szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :

* Z listy rozwijanej **Klasa modelu** wybierz lub wpisz **film**.
* W wierszu **klasy kontekstu danych** wpisz wybierz **Razor PagesMovieContext** to spowoduje utworzenie nowej klasy kontekstu bazy danych z poprawną przestrzenią nazw. W takim przypadku będzie to **Razor PagesMovie. models. Razor PagesMovieContext**.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.

---

Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:

### <a name="files-created"></a>Utworzone pliki

* *Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .
* *Dane/ Razor PagesMovieContext.cs*

### <a name="file-updated"></a>Zaktualizowano plik

* *Startup.cs*

Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migracja początkowa

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W tej sekcji konsola Menedżera pakietów (PMC) służy do:

* Dodawanie początkowej migracji.
* Zaktualizuj bazę danych przy użyciu początkowej migracji.

W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

W obszarze PMC wprowadź następujące polecenia:

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych. Schemat jest oparty na modelu określonym w `DbContext` , w pliku *Razor PagesMovieContext.cs* . `InitialCreate`Argument jest używany do nazwy migracji. Można użyć dowolnej nazwy, ale według Konwencji Nazwa opisująca migrację jest używana. Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.

`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* . `Up`Metoda tworzy bazę danych.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ". Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".

Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst kontekstu bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji. Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu contextB kontekstu bazy danych jest wyświetlany w dalszej części tego samouczka.

Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.

Zapoznaj się z tą `Startup.ConfigureServices` metodą. Podświetlony wiersz został dodany przez program do tworzenia szkieletu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu. Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych. Jednostka odnosi się do wiersza w tabeli.

Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) . W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Zapoznaj się z tą `Up` metodą.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).

Jeśli wystąpi błąd:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Pominięto [krok migracji](#pmc).

* Przetestuj link **tworzenia** .

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > W polu nie można wprowadzać przecinków dziesiętnych `Price` . Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.

* Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.

## <a name="additional-resources"></a>Dodatkowe zasoby

> [!div class="step-by-step"]
> [Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)

::: moniker-end
