---
title: Dodawanie wyszukiwania do aplikacji core mvc ASP.NET
author: rick-anderson
description: Pokazuje, jak dodać wyszukiwanie do podstawowej aplikacji Core MVC ASP.NET
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662870"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Dodawanie wyszukiwania do aplikacji core mvc ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji do tej sekcji `Index` można dodać funkcję wyszukiwania do metody akcji, która umożliwia wyszukiwanie filmów według *gatunku* lub *nazwy*.

Zaktualizuj metodę znalezioną `Index` w *controllers/MoviesController.cs* za pomocą następującego kodu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

Pierwszy wiersz metody `Index` akcji tworzy kwerendę [LINQ,](/dotnet/standard/using-linq) aby wybrać filmy:

```csharp
var movies = from m in _context.Movie
             select m;
```

Kwerenda jest zdefiniowana *tylko* w tym momencie, **nie** został uruchomiony względem bazy danych.

Jeśli `searchString` parametr zawiera ciąg, kwerenda filmów jest modyfikowana w celu filtrowania wartości ciągu wyszukiwania:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Powyższy `s => s.Title.Contains()` kod jest [wyrażeniem Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas są używane w opartych na metodach zapytań [LINQ](/dotnet/standard/using-linq) jako `Contains` argumenty do standardowych metod operatora kwerendy, takich jak [Where](/dotnet/api/system.linq.enumerable.where) metody lub (używane w kodzie powyżej). Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub gdy są `Where`modyfikowane przez wywołanie metody, takiej jak , `Contains`lub `OrderBy`. Zamiast tego wykonywanie kwerendy jest odroczone.  Oznacza to, że ocena wyrażenia jest opóźniona, dopóki jego zrealizowana `ToListAsync` wartość jest rzeczywiście iterowana lub metoda jest wywoływana. Aby uzyskać więcej informacji na temat odroczonego wykonywania kwerendy, zobacz [Wykonywanie kwerend](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Uwaga: [Zawiera](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda jest uruchamiana w bazie danych, a nie w kodzie c# pokazano powyżej. Wielkość liter w kwerendzie zależy od bazy danych i sortowania. Na SQL Server, [Zawiera](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy do [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), który jest bez uwzględniania wielkości liter. W SQLite, z domyślnym sortowania, to rozróżniana wielkość liter.

Przejdź do adresu `/Movies/Index`. Dołącz ciąg zapytania, `?searchString=Ghost` taki jak adres URL. Wyświetlane są filtrowane filmy.

![Widok indeksu](~/tutorials/first-mvc-app/search/_static/ghost.png)

Jeśli zmienisz podpis `Index` metody, aby mieć `id`parametr `id` o nazwie, `{id}` parametr będzie zgodny z opcjonalnym symbolem zastępczym dla tras domyślnych ustawionych w *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Zmień parametr `id` na i wszystkie `searchString` wystąpienia `id`zmiany na .

Poprzednia `Index` metoda:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Zaktualizowana `Index` metoda `id` z parametrem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Teraz możesz przekazać tytuł wyszukiwania jako dane trasy (segment adresu URL), a nie jako wartość ciągu zapytania.

![Widok indeksu ze słowem ghost dodanym do adresu URL i zwróconą listą filmów z dwoma filmami, Ghostbusters i Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Nie można jednak oczekiwać, że użytkownicy będą modyfikować adres URL za każdym razem, gdy chcą wyszukać film. Teraz dodasz elementy interfejsu użytkownika, aby ułatwić im filtrowanie filmów. Jeśli zmieniono podpis `Index` metody, aby przetestować sposób `ID` przekazywania parametru związanego z trasą, zmień go z powrotem, tak aby miał parametr o nazwie: `searchString`

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Otwórz plik *Widoki/Filmy/Index.cshtml* i `<form>` dodaj znaczniki wyróżnione poniżej:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Tag `<form>` HTML używa [pomocnika znacznika formularza,](xref:mvc/views/working-with-forms)więc podczas przesyłania formularza ciąg filtru jest księgowy do `Index` akcji kontrolera filmów. Zapisz zmiany, a następnie przetestuj filtr.

![Widok indeksu ze słowem ghost wpisanym w polu tekstowym filtru tytuł](~/tutorials/first-mvc-app/search/_static/filter.png)

Nie ma `[HttpPost]` przeciążenia `Index` metody, jak można się spodziewać. Nie jest to potrzebne, ponieważ metoda nie zmienia stanu aplikacji, tylko filtrowanie danych.

Można dodać następującą `[HttpPost] Index` metodę.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Parametr `notUsed` jest używany do tworzenia przeciążenia `Index` dla metody. Porozmawiamy o tym w dalszej części samouczka.

Jeśli dodasz tę metodę, wywoływacz `[HttpPost] Index` akcji będzie `[HttpPost] Index` zgodny z metodą, a metoda będzie działać, jak pokazano na obrazku poniżej.

![Okno przeglądarki z odpowiedzią aplikacji z indeksu HttpPost: filtr na duchu](~/tutorials/first-mvc-app/search/_static/fo.png)

Jednak nawet jeśli dodasz `[HttpPost]` tę `Index` wersję metody, istnieje ograniczenie w jaki sposób to wszystko zostało zaimplementowane. Wyobraź sobie, że chcesz dołekować określone wyszukiwanie lub wysłać link do znajomych, który mogą kliknąć, aby zobaczyć tę samą filtrowane listy filmów. Należy zauważyć, że adres URL żądania HTTP POST jest taki sam jak adres URL żądania GET (localhost:{PORT}/Movies/Index) - w adresie URL nie ma żadnych informacji o wyszukiwaniu. Informacje o ciągu wyszukiwania są wysyłane do serwera jako [wartość pola formularza](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Możesz to sprawdzić za pomocą narzędzi programistycznych przeglądarki lub doskonałego [narzędzia Fiddler](https://www.telerik.com/fiddler). Na poniższej ilustracji przedstawiono narzędzia programistyczne przeglądarki Chrome:

![Karta Sieć narzędzi deweloperskich w programie Microsoft Edge przedstawiająca treść żądania z wyszukiwanąciątą wartością ducha](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Parametr wyszukiwania i token [XSRF](xref:security/anti-request-forgery) można zobaczyć w treści żądania. Uwaga, jak wspomniano w poprzednim samouczku, [Pomocnik znaczników formularza](xref:mvc/views/working-with-forms) generuje token antyzwiązany z fałszerszem [XSRF.](xref:security/anti-request-forgery) Nie modyfikujemy danych, więc nie musimy sprawdzać poprawności tokenu w metodzie kontrolera.

Ponieważ parametr wyszukiwania znajduje się w treści żądania, a nie w adresie URL, nie można przechwycić tych informacji wyszukiwania w celu dołek lub udostępnienia innym osobom. Rozwiąż to, określając `HTTP GET` żądanie, które należy znaleźć w pliku *Views/Movies/Index.cshtml.*

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Teraz po przesłaniu wyszukiwania adres URL zawiera ciąg zapytania wyszukiwania. Wyszukiwanie przejdzie również `HttpGet Index` do metody akcji, nawet `HttpPost Index` jeśli masz metodę.

![Okno przeglądarki pokazujące searchString = ghost w adresie URL i filmy wrócił, Ghostbusters i Ghostbusters 2, zawierają słowo duch](~/tutorials/first-mvc-app/search/_static/search_get.png)

Następujące znaczniki pokazuje zmiany `form` do tagu:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Dodaj wyszukiwanie według gatunku

Dodaj następującą `MovieGenreViewModel` klasę do folderu *Modele:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Model widoku gatunku filmu będzie zawierał:

* Lista filmów.
* A `SelectList` zawierający listę gatunków. Dzięki temu użytkownik może wybrać gatunek z listy.
* `MovieGenre`, który zawiera wybrany gatunek.
* `SearchString`, który zawiera tekst wprowadzany przez użytkowników w polu tekstowym wyszukiwania.

`Index` Zastąp `MoviesController.cs` metodę następującym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Poniższy kod `LINQ` jest kwerendą, która pobiera wszystkie gatunki z bazy danych.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

Gatunki `SelectList` są tworzone przez projekcję różnych gatunków (nie chcemy, aby nasza lista wybranych miała zduplikowane gatunki).

Gdy użytkownik wyszukuje element, wartość wyszukiwania jest zachowywana w polu wyszukiwania.

## <a name="add-search-by-genre-to-the-index-view"></a>Dodawanie wyszukiwania według gatunku do widoku Indeks

Aktualizacja `Index.cshtml` znaleziona w *widokach/filmach/* w następujący sposób:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Sprawdź wyrażenie lambda używane w następującym pomocniku HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

W poprzednim kodzie `DisplayNameFor` Pomocnik HTML sprawdza `Title` właściwość, do którego odwołuje się wyrażenie lambda, aby określić nazwę wyświetlaną. Ponieważ wyrażenie lambda jest kontrolowane, a nie oceniane, nie `model`otrzymasz naruszenia zasad dostępu, gdy , `model.Movies`lub są `model.Movies[0]` `null` lub puste. Gdy wyrażenie lambda jest oceniane `@Html.DisplayFor(modelItem => item.Title)`(na przykład), wartości właściwości modelu są oceniane.

Przetestuj aplikację, wyszukując według gatunku, według tytułu filmu i przez oba:

![Okno przeglądarki z wynikamihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Poprzedni](controller-methods-views.md)
> [następny](new-field.md)
