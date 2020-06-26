---
title: Część 6, Dodawanie wyszukiwania do Razor stron ASP.NET Core
author: rick-anderson
description: Część 6 serii samouczków na Razor stronach.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/search
ms.openlocfilehash: b0ee3294a4646b199f85d6a023e152e5eabaa225
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408789"
---
# <a name="part-6-add-search-to-aspnet-core-razor-pages"></a>Część 6, Dodawanie wyszukiwania do Razor stron ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

W poniższych sekcjach są dodawane przeszukiwania filmów według *gatunku* lub *nazwy* .

Dodaj następujące wyróżnione właściwości do *stron/filmów/index. cshtml. cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: zawiera tekst wprowadzany przez użytkowników w polu tekstowym Wyszukaj. `SearchString`ma [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atrybut. `[BindProperty]`tworzy powiązanie wartości formularzy i ciągów zapytań o takiej samej nazwie jak właściwość. `(SupportsGet = true)`jest wymagany do tworzenia powiązań w żądaniach GET.
* `Genres`: zawiera listę gatunku. `Genres`umożliwia użytkownikowi wybranie gatunku z listy. `SelectList`KONIECZN`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: zawiera konkretny gatunek wybierany przez użytkownika (na przykład "zachodni").
* `Genres`i `MovieGenre` są używane w dalszej części tego samouczka.

[!INCLUDE[](~/includes/bind-get.md)]

Zaktualizuj metodę strony indeksu `OnGetAsync` przy użyciu następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Pierwszy wiersz `OnGetAsync` metody tworzy zapytanie [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) do wybierania filmów:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Zapytanie jest zdefiniowane *tylko* w tym momencie, **nie** zostało uruchomione względem bazy danych.

Jeśli `SearchString` Właściwość nie ma wartości null lub jest pusta, zapytanie o filmy jest modyfikowane w celu odfiltrowania ciągu wyszukiwania:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kod jest [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Wyrażenia lambda są używane w kwerendach [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) opartych na metodach jako argumenty dla standardowych metod operatora zapytań, takich jak Metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) lub `Contains` (używana w poprzednim kodzie). Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub są modyfikowane przez wywołanie metody (takiej jak `Where` `Contains` lub `OrderBy` ). Zamiast tego wykonywanie zapytania jest odroczone. Oznacza to, że Obliczanie wyrażenia jest opóźnione do momentu przekroczenia jego zrealizowanej wartości lub `ToListAsync` wywołania metody. Aby uzyskać więcej informacji, zobacz [wykonywanie zapytań](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

> [!NOTE]
> Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) jest uruchamiana w bazie danych, a nie w kodzie C#. Uwzględnianie wielkości liter w zapytaniu zależy od bazy danych i sortowania. Na SQL Server `Contains` mapuje do [programu SQL Server, np](/sql/t-sql/language-elements/like-transact-sql). bez uwzględniania wielkości liter. W ramach programu SQLite domyślne sortowanie uwzględnia wielkość liter.

Przejdź do strony filmy i dołącz ciąg zapytania, taki jak `?searchString=Ghost` adres URL (na przykład `https://localhost:5001/Movies?searchString=Ghost` ). Wyświetlane są filtrowane filmy.

![Widok indeksu](search/_static/ghost.png)

Jeśli do strony indeksu zostanie dodany następujący szablon trasy, ciąg wyszukiwania może zostać przekierowany jako segment adresu URL (na przykład `https://localhost:5001/Movies/Ghost` ).

```cshtml
@page "{searchString?}"
```

Powyższe ograniczenie trasy umożliwia przeszukiwanie tytułu jako dane trasy (segment adresu URL), a nie jako wartość ciągu zapytania.  `?`W `"{searchString?}"` tym przypadku jest to opcjonalny parametr trasy.

![Widok indeksu z wyrazem Ghost dodany do adresu URL i zwrotną listą filmów dwóch filmów, Ghostbusters i Ghostbusters 2](search/_static/g2.png)

Środowisko uruchomieniowe ASP.NET Core używa [powiązania modelu](xref:mvc/models/model-binding) , aby ustawić wartość `SearchString` właściwości z ciągu zapytania ( `?searchString=Ghost` ) lub danych trasy ( `https://localhost:5001/Movies/Ghost` ). W powiązaniu modelu nie jest rozróżniana wielkość liter.

Nie można jednak oczekiwać, że użytkownicy modyfikują adres URL w celu wyszukania filmu. W tym kroku zostanie dodany interfejs użytkownika do filtrowania filmów. Jeśli dodano ograniczenie trasy `"{searchString?}"` , usuń je.

Otwórz plik *Pages/Films/index. cshtml* i Dodaj `<form>` znaczniki wyróżnione w poniższym kodzie:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

Tag HTML `<form>` używa następujących [pomocników tagów](xref:mvc/views/tag-helpers/intro):

* [Pomocnik tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Gdy formularz zostanie przesłany, ciąg filtru jest wysyłany do *stron/filmów/indeksu* za pośrednictwem ciągu zapytania.
* [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms#the-input-tag-helper)

Zapisz zmiany i przetestuj filtr.

![Widok indeksu z słowem Ghost wpisanych do pola tekstowego filtru tytułu](search/_static/filter.png)

## <a name="search-by-genre"></a>Wyszukaj według gatunku

Zaktualizuj `OnGetAsync` metodę przy użyciu następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Poniższy kod jest zapytanie LINQ, które pobiera wszystkie gatunki z bazy danych.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Gatunek jest tworzony przez projekcję odrębnych gatuneków.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Dodaj wyszukiwanie według gatunku na Razor stronie

Zaktualizuj *indeks. cshtml* w następujący sposób:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu tych elementów.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka usługi YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Poprzedni: aktualizowanie stron](xref:tutorials/razor-pages/da1) 
>  [Dalej: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

W poniższych sekcjach są dodawane przeszukiwania filmów według *gatunku* lub *nazwy* .

Dodaj następujące wyróżnione właściwości do *stron/filmów/index. cshtml. cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: zawiera tekst wprowadzany przez użytkowników w polu tekstowym Wyszukaj. `SearchString`ma [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atrybut. `[BindProperty]`tworzy powiązanie wartości formularzy i ciągów zapytań o takiej samej nazwie jak właściwość. `(SupportsGet = true)`jest wymagany do tworzenia powiązań w żądaniach GET.
* `Genres`: zawiera listę gatunku. `Genres`umożliwia użytkownikowi wybranie gatunku z listy. `SelectList`KONIECZN`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: zawiera konkretny gatunek wybierany przez użytkownika (na przykład "zachodni").
* `Genres`i `MovieGenre` są używane w dalszej części tego samouczka.

[!INCLUDE[](~/includes/bind-get.md)]

Zaktualizuj metodę strony indeksu `OnGetAsync` przy użyciu następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Pierwszy wiersz `OnGetAsync` metody tworzy zapytanie [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) do wybierania filmów:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Zapytanie jest zdefiniowane *tylko* w tym momencie, **nie** zostało uruchomione względem bazy danych.

Jeśli `SearchString` Właściwość nie ma wartości null lub jest pusta, zapytanie o filmy jest modyfikowane w celu odfiltrowania ciągu wyszukiwania:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kod jest [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Wyrażenia lambda są używane w kwerendach [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) opartych na metodach jako argumenty dla standardowych metod operatora zapytań, takich jak Metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) lub `Contains` (używana w poprzednim kodzie). Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub są modyfikowane przez wywołanie metody (takiej jak `Where` `Contains` lub `OrderBy` ). Zamiast tego wykonywanie zapytania jest odroczone. Oznacza to, że Obliczanie wyrażenia jest opóźnione do momentu przekroczenia jego zrealizowanej wartości lub `ToListAsync` wywołania metody. Aby uzyskać więcej informacji, zobacz [wykonywanie zapytań](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

**Uwaga:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) jest uruchamiana w bazie danych, a nie w kodzie C#. Uwzględnianie wielkości liter w zapytaniu zależy od bazy danych i sortowania. Na SQL Server `Contains` mapuje do [programu SQL Server, np](/sql/t-sql/language-elements/like-transact-sql). bez uwzględniania wielkości liter. W ramach programu SQLite domyślne sortowanie uwzględnia wielkość liter.

Przejdź do strony filmy i dołącz ciąg zapytania, taki jak `?searchString=Ghost` adres URL (na przykład `https://localhost:5001/Movies?searchString=Ghost` ). Wyświetlane są filtrowane filmy.

![Widok indeksu](search/_static/ghost.png)

Jeśli do strony indeksu zostanie dodany następujący szablon trasy, ciąg wyszukiwania może zostać przekierowany jako segment adresu URL (na przykład `https://localhost:5001/Movies/Ghost` ).

```cshtml
@page "{searchString?}"
```

Powyższe ograniczenie trasy umożliwia przeszukiwanie tytułu jako dane trasy (segment adresu URL), a nie jako wartość ciągu zapytania.  `?`W `"{searchString?}"` tym przypadku jest to opcjonalny parametr trasy.

![Widok indeksu z wyrazem Ghost dodany do adresu URL i zwrotną listą filmów dwóch filmów, Ghostbusters i Ghostbusters 2](search/_static/g2.png)

Środowisko uruchomieniowe ASP.NET Core używa [powiązania modelu](xref:mvc/models/model-binding) , aby ustawić wartość `SearchString` właściwości z ciągu zapytania ( `?searchString=Ghost` ) lub danych trasy ( `https://localhost:5001/Movies/Ghost` ). W powiązaniu modelu nie jest rozróżniana wielkość liter.

Nie można jednak oczekiwać, że użytkownicy modyfikują adres URL w celu wyszukania filmu. W tym kroku zostanie dodany interfejs użytkownika do filtrowania filmów. Jeśli dodano ograniczenie trasy `"{searchString?}"` , usuń je.

Otwórz plik *Pages/Films/index. cshtml* i Dodaj `<form>` znaczniki wyróżnione w poniższym kodzie:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Tag HTML `<form>` używa następujących [pomocników tagów](xref:mvc/views/tag-helpers/intro):

* [Pomocnik tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Gdy formularz zostanie przesłany, ciąg filtru jest wysyłany do *stron/filmów/indeksu* za pośrednictwem ciągu zapytania.
* [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms#the-input-tag-helper)

Zapisz zmiany i przetestuj filtr.

![Widok indeksu z słowem Ghost wpisanych do pola tekstowego filtru tytułu](search/_static/filter.png)

## <a name="search-by-genre"></a>Wyszukaj według gatunku

Zaktualizuj `OnGetAsync` metodę przy użyciu następującego kodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Poniższy kod jest zapytanie LINQ, które pobiera wszystkie gatunki z bazy danych.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Gatunek jest tworzony przez projekcję odrębnych gatuneków.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Dodaj wyszukiwanie według gatunku na Razor stronie

Zaktualizuj *indeks. cshtml* w następujący sposób:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu tych elementów.
Powyższy kod używa pomocnika [SELECT tag](xref:mvc/views/working-with-forms#the-select-tag-helper) i znacznika opcji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka usługi YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Poprzedni: aktualizowanie stron](xref:tutorials/razor-pages/da1) 
>  [Dalej: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)

::: moniker-end
