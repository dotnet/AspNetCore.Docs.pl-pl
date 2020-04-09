---
title: Dodawanie wyszukiwania do ASP.NET podstawowych stron razor
author: rick-anderson
description: Pokazuje, jak dodać wyszukiwanie do ASP.NET podstawowych stron razor
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8228207b0f37a6923b29891ac3115dd0be115501
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667707"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a>Dodawanie wyszukiwania do ASP.NET podstawowych stron razor

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

W poniższych sekcjach dodawane jest wyszukiwanie filmów według *gatunku* lub *nazwy.*

Dodaj następujące wyróżnione właściwości do *strony/filmy/index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: zawiera tekst wprowadzony przez użytkowników w polu tekstowym wyszukiwania. `SearchString`ma [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atrybut. `[BindProperty]`wiąże wartości formularza i ciągi zapytań o takiej samej nazwie jak właściwość. `(SupportsGet = true)`jest wymagane do powiązania z żądaniami GET.
* `Genres`: zawiera listę gatunków. `Genres`umożliwia użytkownikowi wybranie gatunku z listy. `SelectList`Wymaga`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: zawiera określony gatunek wybrany przez użytkownika (na przykład "Western").
* `Genres`i `MovieGenre` są używane w dalszej części tego samouczka.

[!INCLUDE[](~/includes/bind-get.md)]

Zaktualizuj `OnGetAsync` metodę strony Indeks za pomocą następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Pierwszy wiersz `OnGetAsync` metody tworzy kwerendę [LINQ,](/dotnet/csharp/programming-guide/concepts/linq/) aby wybrać filmy:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Kwerenda jest zdefiniowana *tylko* w tym momencie, **nie** został uruchomiony względem bazy danych.

Jeśli `SearchString` właściwość nie ma wartości null lub jest pusta, kwerenda filmów jest modyfikowana w celu filtrowania ciągu wyszukiwania:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Kod `s => s.Title.Contains()` jest [wyrażeniem Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas są używane w opartych na metodach zapytań [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) jako `Contains` argumenty do standardowych metod operatora kwerendy, takich jak [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metody lub (używane w poprzednim kodzie). Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub gdy są modyfikowane `Contains` `OrderBy`przez wywołanie metody (na przykład `Where`, lub ). Zamiast tego wykonywanie kwerendy jest odroczone. Oznacza to, że ocena wyrażenia jest opóźniona, dopóki jego `ToListAsync` zrealizowana wartość nie zostanie przesuń ponad lub metoda jest wywoływana. Zobacz [wykonywanie kwerend, aby](/dotnet/framework/data/adonet/ef/language-reference/query-execution) uzyskać więcej informacji.

> [!NOTE]
> Zawiera [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda jest uruchamiana w bazie danych, a nie w kodzie C#. Wielkość liter w kwerendzie zależy od bazy danych i sortowania. Na SQL `Contains` Server, mapuje do [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), który jest bez uwzględniania wielkości liter. W SQLite, z domyślnym sortowania, to rozróżniana wielkość liter.

Przejdź do strony Filmy i dołącz ciąg `?searchString=Ghost` zapytania, `https://localhost:5001/Movies?searchString=Ghost`taki jak adres URL (na przykład ). Wyświetlane są filtrowane filmy.

![Widok indeksu](search/_static/ghost.png)

Jeśli do strony Indeks zostanie dodany następujący szablon trasy, ciąg wyszukiwania może zostać `https://localhost:5001/Movies/Ghost`przekazany jako segment adresu URL (na przykład ).

```cshtml
@page "{searchString?}"
```

Poprzednie ograniczenie trasy umożliwia wyszukiwanie tytułu jako danych trasy (segmentu adresu URL), a nie jako wartości ciągu zapytania.  In `?` `"{searchString?}"` oznacza, że jest to opcjonalny parametr trasy.

![Widok indeksu ze słowem ghost dodanym do adresu URL i zwróconą listą filmów z dwoma filmami, Ghostbusters i Ghostbusters 2](search/_static/g2.png)

Środowisko uruchomieniowe ASP.NET Core używa [powiązania modelu](xref:mvc/models/model-binding) do `SearchString` ustawiania wartości właściwości`?searchString=Ghost`z ciągu`https://localhost:5001/Movies/Ghost`zapytania ( ) lub danych trasy ( ). W przypadku powiązania modelu nie jest rozróżniana wielkość liter.

Nie można jednak oczekiwać, że użytkownicy zmodyfikują adres URL w celu wyszukania filmu. W tym kroku interfejs użytkownika jest dodawany do filtrowania filmów. Jeśli dodano ograniczenie `"{searchString?}"`trasy, usuń je.

Otwórz plik *Pages/Movies/Index.cshtml* i `<form>` dodaj znaczniki wyróżnione w następującym kodzie:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

Tag `<form>` HTML używa następujących [pomocników tagów:](xref:mvc/views/tag-helpers/intro)

* [Pomocnik znacznika formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Po przesłaniu formularza ciąg filtru jest wysyłany do strony *Strony/Filmy/Indeks* za pomocą ciągu zapytania.
* [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms#the-input-tag-helper)

Zapisz zmiany i przetestuj filtr.

![Widok indeksu ze słowem ghost wpisanym w polu tekstowym filtru tytuł](search/_static/filter.png)

## <a name="search-by-genre"></a>Szukaj według gatunku

Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Poniższy kod jest kwerendą LINQ, która pobiera wszystkie gatunki z bazy danych.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Gatunki `SelectList` są tworzone przez projekcję różnych gatunków.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Dodawanie wyszukiwania według gatunku do strony Razor

Aktualizuj *index.cshtml w* następujący sposób:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Poprzedni: Aktualizowanie stron](xref:tutorials/razor-pages/da1)
> [Dalej: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

W poniższych sekcjach dodawane jest wyszukiwanie filmów według *gatunku* lub *nazwy.*

Dodaj następujące wyróżnione właściwości do *strony/filmy/index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: zawiera tekst wprowadzony przez użytkowników w polu tekstowym wyszukiwania. `SearchString`ma [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atrybut. `[BindProperty]`wiąże wartości formularza i ciągi zapytań o takiej samej nazwie jak właściwość. `(SupportsGet = true)`jest wymagane do powiązania z żądaniami GET.
* `Genres`: zawiera listę gatunków. `Genres`umożliwia użytkownikowi wybranie gatunku z listy. `SelectList`Wymaga`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: zawiera określony gatunek wybrany przez użytkownika (na przykład "Western").
* `Genres`i `MovieGenre` są używane w dalszej części tego samouczka.

[!INCLUDE[](~/includes/bind-get.md)]

Zaktualizuj `OnGetAsync` metodę strony Indeks za pomocą następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Pierwszy wiersz `OnGetAsync` metody tworzy kwerendę [LINQ,](/dotnet/csharp/programming-guide/concepts/linq/) aby wybrać filmy:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Kwerenda jest zdefiniowana *tylko* w tym momencie, **nie** został uruchomiony względem bazy danych.

Jeśli `SearchString` właściwość nie ma wartości null lub jest pusta, kwerenda filmów jest modyfikowana w celu filtrowania ciągu wyszukiwania:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Kod `s => s.Title.Contains()` jest [wyrażeniem Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas są używane w opartych na metodach zapytań [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) jako `Contains` argumenty do standardowych metod operatora kwerendy, takich jak [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metody lub (używane w poprzednim kodzie). Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub gdy są modyfikowane `Contains` `OrderBy`przez wywołanie metody (na przykład `Where`, lub ). Zamiast tego wykonywanie kwerendy jest odroczone. Oznacza to, że ocena wyrażenia jest opóźniona, dopóki jego `ToListAsync` zrealizowana wartość nie zostanie przesuń ponad lub metoda jest wywoływana. Zobacz [wykonywanie kwerend, aby](/dotnet/framework/data/adonet/ef/language-reference/query-execution) uzyskać więcej informacji.

**Uwaga:** Zawiera [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda jest uruchamiana w bazie danych, a nie w kodzie C#. Wielkość liter w kwerendzie zależy od bazy danych i sortowania. Na SQL `Contains` Server, mapuje do [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), który jest bez uwzględniania wielkości liter. W SQLite, z domyślnym sortowania, to rozróżniana wielkość liter.

Przejdź do strony Filmy i dołącz ciąg `?searchString=Ghost` zapytania, `https://localhost:5001/Movies?searchString=Ghost`taki jak adres URL (na przykład ). Wyświetlane są filtrowane filmy.

![Widok indeksu](search/_static/ghost.png)

Jeśli do strony Indeks zostanie dodany następujący szablon trasy, ciąg wyszukiwania może zostać `https://localhost:5001/Movies/Ghost`przekazany jako segment adresu URL (na przykład ).

```cshtml
@page "{searchString?}"
```

Poprzednie ograniczenie trasy umożliwia wyszukiwanie tytułu jako danych trasy (segmentu adresu URL), a nie jako wartości ciągu zapytania.  In `?` `"{searchString?}"` oznacza, że jest to opcjonalny parametr trasy.

![Widok indeksu ze słowem ghost dodanym do adresu URL i zwróconą listą filmów z dwoma filmami, Ghostbusters i Ghostbusters 2](search/_static/g2.png)

Środowisko uruchomieniowe ASP.NET Core używa [powiązania modelu](xref:mvc/models/model-binding) do `SearchString` ustawiania wartości właściwości`?searchString=Ghost`z ciągu`https://localhost:5001/Movies/Ghost`zapytania ( ) lub danych trasy ( ). W przypadku powiązania modelu nie jest rozróżniana wielkość liter.

Nie można jednak oczekiwać, że użytkownicy zmodyfikują adres URL w celu wyszukania filmu. W tym kroku interfejs użytkownika jest dodawany do filtrowania filmów. Jeśli dodano ograniczenie `"{searchString?}"`trasy, usuń je.

Otwórz plik *Pages/Movies/Index.cshtml* i `<form>` dodaj znaczniki wyróżnione w następującym kodzie:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Tag `<form>` HTML używa następujących [pomocników tagów:](xref:mvc/views/tag-helpers/intro)

* [Pomocnik znacznika formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Po przesłaniu formularza ciąg filtru jest wysyłany do strony *Strony/Filmy/Indeks* za pomocą ciągu zapytania.
* [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms#the-input-tag-helper)

Zapisz zmiany i przetestuj filtr.

![Widok indeksu ze słowem ghost wpisanym w polu tekstowym filtru tytuł](search/_static/filter.png)

## <a name="search-by-genre"></a>Szukaj według gatunku

Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Poniższy kod jest kwerendą LINQ, która pobiera wszystkie gatunki z bazy danych.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Gatunki `SelectList` są tworzone przez projekcję różnych gatunków.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Dodawanie wyszukiwania według gatunku do strony Razor

Aktualizuj *index.cshtml w* następujący sposób:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu.
Poprzedni kod używa [pomocnika wybierania znaczników](xref:mvc/views/working-with-forms#the-select-tag-helper) i pomocnika znaczników opcji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Poprzedni: Aktualizowanie stron](xref:tutorials/razor-pages/da1)
> [Dalej: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)

::: moniker-end
