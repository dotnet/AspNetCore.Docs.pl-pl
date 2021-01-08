---
title: Część 4, współpraca z bazą danych
author: rick-anderson
description: Część 4 z serii samouczków na Razor stronach.
ms.author: riande
ms.date: 01/05/2021
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 8c9d0d9c24e0ce81925ccde463bcf085531b665e
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024742"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a>Część 4 z serii samouczków na Razor stronach

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).

`RazorPagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs*:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz. W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *appsettings.json* pliku.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wygenerowane parametry połączenia są podobne do następujących:

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja. Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:\Users\<user>\` katalogu.

<a name="ssox"></a>
1. Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).

   ![Menu Widok](sql/_static/5/ssox.png)

1. Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków**:

   ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/5/design.png)

   ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

   Zanotuj ikonę klucza obok pozycji `ID` . Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.

1. Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane**:

   ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Stan witryny sieci Web programu [SQLite](https://www.sqlite.org/) :

> Oprogramowanie SQLite to samodzielna, osadzona, w pełni funkcjonalna, publiczna i ogólnie oparta na usłudze SQL Database aparat. SQLite to najbardziej używany aparat bazy danych na całym świecie.

Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych programu SQLite i ją przeglądać. Poniższy obraz pochodzi z [przeglądarki DB dla oprogramowania SQLite](https://sqlitebrowser.org/). Jeśli masz ulubione narzędzie SQLite, pozostaw komentarz na temat tego, co Ci się podoba.

![Przeglądarka DB dla oprogramowania SQLite przedstawiająca bazę danych filmów](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> W tym samouczku zostanie użyta funkcja *migracji* Entity Framework Core, jeśli jest to możliwe. Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych. Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone. Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane. Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem. Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite. Zamiast tego, po zmianie schematu baza danych zostanie porzucona i utworzona.
>
>Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie. Ponowne kompilowanie tabeli obejmuje:
>
>* Tworzenie nowej tabeli.
>* Kopiowanie danych ze starej tabeli do nowej tabeli.
>* Porzucenie starej tabeli.
>* Zmiana nazwy nowej tabeli.
>
>Więcej informacji można znaleźć w następujących zasobach:
> * [Ograniczenia dostawcy bazy danych EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Dostosowywanie kodu migracji](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Wstępne wypełnianie danych](/ef/core/modeling/data-seeding)
> * [Instrukcja ALTER TABLE w programie SQLite](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Wypełnianie bazy danych

Utwórz nową klasę o nazwie `SeedData` w folderze *models* o następującym kodzie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodawanie inicjatora inicjatora

Zastąp zawartość pliku *Program.cs* następującym kodem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.
* Usuń kontekst, gdy metoda inicjatora zostanie zakończona. [Instrukcja using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.

Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Usuń wszystkie rekordy z bazy danych. Korzystanie z linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)

1. Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana. Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie. Zatrzymaj i uruchom ponownie usługi IIS przy użyciu dowolnej z następujących metod:

   1. Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień, a następnie wybierz polecenie **Zakończ** lub **Zatrzymaj lokację**:

      ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu kontekstowe](sql/_static/stopIIS.png)

   1. Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.
   1. Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora. Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

---

Aplikacja pokazuje dane z rozrzutu:

![Aplikacja filmowa otwarta w przeglądarce przedstawiająca dane filmu](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

> [!div class="step-by-step"]
> [Poprzednie: Rusztowania Razor ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).

`RazorPagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs*:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz. W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *appsettings.json* pliku.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wygenerowane parametry połączenia będą podobne do następujących:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja. Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:\Users\<user>\` katalogu.

<a name="ssox"></a>
* Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).

  ![Menu Widok](sql/_static/ssox.png)

* Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków**:

  ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/design.png)

  ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

Zanotuj ikonę klucza obok pozycji `ID` . Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.

* Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane**:

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Stan witryny sieci Web programu [SQLite](https://www.sqlite.org/) :

> Oprogramowanie SQLite to samodzielna, osadzona, w pełni funkcjonalna, publiczna i ogólnie oparta na usłudze SQL Database aparat. SQLite to najbardziej używany aparat bazy danych na całym świecie.

Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych programu SQLite i ją przeglądać. Poniższy obraz pochodzi z [przeglądarki DB dla oprogramowania SQLite](https://sqlitebrowser.org/). Jeśli masz ulubione narzędzie SQLite, pozostaw komentarz na temat tego, co Ci się podoba.

![Przeglądarka DB dla oprogramowania SQLite przedstawiająca bazę danych filmów](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> W tym samouczku zostanie użyta funkcja *migracji* Entity Framework Core, jeśli jest to możliwe. Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych. Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone. Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane. Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem. Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite. Zamiast tego, po zmianie schematu baza danych zostanie porzucona i utworzona.
>
>Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie. Ponowne kompilowanie tabeli obejmuje:
>
>* Tworzenie nowej tabeli.
>* Kopiowanie danych ze starej tabeli do nowej tabeli.
>* Porzucenie starej tabeli.
>* Zmiana nazwy nowej tabeli.
>
>Więcej informacji można znaleźć w następujących zasobach:
> * [Ograniczenia dostawcy bazy danych EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Dostosowywanie kodu migracji](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Wstępne wypełnianie danych](/ef/core/modeling/data-seeding)
> * [Instrukcja ALTER TABLE w programie SQLite](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Wypełnianie bazy danych

Utwórz nową klasę o nazwie `SeedData` w folderze *models* o następującym kodzie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodawanie inicjatora inicjatora

Zastąp zawartość pliku *Program.cs* następującym kodem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.
* Usuń kontekst, gdy metoda inicjatora zostanie zakończona. [Instrukcja using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.

Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy z bazy danych. Użyj linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana. Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie. Zatrzymaj i uruchom ponownie usługi IIS przy użyciu dowolnej z następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**:

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.
    * Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora. Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

---

Aplikacja pokazuje dane z rozrzutu:

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55https.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

> [!div class="step-by-step"]
> [Poprzednie: Rusztowania Razor ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).

`RazorPagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs*:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Aby uzyskać więcej informacji na temat metod używanych w programie `ConfigureServices` , zobacz:

