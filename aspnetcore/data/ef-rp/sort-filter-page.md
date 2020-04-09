---
title: Strony brzytwy z EF Core w rdzeniu ASP.NET - Sortowanie, filtr, stronicowanie - 3 z 8
author: rick-anderson
description: W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania do strony Razor przy użyciu ASP.NET Core i Entity Framework Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 9563f3ef52ce429eb0a58b468acb8e9cd7b276e2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656465"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>Strony brzytwy z EF Core w rdzeniu ASP.NET - Sortowanie, filtr, stronicowanie - 3 z 8

Przez [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT)i Jon P [Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku dodano funkcje sortowania, filtrowania i stronicowania na stronach Uczniowie.

Na poniższej ilustracji przedstawiono ukończoną stronę. Nagłówki kolumn są klikalnymi łączami do sortowania kolumny. Kliknij nagłówek kolumny wielokrotnie, aby przełączać się między kolejnością sortowania rosnącego i malejącego.

![Strona indeksu uczniów](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Dodawanie sortowania

Zastąp kod w *pages/students/index.cshtml.cs* następującym kodem, aby dodać sortowanie.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

Powyższy kod ma następujące działanie:

* Dodaje właściwości zawierające parametry sortowania.
* Zmienia nazwę właściwości `Student` na `Students`.
* Zastępuje kod w `OnGetAsync` metodzie.

Metoda `OnGetAsync` odbiera `sortOrder` parametr z ciągu zapytania w adresie URL. Adres URL (w tym ciąg zapytania) jest generowany przez [Pomocnika znacznika zakotwiczenia](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).

Parametrem `sortOrder` jest "Nazwa" lub "Data". Po `sortOrder` parametrze znajduje się opcjonalnie "_desc", aby określić kolejność malejącą. Domyślna kolejność sortowania jest rosnąca.

Gdy indeks strony jest wymagane z **studentów** łącza, nie ma ciągu zapytania. Uczniowie są wyświetlani w porządku rosnącym według nazwiska. Kolejność rosnąca według nazwiska jest domyślnym (przypadek `switch` fall-through) w instrukcji. Gdy użytkownik kliknie łącze `sortOrder` nagłówka kolumny, odpowiednia wartość jest podana w wartości ciągu zapytania.

`NameSort`i `DateSort` są używane przez stronę Razor do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Kod używa operatora warunkowego [C#?:](/dotnet/csharp/language-reference/operators/conditional-operator). Operator `?:` jest operatorem trójskładnikowym (zajmuje trzy operandy). Pierwszy wiersz określa, `sortOrder` że gdy jest `NameSort` null lub pusty, jest ustawiona na "name_desc". Jeśli `sortOrder` **nie** jest null `NameSort` lub puste, jest ustawiona na pusty ciąg.

Te dwie instrukcje umożliwiają stronie ustawienie hiperłączy nagłówka kolumny w następujący sposób:

| Bieżąca kolejność sortowania   | Hiperłącze do nazwiska | Daty Hiperłącze |
|:--------------------:|:-------------------:|:--------------:|
| Nazwisko rosnące  | descending          | ascending      |
| Nazwisko malejące | ascending           | ascending      |
| Data rosnąca       | ascending           | descending     |
| Data malejąco      | ascending           | ascending      |

Metoda używa LINQ do jednostek, aby określić kolumnę do sortowania według. Kod inicjuje `IQueryable<Student>` instrukcję przed switch i modyfikuje ją w instrukcji switch:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Gdy`IQueryable` jest tworzony lub modyfikowany, żadne zapytanie nie jest wysyłane do bazy danych. Kwerenda nie jest wykonywana, `IQueryable` dopóki obiekt nie zostanie przekonwertowany na kolekcję. `IQueryable`są konwertowane na kolekcję, `ToListAsync`wywołując metodę, taką jak . W związku `IQueryable` z tym kod powoduje pojedyncze zapytanie, które nie jest wykonywane, dopóki nie zostanie wykonana następująca instrukcja:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`może uzyskać pełne z dużą liczbą kolumn sortowalnych. Aby uzyskać informacje na temat alternatywnego sposobu kodowania tej funkcji, zobacz [Użyj dynamicznego LINQ, aby uprościć kod](xref:data/ef-mvc/advanced#dynamic-linq) w wersji MVC tej serii samouczków.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Dodawanie hiperłączy nagłówka kolumny do strony Indeks ucznia

Zastąp kod w *Students/Index.cshtml,* następującym kodem. Zmiany są wyróżnione.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Powyższy kod ma następujące działanie:

* Dodaje hiperłącza `LastName` do `EnrollmentDate` nagłówków i kolumn.
* Używa informacji i `NameSort` `DateSort` do konfigurowania hiperłączy z bieżącymi wartościami kolejności sortowania.
* Zmienia nagłówek strony z Indeks na Studenci.
* Zmiany `Model.Student` `Model.Students`na .

Aby sprawdzić, czy sortowanie działa:

* Uruchom aplikację i wybierz kartę **Uczniowie.**
* Kliknij nagłówki kolumn.

## <a name="add-filtering"></a>Dodawanie filtrowania

Aby dodać filtrowanie do strony Indeks uczniów:

* Pole tekstowe i przycisk prześlij zostanie dodany do strony Razor. Pole tekstowe dostarcza ciąg wyszukiwania na imię lub nazwisko.
* Model strony zostanie zaktualizowany w celu użycia wartości pola tekstowego.

### <a name="update-the-ongetasync-method"></a>Aktualizacja Metody OnGetAsync

Zastąp kod w *Students/Index.cshtml.cs* następującym kodem, aby dodać filtrowanie:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

Powyższy kod ma następujące działanie:

* Dodaje `searchString` parametr do `OnGetAsync` metody i zapisuje wartość `CurrentFilter` parametru we właściwości. Wartość ciągu wyszukiwania jest odbierana z pola tekstowego dodanego w następnej sekcji.
* Dodaje do instrukcji LINQ klauzuli. `Where` Klauzula `Where` wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania. Instrukcja LINQ jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable vs. IEnumerable

Kod wywołuje `Where` metodę na `IQueryable` obiekcie, a filtr jest przetwarzany na serwerze. W niektórych scenariuszach aplikacja może `Where` wywoływać metodę jako metodę rozszerzenia w kolekcji w pamięci. Załóżmy `_context.Students` na przykład `DbSet` zmiany z EF Core do `IEnumerable` metody repozytorium, która zwraca kolekcję. Wynik byłby zwykle taki sam, ale w niektórych przypadkach może być inny.

Na przykład implementacja programu `Contains` .NET Framework domyślnie wykonuje porównanie z uwzględnieniem wielkości liter. W programie `Contains` SQL Server wielkość liter zależy od ustawienia sortowania wystąpienia programu SQL Server. Sql Server domyślnie nie jest uwzględniana wielkości liter. SqLite domyślnie rozróżnia wielkość liter. `ToUpper`może być wywołana w celu wyraźnego uchylenia się badania:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Powyższy kod zapewni, że filtr jest niewrażliwy na `Where` przypadek, nawet `IEnumerable` jeśli metoda jest wywoływana na lub działa na SQLite.

Gdy `Contains` jest wywoływana w `IEnumerable` kolekcji, .NET Core implementacji jest używany. Gdy `Contains` jest wywoływana na obiekt, `IQueryable` używana jest implementacja bazy danych.

Wywołanie `Contains` `IQueryable` jest zwykle korzystne ze względu na wydajność. Za `IQueryable`pomocą programu filtrowanie odbywa się za pomocą serwera bazy danych. Jeśli `IEnumerable` zostanie utworzony jako pierwszy, wszystkie wiersze muszą zostać zwrócone z serwera bazy danych.

Istnieje kara za wykonanie `ToUpper`połączenia . Kod `ToUpper` dodaje funkcję w klauzuli WHERE instrukcji TSQL SELECT. Dodana funkcja uniemożliwia optymalizatorowi korzystanie z indeksu. Biorąc pod uwagę, że SQL jest zainstalowany jako niewrażliwe `ToUpper` na wielkości, najlepiej unikać wywołania, gdy nie jest potrzebne.

Aby uzyskać więcej informacji, zobacz [Jak używać kwerendy bez uwzględniania wielkości liter z dostawcą sqlite](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-razor-page"></a>Aktualizowanie strony Razor

Zastąp kod w *pages/students/index.cshtml,* aby utworzyć przycisk **wyszukiwania** i różne chrome.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Poprzedni kod używa `<form>` [pomocnika znacznika,](xref:mvc/views/tag-helpers/intro) aby dodać pole tekstowe wyszukiwania i przycisk. Domyślnie pomocnik `<form>` tagu przesyła dane formularza za pomocą postu. W ucho, parametry są przekazywane w treści wiadomości HTTP, a nie w adresie URL. Gdy jest używany protokół HTTP GET, dane formularza są przekazywane w adresie URL jako ciągi zapytań. Przekazywanie danych za pomocą ciągów zapytań umożliwia użytkownikom dodawanie do zakładek adresu URL. [Wytyczne W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) zalecają, aby GET było używane, gdy akcja nie powoduje aktualizacji.

Przetestuj aplikację:

* Wybierz kartę **Uczniowie** i wprowadź ciąg wyszukiwania. Jeśli używasz SQLite, filtr jest bez uwzględniania wielkości liter tylko wtedy, gdy zaimplementowano opcjonalny `ToUpper` kod pokazany wcześniej.

* Wybierz **pozycję Szukaj**.

Należy zauważyć, że adres URL zawiera ciąg wyszukiwania. Przykład:

```
https://localhost:<port>/Students?SearchString=an
```

Jeśli strona jest zakładka, zakładka zawiera adres URL `SearchString` strony i ciąg zapytania. W `method="get"` tagu `form` jest to, co spowodowało ciąg zapytania do wygenerowania.

Obecnie po wybraniu łącza sortowania nagłówka kolumny wartość filtru z pola **Wyszukiwania** zostanie utracona. Utracona wartość filtru zostanie ustalona w następnej sekcji.

## <a name="add-paging"></a>Dodawanie stronicowania

W tej sekcji `PaginatedList` klasa jest tworzona do obsługi stronicowania. Klasa `PaginatedList` używa `Skip` i `Take` instrukcji do filtrowania danych na serwerze zamiast pobierania wszystkich wierszy tabeli. Na poniższej ilustracji przedstawiono przyciski stronicowania.

![Strona indeksu uczniów z łączami stronicowania](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Tworzenie klasy PaginatedList

W folderze projektu `PaginatedList.cs` utwórz za pomocą następującego kodu:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

Metoda `CreateAsync` w poprzednim kodzie przyjmuje rozmiar strony i numer `Skip` `Take` strony i `IQueryable`stosuje odpowiednie instrukcje do . Gdy `ToListAsync` jest wywoływana na `IQueryable`, zwraca list zawierający tylko żądaną stronę. Właściwości `HasPreviousPage` i `HasNextPage` są używane do włączania lub wyłączania **przycisków stronicowania Poprzedni** i **Następny.**

Metoda `CreateAsync` jest używana do `PaginatedList<T>`tworzenia pliku . Konstruktor nie może `PaginatedList<T>` utworzyć obiektu; konstruktorów nie można uruchomić kodu asynchroniowego.

### <a name="add-paging-to-the-pagemodel-class"></a>Dodawanie stronicowania do klasy PageModel

Zastąp kod w *Students/Index.cshtml.cs,* aby dodać stronicowanie.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

Powyższy kod ma następujące działanie:

* Zmienia typ `Students` właściwości z `IList<Student>` `PaginatedList<Student>`.
* Dodaje indeks strony, `sortOrder`bieżący `currentFilter` i `OnGetAsync` do podpisu metody.
* Zapisuje kolejność sortowania we właściwości CurrentSort.
* Resetuje indeks strony do 1, gdy pojawi się nowy ciąg wyszukiwania.
* Używa klasy, `PaginatedList` aby uzyskać jednostki Student.

Wszystkie parametry, `OnGetAsync` które odbiera są null, gdy:

* Strona jest wywoływana z linku **Uczniowie.**
* Użytkownik nie kliknął łącza stronicowania ani sortowania.

Po kliknięciu łącza stronicowania zmienna indeksu strony zawiera numer strony do wyświetlenia.

Właściwość `CurrentSort` udostępnia razor page z bieżącej kolejności sortowania. Bieżąca kolejność sortowania musi być uwzględniona w łączach stronicowania, aby zachować kolejność sortowania podczas stronicowania.

Właściwość `CurrentFilter` udostępnia Razor Page z bieżącym ciągiem filtru. Wartość: `CurrentFilter`

* Muszą być zawarte w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania.
* Należy przywrócić do pola tekstowego, gdy strona jest ponownie świetliona.

Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, strona zostanie zresetowana do 1. Strona musi zostać zresetowana do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych. Po wprowadzeniu wartości wyszukiwania i **wybraniu opcji Prześlij:**

  * Ciąg wyszukiwania zostanie zmieniony.
  * Parametr `searchString` nie jest null.

  Metoda `PaginatedList.CreateAsync` konwertuje zapytanie ucznia do jednej strony uczniów w typie kolekcji, który obsługuje stronicowania. Ta pojedyncza strona uczniów jest przekazywana do strony Razor.

  Dwa znaki zapytania `pageIndex` po `PaginatedList.CreateAsync` wywołaniu reprezentują [operatora zrzeszania wartości null.](/dotnet/csharp/language-reference/operators/null-conditional-operator) Operator scalania wartości null definiuje wartość domyślną dla typu możliwego do wartości null. Wyrażenie `(pageIndex ?? 1)` oznacza zwraca `pageIndex` wartość, jeśli ma wartość. Jeśli `pageIndex` nie ma wartości, zwróć 1.

### <a name="add-paging-links-to-the-razor-page"></a>Dodawanie łączy stronicowania do strony Razor

Zastąp kod w *Students/Index.cshtml* następującym kodem. Zmiany są wyróżnione:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Łącza nagłówka kolumny używają ciągu zapytania do przekazywania bieżącego ciągu wyszukiwania do `OnGetAsync` metody:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Przyciski stronicowania są wyświetlane przez pomocników znaczników:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Uruchom aplikację i przejdź do strony dla uczniów.

* Aby upewnić się, że stronicowanie działa, kliknij łącza stronicowania w różnych zamówieniach sortowania.
* Aby sprawdzić, czy stronicowanie działa poprawnie z sortowaniem i filtrowaniem, wprowadź ciąg wyszukiwania i spróbuj stronicować.

![strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Dodaj grupowanie

W tej sekcji utworzy się stronę Informacje, która wyświetla liczbę studentów, którzy zarejestrowali się dla każdej daty rejestracji. Aktualizacja używa grupowania i zawiera następujące kroki:

* Utwórz model widoku dla danych używanych przez stronę **Informacje.**
* Zaktualizuj stronę Informacje, aby użyć modelu widoku.

### <a name="create-the-view-model"></a>Tworzenie modelu widoku

Utwórz folder *Modele/Modele SchoolViewModels.*

Utwórz *schoolviewmodels/enrollmentDateGroup.cs* z następującym kodem:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Tworzenie strony Razor

Utwórz plik *Pages/About.cshtml* z następującym kodem:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Tworzenie modelu strony

Utwórz plik *Pages/About.cshtml.cs* z następującym kodem:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w `EnrollmentDateGroup` każdej grupie i przechowuje wyniki w kolekcji obiektów modelu widoku.

Uruchom aplikację i przejdź do strony Informacje. Liczba uczniów dla każdej daty rejestracji jest wyświetlana w tabeli.

![Strona Informacje](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Następne kroki

W następnym samouczku aplikacja używa migracji do aktualizowania modelu danych.

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/crud)
> [Następny samouczek](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku dodawane są funkcje sortowania, filtrowania, grupowania i stronicowania.

Na poniższej ilustracji przedstawiono ukończoną stronę. Nagłówki kolumn są klikalnymi łączami do sortowania kolumny. Kliknięcie nagłówka kolumny wielokrotnie przełącza się między kolejnością sortowania rosnącego i malejącego.

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

Jeśli napotkasz problemy, których nie możesz rozwiązać, pobierz [ukończoną aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Dodawanie sortowania do strony Indeks

Dodaj ciągi do *students/index.cshtml.cs,* `PageModel` aby zawierały parametry sortowania:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Zaktualizuj *students/index.cshtml.cs* `OnGetAsync` następującym kodem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Poprzedni kod odbiera `sortOrder` parametr z ciągu zapytania w adresie URL. Adres URL (w tym ciąg zapytania) jest generowany przez [Pomocnika tagu zakotwiczenia](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

Parametrem `sortOrder` jest "Nazwa" lub "Data". Po `sortOrder` parametrze znajduje się opcjonalnie "_desc", aby określić kolejność malejącą. Domyślna kolejność sortowania jest rosnąca.

Gdy indeks strony jest wymagane z **studentów** łącza, nie ma ciągu zapytania. Uczniowie są wyświetlani w porządku rosnącym według nazwiska. Kolejność rosnąca według nazwiska jest domyślnym (przypadek `switch` fall-through) w instrukcji. Gdy użytkownik kliknie łącze `sortOrder` nagłówka kolumny, odpowiednia wartość jest podana w wartości ciągu zapytania.

`NameSort`i `DateSort` są używane przez stronę Razor do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Następujący kod zawiera warunkowy [c#?: operator:](/dotnet/csharp/language-reference/operators/conditional-operator)

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

Pierwszy wiersz określa, `sortOrder` że gdy jest `NameSort` null lub pusty, jest ustawiona na "name_desc". Jeśli `sortOrder` **nie** jest null `NameSort` lub puste, jest ustawiona na pusty ciąg.

Jest `?: operator` również znany jako operator trójskładnikowy.

Te dwie instrukcje umożliwiają stronie ustawienie hiperłączy nagłówka kolumny w następujący sposób:

| Bieżąca kolejność sortowania | Hiperłącze do nazwiska | Daty Hiperłącze |
|:--------------------:|:-------------------:|:--------------:|
| Nazwisko rosnące | descending        | ascending      |
| Nazwisko malejące | ascending           | ascending      |
| Data rosnąca       | ascending           | descending     |
| Data malejąco      | ascending           | ascending      |

Metoda używa LINQ do jednostek, aby określić kolumnę do sortowania według. Kod inicjuje `IQueryable<Student>` instrukcję przed switch i modyfikuje ją w instrukcji switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Gdy`IQueryable` jest tworzony lub modyfikowany, żadne zapytanie nie jest wysyłane do bazy danych. Kwerenda nie jest wykonywana, `IQueryable` dopóki obiekt nie zostanie przekonwertowany na kolekcję. `IQueryable`są konwertowane na kolekcję, `ToListAsync`wywołując metodę, taką jak . W związku `IQueryable` z tym kod powoduje pojedyncze zapytanie, które nie jest wykonywane, dopóki nie zostanie wykonana następująca instrukcja:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`może uzyskać pełne z dużą liczbą kolumn sortowalnych.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Dodawanie hiperłączy nagłówka kolumny do strony Indeks ucznia

Zastąp kod w *Students/Index.cshtml,* następującym wyróżnionym kodem:

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Powyższy kod ma następujące działanie:

* Dodaje hiperłącza `LastName` do `EnrollmentDate` nagłówków i kolumn.
* Używa informacji i `NameSort` `DateSort` do konfigurowania hiperłączy z bieżącymi wartościami kolejności sortowania.

Aby sprawdzić, czy sortowanie działa:

* Uruchom aplikację i wybierz kartę **Uczniowie.**
* Kliknij **pozycję Nazwisko**.
* Kliknij pozycję **Data rejestracji**.

Aby lepiej zrozumieć kod:

* W *programie Students/Index.cshtml.cs*ustaw `switch (sortOrder)`punkt przerwania na .
* Dodaj zegarek `NameSort` i `DateSort`.
* W *programie Students/Index.cshtml*ustaw `@Html.DisplayNameFor(model => model.Student[0].LastName)`punkt przerwania na .

Krok po kroku debugera.

## <a name="add-a-search-box-to-the-students-index-page"></a>Dodawanie pola wyszukiwania do strony Indeks uczniów

Aby dodać filtrowanie do strony Indeks uczniów:

* Pole tekstowe i przycisk prześlij zostanie dodany do strony Razor. Pole tekstowe dostarcza ciąg wyszukiwania na imię lub nazwisko.
* Model strony zostanie zaktualizowany w celu użycia wartości pola tekstowego.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dodawanie funkcji filtrowania do metody Indeks

Zaktualizuj *students/index.cshtml.cs* `OnGetAsync` następującym kodem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Powyższy kod ma następujące działanie:

* Dodaje `searchString` parametr do `OnGetAsync` metody. Wartość ciągu wyszukiwania jest odbierana z pola tekstowego dodanego w następnej sekcji.
* Dodano do instrukcji LINQ klauzuli. `Where` Klauzula `Where` wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania. Instrukcja LINQ jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.

Uwaga: Poprzedni kod wywołuje `Where` metodę `IQueryable` na obiekcie, a filtr jest przetwarzany na serwerze. W niektórych scenariuszach aplikacja może `Where` wywoływać metodę jako metodę rozszerzenia w kolekcji w pamięci. Załóżmy `_context.Students` na przykład `DbSet` zmiany z EF Core do `IEnumerable` metody repozytorium, która zwraca kolekcję. Wynik byłby zwykle taki sam, ale w niektórych przypadkach może być inny.

Na przykład implementacja programu `Contains` .NET Framework domyślnie wykonuje porównanie z uwzględnieniem wielkości liter. W programie `Contains` SQL Server wielkość liter zależy od ustawienia sortowania wystąpienia programu SQL Server. Sql Server domyślnie nie jest uwzględniana wielkości liter. `ToUpper`może być wywołana w celu wyraźnego uchylenia się badania:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Powyższy kod zapewni, że wyniki są bez uwzględniania wielkości liter, jeśli kod zmieni się do użycia `IEnumerable`. Gdy `Contains` jest wywoływana w `IEnumerable` kolekcji, .NET Core implementacji jest używany. Gdy `Contains` jest wywoływana na obiekt, `IQueryable` używana jest implementacja bazy danych. Zwracanie `IEnumerable` z repozytorium może mieć znaczną karę wydajności:

1. Wszystkie wiersze są zwracane z serwera bazy danych.
1. Filtr jest stosowany do wszystkich zwróconych wierszy w aplikacji.

Istnieje kara za wykonanie `ToUpper`połączenia . Kod `ToUpper` dodaje funkcję w klauzuli WHERE instrukcji TSQL SELECT. Dodana funkcja uniemożliwia optymalizatorowi korzystanie z indeksu. Biorąc pod uwagę, że SQL jest zainstalowany jako niewrażliwe `ToUpper` na wielkości, najlepiej unikać wywołania, gdy nie jest potrzebne.

### <a name="add-a-search-box-to-the-student-index-page"></a>Dodawanie pola wyszukiwania do strony Indeks ucznia

W *polu Strony/Studenci/Index.cshtml*dodaj poniższy wyróżniony kod, aby utworzyć przycisk **Wyszukiwania** i różne chrome.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Poprzedni kod używa `<form>` [pomocnika znacznika,](xref:mvc/views/tag-helpers/intro) aby dodać pole tekstowe wyszukiwania i przycisk. Domyślnie pomocnik `<form>` tagu przesyła dane formularza za pomocą postu. W ucho, parametry są przekazywane w treści wiadomości HTTP, a nie w adresie URL. Gdy jest używany protokół HTTP GET, dane formularza są przekazywane w adresie URL jako ciągi zapytań. Przekazywanie danych za pomocą ciągów zapytań umożliwia użytkownikom dodawanie do zakładek adresu URL. [Wytyczne W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) zalecają, aby GET było używane, gdy akcja nie powoduje aktualizacji.

Przetestuj aplikację:

* Wybierz kartę **Uczniowie** i wprowadź ciąg wyszukiwania.
* Wybierz **pozycję Szukaj**.

Należy zauważyć, że adres URL zawiera ciąg wyszukiwania.

```html
http://localhost:5000/Students?SearchString=an
```

Jeśli strona jest zakładka, zakładka zawiera adres URL `SearchString` strony i ciąg zapytania. W `method="get"` tagu `form` jest to, co spowodowało ciąg zapytania do wygenerowania.

Obecnie po wybraniu łącza sortowania nagłówka kolumny wartość filtru z pola **Wyszukiwania** zostanie utracona. Utracona wartość filtru zostanie ustalona w następnej sekcji.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Dodawanie funkcji stronicowania do strony Indeks uczniów

W tej sekcji `PaginatedList` klasa jest tworzona do obsługi stronicowania. Klasa `PaginatedList` używa `Skip` i `Take` instrukcji do filtrowania danych na serwerze zamiast pobierania wszystkich wierszy tabeli. Na poniższej ilustracji przedstawiono przyciski stronicowania.

![Strona indeksu uczniów z łączami stronicowania](sort-filter-page/_static/paging.png)

W folderze projektu `PaginatedList.cs` utwórz za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

Metoda `CreateAsync` w poprzednim kodzie przyjmuje rozmiar strony i numer `Skip` `Take` strony i `IQueryable`stosuje odpowiednie instrukcje do . Gdy `ToListAsync` jest wywoływana na `IQueryable`, zwraca list zawierający tylko żądaną stronę. Właściwości `HasPreviousPage` i `HasNextPage` są używane do włączania lub wyłączania **przycisków stronicowania Poprzedni** i **Następny.**

Metoda `CreateAsync` jest używana do `PaginatedList<T>`tworzenia pliku . Konstruktor nie można `PaginatedList<T>` utworzyć obiektu, konstruktory nie można uruchomić kod asynchroniczne.

## <a name="add-paging-functionality-to-the-index-method"></a>Dodawanie funkcji stronicowania do metody Indeks

W *aplikacji Studenci/Index.cshtml.cs*zaktualizuj typ `Student` od: `IList<Student>` `PaginatedList<Student>`

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Zaktualizuj *students/index.cshtml.cs* `OnGetAsync` następującym kodem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Poprzedni kod dodaje indeks strony, `sortOrder`bieżący `currentFilter` i do podpisu metody.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Wszystkie parametry są null, gdy:

* Strona jest wywoływana z linku **Uczniowie.**
* Użytkownik nie kliknął łącza stronicowania ani sortowania.

Po kliknięciu łącza stronicowania zmienna indeksu strony zawiera numer strony do wyświetlenia.

`CurrentSort`udostępnia stronie Razor z bieżącą kolejnością sortowania. Bieżąca kolejność sortowania musi być uwzględniona w łączach stronicowania, aby zachować kolejność sortowania podczas stronicowania.

`CurrentFilter`udostępnia razor Page z bieżącym ciągiem filtru. Wartość: `CurrentFilter`

* Muszą być zawarte w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania.
* Należy przywrócić do pola tekstowego, gdy strona jest ponownie świetliona.

Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, strona zostanie zresetowana do 1. Strona musi zostać zresetowana do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych. Po wprowadzeniu wartości wyszukiwania i **wybraniu opcji Prześlij:**

* Ciąg wyszukiwania zostanie zmieniony.
* Parametr `searchString` nie jest null.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

Metoda `PaginatedList.CreateAsync` konwertuje zapytanie ucznia do jednej strony uczniów w typie kolekcji, który obsługuje stronicowania. Ta pojedyncza strona uczniów jest przekazywana do strony Razor.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Dwa znaki zapytania `PaginatedList.CreateAsync` reprezentują [operatora zrzeszenie null](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operator scalania wartości null definiuje wartość domyślną dla typu możliwego do wartości null. Wyrażenie `(pageIndex ?? 1)` oznacza zwraca `pageIndex` wartość, jeśli ma wartość. Jeśli `pageIndex` nie ma wartości, zwróć 1.

## <a name="add-paging-links-to-the-student-razor-page"></a>Dodawanie łączy stronicowania do strony razor studenta

Zaktualizuj znaczniki w *pliku Students/Index.cshtml*. Zmiany są wyróżnione:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Łącza nagłówka kolumny używają ciągu zapytania do przekazywania bieżącego ciągu wyszukiwania do `OnGetAsync` metody, dzięki czemu użytkownik może sortować w wynikach filtru:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Przyciski stronicowania są wyświetlane przez pomocników znaczników:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Uruchom aplikację i przejdź do strony dla uczniów.

* Aby upewnić się, że stronicowanie działa, kliknij łącza stronicowania w różnych zamówieniach sortowania.
* Aby sprawdzić, czy stronicowanie działa poprawnie z sortowaniem i filtrowaniem, wprowadź ciąg wyszukiwania i spróbuj stronicować.

![strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

Aby lepiej zrozumieć kod:

* W *programie Students/Index.cshtml.cs*ustaw `switch (sortOrder)`punkt przerwania na .
* Dodaj zegarek `NameSort`dla `DateSort` `CurrentSort`, `Model.Student.PageIndex`, i .
* W *programie Students/Index.cshtml*ustaw `@Html.DisplayNameFor(model => model.Student[0].LastName)`punkt przerwania na .

Krok po kroku debugera.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualizowanie strony Informacje, aby wyświetlić statystyki uczniów

W tym kroku *pages/About.cshtml* jest aktualizowany, aby wyświetlić liczbę studentów, którzy zarejestrowali się dla każdej daty rejestracji. Aktualizacja używa grupowania i zawiera następujące kroki:

* Utwórz model widoku dla danych używanych przez stronę **Informacje.**
* Zaktualizuj stronę Informacje, aby użyć modelu widoku.

### <a name="create-the-view-model"></a>Tworzenie modelu widoku

Utwórz folder *SchoolViewModels* w folderze *Modele.*

W folderze *SchoolViewModels* dodaj *EnrollmentDateGroup.cs* z następującym kodem:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualizowanie modelu strony Informacje

Szablony sieci Web w ASP.NET Core 2.2 nie zawierają strony Informacje. Jeśli używasz ASP.NET Core 2.2, utwórz stronę O brzytwie.

Zaktualizuj plik *Pages/About.cshtml.cs* o następujący kod:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w `EnrollmentDateGroup` każdej grupie i przechowuje wyniki w kolekcji obiektów modelu widoku.

### <a name="modify-the-about-razor-page"></a>Modyfikowanie strony O brzytwie

Zastąp kod w pliku *Pages/About.cshtml* następującym kodem:

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

Uruchom aplikację i przejdź do strony Informacje. Liczba uczniów dla każdej daty rejestracji jest wyświetlana w tabeli.

Jeśli napotkasz problemy, których nie możesz rozwiązać, pobierz [ukończoną aplikację na tym etapie.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)

![Strona Informacje](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Debugowanie ASP.NET core 2.x źródło](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Wersja tego samouczka w YouTube](https://www.youtube.com/watch?v=MDs7PFpoMqI)

W następnym samouczku aplikacja używa migracji do aktualizowania modelu danych.

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/crud)
> [następny](xref:data/ef-rp/migrations)

::: moniker-end

