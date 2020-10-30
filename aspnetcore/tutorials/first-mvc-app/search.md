---
title: Część 7, Dodawanie wyszukiwania do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 7 serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 657072803f59feb99de8b31ddb3a6433d832aa30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059627"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>Część 7, Dodawanie wyszukiwania do aplikacji ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji dodasz możliwość wyszukiwania do `Index` metody akcji, która umożliwia wyszukiwanie filmów według *gatunku* lub *nazwy* .

Zaktualizuj metodę znajdującą się `Index` wewnątrz *kontrolerów/MoviesController. cs* przy użyciu następującego kodu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

Pierwszy wiersz `Index` metody akcji tworzy zapytanie [LINQ](/dotnet/standard/using-linq) do wybierania filmów:

```csharp
var movies = from m in _context.Movie
             select m;
```

Zapytanie jest zdefiniowane *tylko* w tym momencie, **nie** zostało uruchomione względem bazy danych.

Jeśli `searchString` parametr zawiera ciąg, zapytanie o filmy jest modyfikowane w celu filtrowania wartości ciągu wyszukiwania:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

`s => s.Title.Contains()`Powyższy kod jest [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Wyrażenia lambda są używane w kwerendach [LINQ](/dotnet/standard/using-linq) opartych na metodach jako argumenty dla standardowych metod operatora zapytań, takich jak Metoda [WHERE](/dotnet/api/system.linq.enumerable.where) lub `Contains` (używana w kodzie powyżej). Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub są modyfikowane przez wywołanie metody takiej jak `Where` , `Contains` lub `OrderBy` . Zamiast tego wykonywanie zapytania jest odroczone.  Oznacza to, że Obliczanie wyrażenia jest opóźnione do momentu rzeczywistego przełączenia jego wartości rzeczywistej lub `ToListAsync` wywołania metody. Aby uzyskać więcej informacji o odroczonym wykonywaniu zapytań, zobacz [wykonywanie zapytań](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Uwaga: Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) jest uruchamiana w bazie danych, a nie w kodzie c# pokazanym powyżej. Uwzględnianie wielkości liter w zapytaniu zależy od bazy danych i sortowania. Na SQL Server [zawiera](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy do [programu SQL, takie jak](/sql/t-sql/language-elements/like-transact-sql), w przypadku których wielkość liter nie jest uwzględniana. W ramach programu SQLite domyślne sortowanie uwzględnia wielkość liter.

Przejdź do adresu `/Movies/Index`. Dołącz ciąg zapytania, taki jak `?searchString=Ghost` do adresu URL. Wyświetlane są filtrowane filmy.

![Widok indeksu](~/tutorials/first-mvc-app/search/_static/ghost.png)

Jeśli zmienisz podpis `Index` metody w taki sposób, aby miał parametr o nazwie `id` , `id` parametr będzie zgodny z opcjonalnym `{id}` symbolem zastępczym dla tras domyślnych ustawionych w *Startup.cs* .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Zmień parametr na `id` i wszystkie wystąpienia `searchString` zmiany na `id` .

Poprzednia `Index` Metoda:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Zaktualizowana `Index` Metoda z `id` parametrem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Teraz możesz przekazać tytuł wyszukiwania jako dane trasy (segment adresu URL), a nie jako wartość ciągu zapytania.

![Widok indeksu z wyrazem Ghost dodany do adresu URL i zwrotną listą filmów dwóch filmów, Ghostbusters i Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Nie można jednak oczekiwać, aby użytkownicy modyfikują adres URL za każdym razem, gdy chcą wyszukać film. Teraz dodasz elementy interfejsu użytkownika, aby ułatwić im filtrowanie filmów. Jeśli zmieniono sygnaturę `Index` metody w celu przetestowania, jak przekazać parametr powiązany z trasą `ID` , należy zmienić go z powrotem tak, aby pobierał parametr o nazwie `searchString` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Otwórz plik *views/filmy/index. cshtml* i Dodaj `<form>` wyróżniony poniżej znacznik:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Tag HTML `<form>` używa [pomocnika tagów formularza](xref:mvc/views/working-with-forms), dlatego podczas przesyłania formularza ciąg filtru jest ogłaszany w `Index` akcji kontrolera filmów. Zapisz zmiany, a następnie przetestuj filtr.

![Widok indeksu z słowem Ghost wpisanych do pola tekstowego filtru tytułu](~/tutorials/first-mvc-app/search/_static/filter.png)

Nie istnieje `[HttpPost]` Przeciążenie `Index` metody w oczekiwany sposób. Nie jest to potrzebne, ponieważ metoda nie zmienia stanu aplikacji, tylko filtrowanie danych.

Można dodać następującą `[HttpPost] Index` metodę.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed`Parametr służy do tworzenia przeciążenia dla `Index` metody. Będziemy mówić o tym w dalszej części tego samouczka.

W przypadku dodania tej metody akcja Źródło będzie zgodna z `[HttpPost] Index` metodą, a `[HttpPost] Index` Metoda zostanie uruchomiona, jak pokazano na poniższej ilustracji.

![Okno przeglądarki z odpowiedzią aplikacji z indeksu HttpPost: filtr dla elementu Ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Jednak nawet w przypadku dodania tej `[HttpPost]` wersji `Index` metody istnieje ograniczenie, w jaki sposób to wszystko zostało zaimplementowane. Załóżmy, że chcesz utworzyć zakładką określonego wyszukiwania, lub chcesz wysłać link do znajomych, które mogą kliknąć, aby zobaczyć tę samą filtrowaną listę filmów. Zwróć uwagę, że adres URL żądania HTTP POST jest taki sam jak adres URL żądania GET (localhost: {PORT}/filmy/indeks) — w adresie URL nie ma informacji o wyszukiwaniu. Informacje o ciągu wyszukiwania są wysyłane do serwera jako [wartość pola formularza](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Możesz sprawdzić, czy za pomocą narzędzi deweloperskich przeglądarki lub doskonałego [Narzędzia programu Fiddler](https://www.telerik.com/fiddler). Na poniższej ilustracji przedstawiono narzędzia deweloperskie przeglądarki Chrome:

![Karta sieciowa Narzędzia deweloperskie w przeglądarce Microsoft Edge pokazująca treść żądania z Ciągwyszukiwania wartością Ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

W treści żądania można zobaczyć parametr Search i token [XSRF](xref:security/anti-request-forgery) . Uwaga, jak wspomniano w poprzednim samouczku, [pomocnik tagów formularza](xref:mvc/views/working-with-forms) generuje token [XSRF](xref:security/anti-request-forgery) chroniący przed fałszerstwem. Dane nie są modyfikowane, więc nie trzeba sprawdzać tokenu w metodzie kontrolera.

Ponieważ parametr wyszukiwania znajduje się w treści żądania, a nie w adresie URL, nie można przechwytywać tych informacji wyszukiwania do zakładek lub udostępniania innym osobom. Aby rozwiązać ten problem, należy określić żądanie należy `HTTP GET` znaleźć w pliku *viewss/filmów/index. cshtml* .

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Teraz, gdy wyślesz wyszukiwanie, adres URL zawiera ciąg zapytania wyszukiwania. Wyszukiwanie spowoduje również przejście do `HttpGet Index` metody akcji, nawet jeśli masz `HttpPost Index` metodę.

![Okno przeglądarki pokazujące Ciągwyszukiwania = Ghost w adresie URL, a filmy zwrócone, Ghostbusters i Ghostbusters 2 zawierają słowo Ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

Następujące znaczniki pokazują zmianę `form` znacznika:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Dodaj wyszukiwanie według gatunku

Dodaj następującą `MovieGenreViewModel` klasę do folderu *models* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Model widoku film-gatunek będzie zawierać następujące:

* Lista filmów.
* A `SelectList` zawiera listę gatunku. Dzięki temu użytkownik może wybrać gatunek z listy.
* `MovieGenre`, który zawiera wybrany gatunek.
* `SearchString`, który zawiera tekst wprowadzany przez użytkowników w polu tekstowym Wyszukaj.

Zastąp `Index` metodę w `MoviesController.cs` następującym kodzie:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Poniższy kod jest `LINQ` zapytaniem pobierającym wszystkie gatunki z bazy danych.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

`SelectList`Gatunek jest tworzony przez projekcję odrębnych gatunków (nie chcemy, aby nasze listy wyboru miały zduplikowane gatunek).

Gdy użytkownik wyszukuje element, wartość wyszukiwania jest zachowywana w polu wyszukiwania.

## <a name="add-search-by-genre-to-the-index-view"></a>Dodaj wyszukiwanie według gatunku do widoku indeksu

Aktualizacja `Index.cshtml` znaleziona w *widokach/filmach/* w następujący sposób:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Bada wyrażenie lambda użyte w następującym Pomocniku HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

W poprzednim kodzie `DisplayNameFor` pomocnik html sprawdza `Title` Właściwość, do której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną. Ponieważ wyrażenie lambda jest sprawdzane zamiast oceniania, nie otrzymujesz naruszenia dostępu, gdy `model` , `model.Movies` , lub `model.Movies[0]` są `null` puste. Gdy wyrażenie lambda zostanie obliczone (na przykład `@Html.DisplayFor(modelItem => item.Title)` ), wartości właściwości modelu są oceniane.

Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu tych elementów:

![Okno przeglądarki z wynikami https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Poprzedni](controller-methods-views.md) 
>  [Dalej](new-field.md)
