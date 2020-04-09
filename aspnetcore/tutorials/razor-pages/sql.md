---
title: Praca z bazą danych i ASP.NET Core
author: rick-anderson
description: Wyjaśnia pracę z bazą danych i ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664340"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Praca z bazą danych i ASP.NET Core

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Joe [Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Obiekt `RazorPagesMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera Iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w *Startup.cs:*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik . W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json.*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wartość nazwy bazy danych`Database={Database name}`( ) będzie inna dla wygenerowanego kodu. Wartość nazwy jest dowolna.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania ciągu połączenia na serwerze prawdziwej bazy danych. Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to lekka wersja aparatu bazy danych programu SQL Server Express przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji. Domyślnie baza danych `*.mdf` LocalDB `C:\Users\<user>\` tworzy pliki w katalogu.

<a name="ssox"></a>
* Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).

  ![Menu Widok](sql/_static/ssox.png)

* Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz **polecenie Wyświetl projektanta:**

  ![Menu kontekstowe otwarte w tabeli Film](sql/_static/design.png)

  ![Tabele filmów otwarte w projektancie](sql/_static/dv.png)

Zanotuj ikonę klawisza obok pozycji `ID`. Domyślnie EF tworzy właściwość o nazwie `ID` klucz podstawowy.

* Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz polecenie **Wyświetl dane:**

  ![Otwieranie tabeli filmowych z danymi tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Seed bazy danych

Utwórz nową `SeedData` klasę o nazwie w folderze *Modele* z następującym kodem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodaj inicjatora nasion

W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę seed, przekazując do niej kontekst.
* Zutylizuj kontekst po zakończeniu metody inicjatora.

Poniższy kod przedstawia zaktualizowany plik *Program.cs.*

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Następujący wyjątek `Update-Database` występuje, gdy nie został uruchomiony:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy w bazy danych. Można to zrobić za pomocą linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora. Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony. Można to zrobić za pomocą dowolnej z następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę:**

    ![Ikona zasobnika systemowego usługi IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * Jeśli program VS jest uruchomiony w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania.
    * Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony). Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.

Aplikacja pokazuje dane rozstawione.

---

Następny samouczek poprawi prezentację danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Rusztowanie Razor Strony](xref:tutorials/razor-pages/page)
> [Dalej: Aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Obiekt `RazorPagesMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera Iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w *Startup.cs:*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Aby uzyskać więcej informacji `ConfigureServices`na temat metod stosowanych w programie , zobacz:

* [Ogólne rozporządzenie o ochronie danych (RODO)](xref:security/gdpr) w `CookiePolicyOptions`ASP.NET Core dla .
* [Wersja setcompatibility](xref:mvc/compatibility-version)

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik . W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json.*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wartość nazwy bazy danych`Database={Database name}`( ) będzie inna dla wygenerowanego kodu. Wartość nazwy jest dowolna.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania ciągu połączenia na serwerze prawdziwej bazy danych. Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to lekka wersja aparatu bazy danych programu SQL Server Express przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji. Domyślnie baza danych `*.mdf` LocalDB `C:/Users/<user/>` tworzy pliki w katalogu.

<a name="ssox"></a>
* Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).

  ![Menu Widok](sql/_static/ssox.png)

* Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz **polecenie Wyświetl projektanta:**

  ![Menu kontekstowe otwarte w tabeli Film](sql/_static/design.png)

  ![Otwarcie tabeli filmów w projektancie](sql/_static/dv.png)

Zanotuj ikonę klawisza obok pozycji `ID`. Domyślnie EF tworzy właściwość o nazwie `ID` klucz podstawowy.

* Kliknij prawym `Movie` przyciskiem myszy tabelę i wybierz polecenie **Wyświetl dane:**

  ![Otwieranie tabeli filmowych z danymi tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Seed bazy danych

Utwórz nową `SeedData` klasę o nazwie w folderze *Modele* z następującym kodem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodaj inicjatora nasion

W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj metodę seed, przekazując do niej kontekst.
* Zutylizuj kontekst po zakończeniu metody inicjatora.

Poniższy kod przedstawia zaktualizowany plik *Program.cs.*

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Aplikacja produkcyjna nie `Database.Migrate`będzie wywoływać . Jest dodawany do poprzedniego kodu, aby zapobiec `Update-Database` następującemu wyjątkowi, gdy nie został uruchomiony:

SqlException: Nie można otworzyć bazy danych "RazorPagesMovieContext-21" wymagane przez login. Logowanie nie powiodło się.
Nie można zalogować się dla użytkownika "nazwa użytkownika".

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy w bazy danych. Można to zrobić za pomocą linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora. Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony. Można to zrobić za pomocą dowolnej z następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę:**

    ![Ikona zasobnika systemowego usługi IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * Jeśli program VS jest uruchomiony w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania.
    * Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony). Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.

Aplikacja pokazuje dane rozstawione.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony). Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.

Aplikacja pokazuje dane rozstawione.

---

Aplikacja pokazuje dane rozstawione:

![Aplikacja filmowa otwarta w Chrome pokazująca dane filmu](sql/_static/m55.png)

Następny samouczek oczyści prezentację danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Poprzedni: Rusztowanie Razor Strony](xref:tutorials/razor-pages/page)
> [Dalej: Aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end
