---
title: Dodawanie nowego pola do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak użyć pierwszej migracji kodu frameworka, aby dodać nowe pole do modelu i przeprowadzić migrację do bazy danych.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a5ea9b75cf8bb1f31cb07a2b32f361bdbfd4efa3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662905"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>Dodawanie nowego pola do aplikacji ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations służy do:

* Dodaj nowe pole do modelu.
* Migrowanie nowego pola do bazy danych.

Gdy EF Code First jest używany do automatycznego tworzenia bazy danych, Code First:

* Dodaje tabelę do bazy danych, aby śledzić schemat bazy danych.
* Weryfikuje bazy danych jest zsynchronizowany z klas modelu, który został wygenerowany z. Jeśli nie są one zsynchronizowane, EF zgłasza wyjątek. Ułatwia to znajdowanie niespójnych problemów z bazą danych/kodem.

## <a name="add-a-rating-property-to-the-movie-model"></a>Dodawanie właściwości Ocena do modelu filmu

Dodaj `Rating` właściwość do *Models/Movie.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Kompilacja aplikacji

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Polecenie ♠ + B

------

Ponieważ dodano nowe pole do `Movie` klasy, należy zaktualizować białą listę wiązania, aby ta nowa właściwość została uwzględniona. W *MoviesController.cs*, zaktualizuj `[Bind]` atrybut `Create` `Edit` zarówno dla metod `Rating` akcji, jak i metod działania, aby uwzględnić właściwość:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Zaktualizuj szablony widoku, aby wyświetlić, utworzyć i edytować nową `Rating` właściwość w widoku przeglądarki.

Edytuj plik */Views/Movies/Index.cshtml* i `Rating` dodaj pole:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Zaktualizuj *pole /Views/Movies/Create.cshtml.* `Rating`

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio+visual-studio-mac)

Możesz skopiować/wkleić poprzednią "grupę formularzy" i umożliwić intelliSense pomoc w aktualizacji pól. IntelliSense współpracuje z [Tag Helpers](xref:mvc/views/tag-helpers/intro).

![Deweloper wpisał literę R dla wartości atrybutu asp-for w drugim elemencie etykiety widoku. Pojawiło się menu kontekstowe Intellisense pokazujące dostępne pola, w tym Ocena, które jest automatycznie wyróżnione na liście. Gdy deweloper kliknie pole lub naciśnie klawisz Enter na klawiaturze, wartość zostanie ustawiona na Ocena.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Zaktualizuj pozostałe szablony.

Zaktualizuj `SeedData` klasę tak, aby zapewniała wartość dla nowej kolumny. Przykładowa zmiana jest pokazana poniżej, ale warto `new Movie`wprowadzić tę zmianę dla każdego .

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Aplikacja nie będzie działać, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola. Jeśli jest uruchomiony teraz, `SqlException` są generowane następujące:

`SqlException: Invalid column name 'Rating'.`

Ten błąd występuje, ponieważ zaktualizowana klasa modelu Movie różni się od schematu tabeli Movie istniejącej bazy danych. (W tabeli `Rating` bazy danych nie ma żadnej kolumny).

Istnieje kilka podejść do rozwiązania błędu:

1. Czy entity framework automatycznie upuścić i ponownie utworzyć bazę danych na podstawie nowego schematu klasy modelu. Takie podejście jest bardzo wygodne na początku cyklu rozwoju, gdy robisz aktywnego rozwoju w testowej bazie danych; pozwala szybko rozwijać schemat modelu i bazy danych razem. Minusem jest jednak to, że tracisz istniejące dane w bazie danych — więc nie chcesz używać tego podejścia w produkcyjnej bazie danych! Za pomocą inicjatora automatycznie seed bazy danych z danymi testowymi jest często produktywny sposób tworzenia aplikacji. Jest to dobre podejście do wczesnego rozwoju i przy użyciu SQLite.

2. Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby odpowiadała klasom modelu. Zaletą tego podejścia jest przechowywanie danych. Tę zmianę można wprowadzić ręcznie lub tworząc skrypt zmiany bazy danych.

3. Użyj migracji code first, aby zaktualizować schemat bazy danych.

W tym samouczku używane są migracje code first.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.

  ![Menu PMC](adding-model/_static/pmc.png)

W pmc wprowadź następujące polecenia:

```powershell
Add-Migration Rating
Update-Database
```

Polecenie `Add-Migration` informuje strukturę migracji, `Movie` aby zbadać `Movie` bieżący model z bieżącym schematem bazy danych i utworzyć kod niezbędny do migracji bazy danych do nowego modelu.

Nazwa "Ocena" jest dowolna i jest używana do nadawanie nazwy plikowi migracji. Warto użyć znaczącej nazwy pliku migracji.

Jeśli wszystkie rekordy w bazy danych zostaną usunięte, metoda inicjowania `Rating` spowoduje wysiew bazy danych i uwzględni pole.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych. Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie.db*). Następnie uruchom `ef database update` polecenie:

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

Uruchom aplikację i sprawdź, czy możesz tworzyć, `Rating` edytować i wyświetlać filmy za pomocą pola. Zaktualizuj aplikację:

* Dodaj `Rating` to pole `Edit` `Details`do `Delete` przycisku , i wyświetl szablony.
* Zaktualizuj powiązanie w `MoviesController`metodzie akcji edycji programu .

> [!div class="step-by-step"]
> [Poprzedni](search.md)
> [następny](validation.md)
