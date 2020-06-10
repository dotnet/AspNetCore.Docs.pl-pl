---
title: Część 8 Dodaj nowe pole do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 8 serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 2dfaba166617c5d0a224f4b47028c141e59ce47f
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652912"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a>Część 8 Dodaj nowe pole do aplikacji ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:

* Dodaj nowe pole do modelu.
* Migruj nowe pole do bazy danych.

Gdy Code First EF jest używany do automatycznego tworzenia bazy danych, Code First:

* Dodaje tabelę do bazy danych w celu śledzenia schematu bazy danych.
* Weryfikuje, czy baza danych jest zsynchronizowana z klasami modelu, z których została wygenerowana. Jeśli nie są zsynchronizowane, EF zgłasza wyjątek. Ułatwia to znalezienie niespójnych problemów z bazą danych i kodem.

## <a name="add-a-rating-property-to-the-movie-model"></a>Dodawanie właściwości oceny do modelu filmu

Dodaj `Rating` Właściwość do *modeli/filmów. cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Kompilacja aplikacji

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Polecenie ⌘ + B

------

Ponieważ dodano nowe pole do `Movie` klasy, należy zaktualizować białe listy powiązań, aby ta nowa właściwość została uwzględniona. W programie *MoviesController.cs*zaktualizuj `[Bind]` atrybut dla obu `Create` metod i, `Edit` Aby uwzględnić `Rating` Właściwość:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Aktualizowanie szablonów widoku w celu wyświetlania, tworzenia i edytowania nowej `Rating` właściwości w widoku przeglądarki.

Edytuj plik */views/Movies/index.cshtml* i Dodaj `Rating` pole:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Zaktualizuj */views/Movies/Create.cshtml* z `Rating` polem.

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio/Visual Studio dla komputerów Mac](#tab/visual-studio+visual-studio-mac)

Możesz skopiować/wkleić poprzednią "grupę formularzy" i pozwól, aby funkcja intelliSense mogła zaktualizować pola. Technologia IntelliSense współpracuje z [pomocnikami tagów](xref:mvc/views/tag-helpers/intro).

![Deweloper wpisze literę R dla wartości atrybutu ASP-for w drugim elemencie Label widoku. W menu kontekstowym IntelliSense pojawiły się dostępne pola, w tym klasyfikacja, które są automatycznie wyróżnione na liście. Gdy deweloper kliknie pole lub naciśnie klawisz Enter na klawiaturze, wartość zostanie ustawiona na Klasyfikacja.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Zaktualizuj pozostałe szablony.

Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny. Poniżej przedstawiono przykładową zmianę, ale trzeba wprowadzić tę zmianę dla każdej z nich `new Movie` .

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola. Jeśli jest teraz uruchomiona, `SqlException` zgłaszane są następujące elementy:

`SqlException: Invalid column name 'Rating'.`

Ten błąd występuje, ponieważ zaktualizowana Klasa modelu filmu jest inna niż schemat tabeli filmów istniejącej bazy danych. (Brak `Rating` kolumn w tabeli bazy danych).

Istnieje kilka metod rozpoznawania błędu:

1. Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych na podstawie nowego schematu klasy modelu. To podejście jest bardzo wygodne w cyklu rozwoju, gdy chodzi o aktywne programowanie w testowej bazie danych. pozwala ona szybko rozwijać model i schemat bazy danych. Minusem, mimo że utracisz istniejące dane w bazie danych, więc nie chcesz używać tego podejścia w produkcyjnej bazie danych. Użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji. Jest to dobre podejście do wczesnego programowania i korzystania z oprogramowania SQLite.

2. Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu. Zaletą tego podejścia jest utrzymywanie danych. Tę zmianę można wprowadzić ręcznie lub przez utworzenie skryptu zmiany bazy danych.

3. Użyj Migracje Code First, aby zaktualizować schemat bazy danych.

W tym samouczku zostanie użyta Migracje Code First.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.

  ![Menu PMC](adding-model/_static/pmc.png)

W obszarze PMC wprowadź następujące polecenia:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration`Polecenie informuje platformę migracji, aby przeanalizować bieżący `Movie` model z bieżącym `Movie` schematem bazy danych i utworzyć wymagany kod w celu przeprowadzenia migracji bazy danych do nowego modelu.

Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji. Warto użyć zrozumiałej nazwy dla pliku migracji.

Jeśli wszystkie rekordy w bazie danych zostaną usunięte, metoda Initialize będzie wypełniać bazę danych i zawierać `Rating` pole.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych. Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie. DB*). Następnie uruchom `ef database update` polecenie:

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

Uruchom aplikację i sprawdź, czy można tworzyć, edytować i wyświetlać filmy z `Rating` polem. Zaktualizuj aplikację:

* Dodaj `Rating` pole do `Edit` `Details` szablonów, i `Delete` .
* Zaktualizuj powiązanie w metodzie Edytuj akcję elementu `MoviesController` .

> [!div class="step-by-step"]
> [Poprzedni](search.md) 
>  [Dalej](validation.md)
