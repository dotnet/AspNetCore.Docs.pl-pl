---
title: Dodawanie nowego pola do strony razor w ASP.NET Core
author: rick-anderson
description: Pokazuje, jak dodać nowe pole do strony Razor ze wzorem Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d34b938dbd1b512ddb167cac0c035837889cd38f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657816"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Dodawanie nowego pola do strony razor w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations służy do:

* Dodaj nowe pole do modelu.
* Migrowanie zmiany nowego schematu pola do bazy danych.

Podczas korzystania z EF Code First do automatycznego tworzenia bazy danych, Code First:

* Dodaje `__EFMigrationsHistory` tabelę do bazy danych, aby śledzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z wygenerowali.
* Jeśli klasy modelu nie są zsynchronizowane z bazy danych, EF zgłasza wyjątek.

Automatyczna weryfikacja schematu/modelu w synchronizacji ułatwia znajdowanie niespójnych problemów z bazą danych/kodem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Dodawanie właściwości Ocena do modelu filmu

Otwórz plik *Models/Movie.cs* i `Rating` dodaj właściwość:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Kompilowanie aplikacji.

Edytuj *strony/filmy/index.cshtml*i `Rating` dodaj pole:

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

Zaktualizuj następujące strony:

* Dodaj `Rating` to pole do stron Usuń i Szczegóły.
* Zaktualizuj plik `Rating` [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) za pomocą pola.
* Dodaj `Rating` to pole do strony edytuj.

Aplikacja nie będzie działać, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola. Uruchamianie aplikacji bez aktualizowania bazy `SqlException`danych zgłasza:

`SqlException: Invalid column name 'Rating'.`

Wyjątek `SqlException` jest spowodowany przez zaktualizowane Movie model klasy jest inny niż schemat Movie tabeli bazy danych. (W tabeli `Rating` bazy danych nie ma żadnej kolumny).

Istnieje kilka podejść do rozwiązania błędu:

1. Czy entity framework automatycznie upuścić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu. Takie podejście jest wygodne na początku cyklu rozwoju; pozwala szybko rozwijać schemat modelu i bazy danych razem. Wadą jest utrata istniejących danych w bazie danych. Nie używaj tego podejścia w produkcyjnej bazie danych! Upuszczanie bazy danych na zmiany schematu i przy użyciu inicjatora automatycznie seed bazy danych z danymi testowymi jest często produktywny sposób tworzenia aplikacji.

2. Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby odpowiadała klasom modelu. Zaletą tego podejścia jest przechowywanie danych. Tę zmianę można wprowadzić ręcznie lub tworząc skrypt zmiany bazy danych.

3. Użyj migracji code first, aby zaktualizować schemat bazy danych.

W tym samouczku użyj migracji code first.

Zaktualizuj `SeedData` klasę tak, aby zapewniała wartość dla nowej kolumny. Przykładowa zmiana jest wyświetlana poniżej, ale warto `new Movie` wprowadzić tę zmianę dla każdego bloku.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Zobacz [wypełniony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).

Skompiluj rozwiązanie.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Dodawanie migracji dla pola klasyfikacji

Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.
W pmc wprowadź następujące polecenia:

```powershell
Add-Migration Rating
Update-Database
```

Polecenie `Add-Migration` informuje framework do:

* Porównaj `Movie` model `Movie` ze schematem bazy danych.
* Utwórz kod, aby przeprowadzić migrację schematu bazy danych do nowego modelu.

Nazwa "Ocena" jest dowolna i jest używana do nadawanie nazwy plikowi migracji. Warto użyć znaczącej nazwy pliku migracji.

Polecenie `Update-Database` informuje strukturę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.

<a name="ssox"></a>

