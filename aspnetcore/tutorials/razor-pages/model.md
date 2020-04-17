---
title: Dodawanie modelu do aplikacji Razor Pages w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać klasy do zarządzania filmami w bazie danych przy użyciu entity framework core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488874"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Dodawanie modelu do aplikacji Razor Pages w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

W tej sekcji klasy są dodawane do zarządzania filmami. Klasy modelu aplikacji używają [entity framework core (EF Core)](/ef/core) do pracy z bazą danych. EF Core jest obiektokręgowym maperem (O/RM), który upraszcza dostęp do danych.

Klasy modelu są znane jako klasy POCO (z "zwykłych starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core. Definiują właściwości danych, które są przechowywane w bazie danych.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Dodawanie modelu danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kliknij prawym przyciskiem myszy projekt **RazorPagesMovie** > **Dodaj** > **nowy folder**. Nazwij folder *Models*.

Kliknij prawym przyciskiem myszy folder *Modele.* Wybierz **pozycję Dodaj** > **klasę**. Nazwij klasę **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodawanie folderu o nazwie *Modele*.
* Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W witrynie Solution Pad kliknij prawym przyciskiem myszy projekt **RazorPagesMovie,** a następnie wybierz polecenie **Dodaj** > **nowy folder...**. Nazwij folder *Models*.
* Kliknij prawym przyciskiem myszy folder *Modele,* a następnie wybierz polecenie **Dodaj** > **nowy plik...**.
* W oknie dialogowym **Nowy plik:**

  * Wybierz **pozycję Ogólne** w lewym okienku.
  * Wybierz **pozycję Pusta klasa** w środkowym okienku.
  * Nazwij klasę **Film** i wybierz pozycję **Nowy**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilacji.

## <a name="scaffold-the-movie-model"></a>Rusztowanie modelu filmu

W tej sekcji model filmu jest szkieletu. Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Tworzenie folderu *Strony/Filmy:*

* Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.
* Nadawanie nazwy folderowi *Filmy*

Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:

* W rozwijaniu **klasy Model** wybierz pozycję **Film (RazorPagesMovie.Models).**
* W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zmień wygenerowaną nazwę z RazorPagesMovie. **Modele**. RazorPagesMovieContext do RazorPagesMovie. **Dane**. RazorPagesMovieContext. [Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana. Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*
* Zainstaluj narzędzie rusztowania:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **W systemie Windows**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Tworzenie folderu *Strony/Filmy:*

* Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.
* Nadawanie nazwy folderowi *Filmy*

Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowe rusztowania...**.

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

W oknie dialogowym **Nowe rusztowania** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Next**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:

* W rozwijaniu **klasy Modelu** wybierz lub wpisz **film (RazorPagesMovie.Models).**
* W wierszu **klasy Kontekst danych** wpisz nazwę nowej klasy RazorPagesMovie. **Dane**. RazorPagesMovieContext. [Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana. Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.

### <a name="add-ef-tools"></a>Dodawanie narzędzi EF

Uruchom następujące polecenie .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Poprzednie polecenie dodaje podstawowe narzędzia entity framework dla interfejsu wiersza polecenia .NET Core.

---

### <a name="files-created"></a>Utworzone pliki

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Proces rusztowania tworzy i aktualizuje następujące pliki:

* *Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.
* *Dane/RazorPagesMovieContext.cs*

### <a name="updated"></a>Zaktualizowano

* *Startup.cs*

Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Proces rusztowania tworzy i aktualizuje następujące pliki:

* *Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.
* *Dane/RazorPagesMovieContext.cs*

### <a name="updated"></a>Zaktualizowano

* *Startup.cs*

Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Proces rusztowania tworzy następujące pliki:

* *Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.

Utworzone pliki są wyjaśnione w następnej sekcji.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migracja początkowa

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W tej sekcji konsola Menedżera pakietów (PMC) służy do:

* Dodaj migrację początkową.
* Zaktualizuj bazę danych za pomocą migracji początkowej.

Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package** Manager .

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

W pmc wprowadź następujące polecenia:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Poprzednie polecenia generują następujące ostrzeżenie: "Dla kolumny dziesiętnej "Cena" dla kolumny dziesiętnej nie określono żadnego typu "Cena". Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".

Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.

Polecenie migracji generuje kod do tworzenia początkowego schematu bazy danych. Schemat jest oparty na modelu `DbContext`określonym w programie . Argument `InitialCreate` jest używany do nazywania migracji. Można użyć dowolnej nazwy, ale zgodnie z konwencją wybrano nazwę opisującą migrację.

Polecenie `update` uruchamia `Up` metodę w migracjach, które nie zostały zastosowane. W takim `update` przypadku `Up` uruchamia metodę w *migrations/\<time-stamp>_InitialCreate.cs* file, który tworzy bazę danych.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Badanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.

Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.

Sprawdź `Startup.ConfigureServices` metodę. Podświetlona linia została dodana przez rusztowanie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Współrzędne `RazorPagesMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu. Kontekst danych`RazorPagesMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych. Jednostka odpowiada wierszowi w tabeli.

Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Sprawdź `Up` metodę.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Sprawdź `Up` metodę.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).

Jeśli zostanie wyświetlony błąd:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Pominięto [krok migracji](#pmc).

* Przetestuj łącze **Utwórz.**

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych. Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

Następny samouczek wyjaśnia pliki utworzone przez szkielety.

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Zacznij](xref:tutorials/razor-pages/razor-pages-start)
> [dalej: Scaffolded Strony brzytwy](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

W tej sekcji klasy są dodawane do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html). Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych SQLite. Klasy modelu aplikacji są używane z [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)do pracy z bazą danych. EF Core jest strukturą mapowania obiekto-relacyjnego (ORM), która upraszcza dostęp do danych.

Klasy modelu są znane jako klasy POCO (z "zwykłych starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core. Definiują właściwości danych, które są przechowywane w bazie danych.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Dodawanie modelu danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kliknij prawym przyciskiem myszy projekt **RazorPagesMovie** > **Dodaj** > **nowy folder**. Nazwij folder *Models*.

Kliknij prawym przyciskiem myszy folder *Modele.* Wybierz **pozycję Dodaj** > **klasę**. Nazwij klasę **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodawanie folderu o nazwie *Modele*.
* Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **RazorPagesMovie,** a następnie wybierz polecenie **Dodaj** > **nowy folder**. Nazwij folder *Models*.
* Kliknij prawym przyciskiem myszy folder *Modele,* a następnie wybierz polecenie **Dodaj** > **nowy plik**.
* W oknie dialogowym **Nowy plik:**

  * Wybierz **pozycję Ogólne** w lewym okienku.
  * Wybierz **pozycję Pusta klasa** w środkowym okienku.
  * Nazwij klasę **Film** i wybierz pozycję **Nowy**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilacji.

## <a name="scaffold-the-movie-model"></a>Rusztowanie modelu filmu

W tej sekcji model filmu jest szkieletu. Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Tworzenie folderu *Strony/Filmy:*

* Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.
* Nadawanie nazwy folderowi *Filmy*

Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* W rozwijaniu **klasy Model** wybierz pozycję **Film (RazorPagesMovie.Models).**
* W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zaakceptuj wygenerowaną nazwę **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*

* **W systemie Windows**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Tworzenie folderu *Strony/Filmy:*

* Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.
* Nadawanie nazwy folderowi *Filmy*

Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

W oknie dialogowym **Dodawanie nowych rusztowań** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:

* W rozwijaniu **klasy Model** wybierz lub wpisz **film**.
* W wierszu **klasy Kontekst danych** wpisz opcję **RazorPagesMovieContext** spowoduje utworzenie nowej klasy kontekstu db z poprawną przestrzenią nazw. W tym przypadku będzie to **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Wybierz pozycję **Dodaj**.

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.

---

Proces rusztowania tworzy i aktualizuje następujące pliki:

### <a name="files-created"></a>Utworzone pliki

* *Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.
* *Dane/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Zaktualizowano plik

* *Startup.cs*

Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migracja początkowa

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W tej sekcji konsola Menedżera pakietów (PMC) służy do:

* Dodaj migrację początkową.
* Zaktualizuj bazę danych za pomocą migracji początkowej.

Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package** Manager .

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

W pmc wprowadź następujące polecenia:

```powershell
Add-Migration Initial
Update-Database
```

Polecenie `Add-Migration` generuje kod do tworzenia początkowego schematu bazy danych. Schemat jest oparty na modelu określonym `DbContext` w pliku (W *RazorPagesMovieContext.cs).* Argument `InitialCreate` jest używany do nazwy migracji. Można użyć dowolnej nazwy, ale zgodnie z konwencją używana jest nazwa opisująca migrację. Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.

Polecenie `Update-Database` uruchamia `Up` metodę w pliku *Migrations/\<time-stamp>_InitialCreate.cs.* Metoda `Up` tworzy bazę danych.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Poprzednie polecenia generują następujące ostrzeżenie: "*Dla kolumny dziesiętnej "Cena" dla kolumny dziesiętnej "Cena" dla typu encji "Film". Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".*" Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Badanie kontekstu zarejestrowanego przy iniekcji zależności

ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection). Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji. Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora. Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.

Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.

Sprawdź `Startup.ConfigureServices` metodę. Podświetlona linia została dodana przez rusztowanie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

Współrzędne `RazorPagesMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu. Kontekst danych`RazorPagesMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontekst danych określa, które jednostki są uwzględnione w modelu danych.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych. Jednostka odpowiada wierszowi w tabeli.

Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu. W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Sprawdź `Up` metodę.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Sprawdź `Up` metodę.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testowanie aplikacji

* Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).

Jeśli zostanie wyświetlony błąd:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Pominięto [krok migracji](#pmc).

* Przetestuj łącze **Utwórz.**

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych. Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana. Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

Następny samouczek wyjaśnia pliki utworzone przez szkielety.

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Zacznij](xref:tutorials/razor-pages/razor-pages-start)
> [dalej: Scaffolded Strony brzytwy](xref:tutorials/razor-pages/page)

::: moniker-end