* [Obsługa ogólne rozporządzenie o ochronie danych UE (Rodo) w ASP.NET Core](xref:security/gdpr) dla `CookiePolicyOptions` .
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz. W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *appsettings.json* pliku.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wygenerowane parametry połączenia będą podobne do następujących:

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja. Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:/Users/<user/>` katalogu.

<a name="ssox"></a>
* Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).

  ![Menu Widok](sql/_static/ssox.png)

* Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków**:

  ![Menu kontekstowe jest otwarte w tabeli filmów](sql/_static/design.png)

  ![Tabela filmów otwarta w projektancie](sql/_static/dv.png)

Zanotuj ikonę klucza obok pozycji `ID` . Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.

* Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane**:

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Wypełnianie bazy danych

Utwórz nową klasę o nazwie `SeedData` w folderze *models* o następującym kodzie:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodawanie inicjatora inicjatora

Zastąp zawartość pliku *Program.cs* następującym kodem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:

* Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.
* Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.
* Usuń kontekst, gdy metoda inicjatora zostanie zakończona. [Instrukcja using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.

Aplikacja produkcyjna nie będzie wywoływana `Database.Migrate` . Jest on dodawany do poprzedniego kodu, aby zapobiec następującemu wyjątku, gdy `Update-Database` nie został uruchomiony:

SqlException: nie można otworzyć bazy danych " Razor PagesMovieContext-21" żądanej przez nazwę logowania. Logowanie nie powiodło się.
Logowanie użytkownika "Nazwa użytkownika" nie powiodło się.

### <a name="test-the-app"></a>Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy z bazy danych. Można to zrobić za pomocą linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana. Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie. Można to zrobić przy użyciu następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**:

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.
    * Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora. Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora. Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.

---

Aplikacja pokazuje dane z rozrzutu:

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55https.png)

Następny samouczek czyści prezentację danych.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja tego samouczka usługi YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Poprzednie: Rusztowania Razor ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)

::: moniker-end