Jeśli usuniesz wszystkie rekordy w bazy danych, inicjator wysiewu bazy danych i uwzględni `Rating` pole. Można to zrobić za pomocą łączy usuwania w przeglądarce lub z [Programu Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Inną opcją jest usunięcie bazy danych i użycie migracji do ponownego utworzenia bazy danych. Aby usunąć bazę danych w SSOX:

* Wybierz bazę danych w SSOX.
* Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie *Usuń*.
* Zaznacz **pole wyboru Zamknij istniejące połączenia**.
* Kliknij przycisk **OK**.
* W [PMC](xref:tutorials/razor-pages/new-field#pmc), zaktualizować bazę danych:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Upuść i ponownie utwórz bazę danych

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Usuń folder migracji.  Użyj następujących poleceń, aby ponownie utworzyć bazę danych.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Uruchom aplikację i sprawdź, czy możesz tworzyć/edytować/wyświetlać filmy z polem. `Rating` Jeśli baza danych nie jest rozstawiona, `SeedData.Initialize` ustaw punkt przerwania w metodzie.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)
> [dalej: Dodawanie sprawdzania poprawności](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations służy do:

* Dodaj nowe pole do modelu.
* Migrowanie zmiany nowego schematu pola do bazy danych.

Podczas korzystania z EF Code First do automatycznego tworzenia bazy danych, Code First:

* Dodaje tabelę do bazy danych, aby śledzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z wygenerowali.
* Jeśli klasy modelu nie są zsynchronizowane z bazy danych, EF zgłasza wyjątek.

Automatyczna weryfikacja schematu/modelu w synchronizacji ułatwia znajdowanie niespójnych problemów z bazą danych/kodem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Dodawanie właściwości Ocena do modelu filmu

Otwórz plik *Models/Movie.cs* i `Rating` dodaj właściwość:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Kompilowanie aplikacji.

Edytuj *strony/filmy/index.cshtml*i `Rating` dodaj pole:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Zaktualizuj następujące strony:

* Dodaj `Rating` to pole do stron Usuń i Szczegóły.
* Zaktualizuj plik `Rating` [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) za pomocą pola.
* Dodaj `Rating` to pole do strony edytuj.

Aplikacja nie będzie działać, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola. Jeśli działa teraz, aplikacja `SqlException`zgłasza :

`SqlException: Invalid column name 'Rating'.`

Ten błąd jest spowodowany przez zaktualizowaną klasę modelu Movie jest inny niż schemat Movie tabeli bazy danych. (W tabeli `Rating` bazy danych nie ma żadnej kolumny).

Istnieje kilka podejść do rozwiązania błędu:

1. Czy entity framework automatycznie upuścić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu. Takie podejście jest wygodne na początku cyklu rozwoju; pozwala szybko rozwijać schemat modelu i bazy danych razem. Wadą jest utrata istniejących danych w bazie danych. Nie używaj tego podejścia w produkcyjnej bazie danych! Upuszczanie bazy danych na zmiany schematu i przy użyciu inicjatora automatycznie seed bazy danych z danymi testowymi jest często produktywny sposób tworzenia aplikacji.

2. Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby odpowiadała klasom modelu. Zaletą tego podejścia jest przechowywanie danych. Tę zmianę można wprowadzić ręcznie lub tworząc skrypt zmiany bazy danych.

3. Użyj migracji code first, aby zaktualizować schemat bazy danych.

W tym samouczku użyj migracji code first.

Zaktualizuj `SeedData` klasę tak, aby zapewniała wartość dla nowej kolumny. Przykładowa zmiana jest wyświetlana poniżej, ale warto `new Movie` wprowadzić tę zmianę dla każdego bloku.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Zobacz [wypełniony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Skompiluj rozwiązanie.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Dodawanie migracji dla pola klasyfikacji

Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.
W pmc wprowadź następujące polecenia:

```powershell
Add-Migration Rating
Update-Database
```

Polecenie `Add-Migration` informuje framework do:

* Porównaj `Movie` model `Movie` ze schematem bazy danych.
* Utwórz kod, aby przeprowadzić migrację schematu bazy danych do nowego modelu.

Nazwa "Ocena" jest dowolna i jest używana do nadawanie nazwy plikowi migracji. Warto użyć znaczącej nazwy pliku migracji.

Polecenie `Update-Database` informuje strukturę, aby zastosować zmiany schematu do bazy danych.

<a name="ssox"></a>

Jeśli usuniesz wszystkie rekordy w bazy danych, inicjator wysiewu bazy danych i uwzględni `Rating` pole. Można to zrobić za pomocą łączy usuwania w przeglądarce lub z [Programu Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Inną opcją jest usunięcie bazy danych i użycie migracji do ponownego utworzenia bazy danych. Aby usunąć bazę danych w SSOX:

* Wybierz bazę danych w SSOX.
* Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie *Usuń*.
* Zaznacz **pole wyboru Zamknij istniejące połączenia**.
* Kliknij przycisk **OK**.
* W [PMC](xref:tutorials/razor-pages/new-field#pmc), zaktualizować bazę danych:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Upuść i ponownie utwórz bazę danych

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych. Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie.db*). Następnie uruchom `ef database update` polecenie:

```dotnetcli
dotnet ef database update
```

---

Uruchom aplikację i sprawdź, czy możesz tworzyć/edytować/wyświetlać filmy z polem. `Rating` Jeśli baza danych nie jest rozstawiona, `SeedData.Initialize` ustaw punkt przerwania w metodzie.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)
> [dalej: Dodawanie sprawdzania poprawności](xref:tutorials/razor-pages/validation)

::: moniker-end
