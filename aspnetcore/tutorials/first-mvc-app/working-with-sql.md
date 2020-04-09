---
title: Praca z sqlem w ASP.NET podstawowej aplikacji MVC
author: rick-anderson
description: Dowiedz się więcej o korzystaniu z usługi SQL Server LocalDB lub SQLite w ASP.NET aplikacji Core MVC.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665040"
---
# <a name="work-with-sql-in-aspnet-core"></a>Praca z sql w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Obiekt `MvcMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w pliku *Startup.cs:*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik . W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik . W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania ciągu połączenia na produkcyjny program SQL Server. Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to lekka wersja aparatu baz danych programu SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji. Domyślnie baza danych LocalDB tworzy pliki *mdf* w katalogu *C:/Users/{user}.*

* Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).

  ![Menu Widok](working-with-sql/_static/ssox.png)

* Kliknij prawym `Movie` przyciskiem myszy tabelę **> Zobacz projektanta**

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/design.png)

  ![Otwarcie tabeli filmów w projektancie](working-with-sql/_static/dv.png)

Zanotuj ikonę klawisza obok pozycji `ID`. Domyślnie EF spowoduje, że `ID` właściwość o nazwie klucz podstawowy.

* Kliknij prawym `Movie` przyciskiem myszy tabelę **> Wyświetl dane**

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/ssox2.png)

  ![Otwieranie tabeli filmowych z danymi tabeli](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Seed bazy danych

Utwórz nową `SeedData` klasę o nazwie w folderze *Modele.* Zastąp wygenerowany kod następującymi:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodaj inicjatora nasion

Zastąp zawartość *Program.cs* następującym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy w bazy danych. Można to zrobić za pomocą linków do usuwania w przeglądarce lub z SSOX.
* Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora. Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony. Można to zrobić za pomocą dowolnej z następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**

    ![Ikona zasobnika systemowego usługi IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](working-with-sql/_static/stopIIS.png)

    * Jeśli korzystasz z usługi VS w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania
    * Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony). Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.

---

Aplikacja pokazuje dane rozstawione.

![Aplikacja MVC Movie otwarta w programie Microsoft Edge z danymi filmowymi](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Poprzedni](adding-model.md)
> [następny](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Obiekt `MvcMovieContext` obsługuje zadanie łączenia się z `Movie` bazą danych i mapowania obiektów do rekordów bazy danych. Kontekst bazy danych jest zarejestrowany za pomocą `ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w metodzie w pliku *Startup.cs:*

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik . W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString`plik . W przypadku rozwoju lokalnego pobiera ciąg połączenia z pliku *appsettings.json:*

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Podczas wdrażania aplikacji na serwerze testowym lub produkcyjnym można użyć zmiennej środowiskowej lub innego podejścia, aby ustawić ciąg połączenia na prawdziwy program SQL Server. Zobacz [konfiguracja,](xref:fundamentals/configuration/index) aby uzyskać więcej informacji.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB to lekka wersja aparatu baz danych programu SQL Server Express, która jest przeznaczona do tworzenia programów. LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji. Domyślnie baza danych LocalDB tworzy pliki *mdf* w katalogu *C:/Users/{user}.*

* Z menu **Widok** otwórz **program SQL Server Object Explorer** (SSOX).

  ![Menu Widok](working-with-sql/_static/ssox.png)

* Kliknij prawym `Movie` przyciskiem myszy tabelę **> Zobacz projektanta**

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/design.png)

  ![Otwarcie tabeli filmów w projektancie](working-with-sql/_static/dv.png)

Zanotuj ikonę klawisza obok pozycji `ID`. Domyślnie EF spowoduje, że `ID` właściwość o nazwie klucz podstawowy.

* Kliknij prawym `Movie` przyciskiem myszy tabelę **> Wyświetl dane**

  ![Menu kontekstowe otwarte w tabeli Film](working-with-sql/_static/ssox2.png)

  ![Otwieranie tabeli filmowych z danymi tabeli](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Seed bazy danych

Utwórz nową `SeedData` klasę o nazwie w folderze *Modele.* Zastąp wygenerowany kod następującymi:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Jeśli w db znajdują się jakieś filmy, inicjatora zwraca się i nie są dodawane żadne filmy.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Dodaj inicjatora nasion

Zastąp zawartość *Program.cs* następującym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Testowanie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Usuń wszystkie rekordy w bazy danych. Można to zrobić za pomocą linków do usuwania w przeglądarce lub z SSOX.
* Wymuś zainicjowanie aplikacji (wywołanie metod w `Startup` klasie), aby została uruchamiana metoda inicjatora. Aby wymusić inicjowanie, program IIS Express musi zostać zatrzymany i ponownie uruchomiony. Można to zrobić za pomocą dowolnej z następujących metod:

  * Kliknij prawym przyciskiem myszy ikonę zasobnika systemowego usługi IIS Express w obszarze powiadomień, a następnie naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**

    ![Ikona zasobnika systemowego usługi IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](working-with-sql/_static/stopIIS.png)

    * Jeśli korzystasz z usługi VS w trybie bez debugowania, naciśnij klawisz F5, aby uruchomić go w trybie debugowania
    * Jeśli korzystasz z usługi VS w trybie debugowania, zatrzymaj debuger i naciśnij klawisz F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Usuń wszystkie rekordy w DB (Więc metoda inicjatora zostanie uruchomiony). Zatrzymaj i uruchom aplikację, aby wysiewnić bazę danych.

---

Aplikacja pokazuje dane rozstawione.

![Aplikacja MVC Movie otwarta w programie Microsoft Edge z danymi filmowymi](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Poprzedni](adding-model.md)
> [następny](controller-methods-views.md)

::: moniker-end
