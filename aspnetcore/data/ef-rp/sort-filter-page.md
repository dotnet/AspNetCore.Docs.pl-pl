---
title: Część 3, Razor strony z EF Core w ASP.NET Core — sortowanie, filtrowanie, stronicowanie
author: rick-anderson
description: Część 3 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
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
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: e01704cb10c88f3e9442e74034f5e5d39787f300
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653896"
---
# <a name="part-3-no-locrazor-pages-with-ef-core-in-aspnet-core---sort-filter-paging"></a>Część 3, Razor strony z EF Core w ASP.NET Core — sortowanie, filtrowanie, stronicowanie

Autorzy [Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Jan P Kowalski](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Ten samouczek dodaje do stron uczniów funkcje sortowania, filtrowania i stronicowania.

Na poniższej ilustracji przedstawiono kompletną stronę. Nagłówkami kolumn są łącza do klikania, aby posortować kolumnę. Kliknij wielokrotnie nagłówek kolumny, aby przełączać się między rosnącą a malejącą kolejnością sortowania.

![Strona indeksu uczniów](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Dodaj sortowanie

Zastąp kod w obszarze *Pages/Students/index. cshtml. cs* następującym kodem, aby dodać sortowanie.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All)]

Powyższy kod ma następujące działanie:

* Wymaga dodania `using System;` .
* Dodaje właściwości, aby zawierały parametry sortowania.
* Zmienia nazwę `Student` właściwości na `Students` .
* Zastępuje kod w `OnGetAsync` metodzie.

`OnGetAsync`Metoda otrzymuje `sortOrder` parametr z ciągu zapytania w adresie URL. Adres URL i ciąg zapytania są generowane przez [pomocnika tagu zakotwiczenia](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).

`sortOrder`Parametr ma wartość `Name` lub `Date` . `sortOrder`Opcjonalnie, po którym następuje `_desc` określenie kolejności malejącej. Domyślna kolejność sortowania to Ascending.

Gdy strona indeksu zostanie zażądana od linku **uczniów** , nie ma ciągu zapytania. Studenci są wyświetlani w porządku rosnącym według nazwiska. Rosnąca kolejność według nazwiska jest `default` w `switch` instrukcji. Gdy użytkownik kliknie łącze nagłówka kolumny, odpowiednia `sortOrder` wartość jest podana w wartości ciągu zapytania.

`NameSort` i `DateSort` są używane przez Razor stronę do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Kod używa [warunkowego operatora języka C#?:](/dotnet/csharp/language-reference/operators/conditional-operator). `?:`Operator jest operatorem Trzyelementowy, przyjmuje trzy operandy. Pierwszy wiersz określa, że gdy `sortOrder` ma wartość null lub jest pusty, `NameSort` jest ustawiony na `name_desc` . Jeśli `sortOrder` wartość ***nie*** jest równa null lub pusta, `NameSort` jest ustawiona na pusty ciąg.

Te dwie instrukcje umożliwiają stronie ustawienie hiperłączy nagłówka kolumny w następujący sposób:

| Bieżący porządek sortowania   | Hiperłącze nazwisko | Hiperłącze daty |
|:--------------------:|:-------------------:|:--------------:|
| Nazwisko — rosnąco  | descending          | ascending      |
| Nazwisko malejąco | ascending           | ascending      |
| Data rosnąca       | ascending           | descending     |
| Data malejąca      | ascending           | ascending      |

Metoda używa LINQ to Entities, aby określić kolumnę, według której ma zostać wykonane sortowanie. Kod inicjuje `IQueryable<Student>` instrukcję Switch i modyfikuje ją w instrukcji switch:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Po `IQueryable` utworzeniu lub zmodyfikowaniu nie są wysyłane żadne zapytania do bazy danych. Zapytanie nie jest wykonywane, dopóki `IQueryable` obiekt nie zostanie skonwertowany do kolekcji. `IQueryable` są konwertowane do kolekcji przez wywołanie metody, takiej jak `ToListAsync` . W związku z tym `IQueryable` kod skutkuje pojedynczym zapytaniem, które nie jest wykonywane do następującej instrukcji:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` może uzyskać pełne informacje o dużej liczbie kolumn do sortowania. Aby uzyskać informacje o alternatywnym sposobie kodowania tej funkcji, zobacz [Używanie dynamicznego LINQ do uproszczenia kodu](xref:data/ef-mvc/advanced#dynamic-linq) w wersji MVC tej serii samouczków.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Dodawanie hiperłączy nagłówka kolumny do strony indeksu ucznia

Zastąp kod w *Students/index. cshtml*poniższym kodem. Zmiany są wyróżnione.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Powyższy kod ma następujące działanie:

* Dodaje hiperłącza do `LastName` nagłówków i `EnrollmentDate` .
* Program używa informacji w `NameSort` i, `DateSort` Aby skonfigurować hiperłącza z bieżącymi wartościami kolejności sortowania.
* Zmienia nagłówek strony z indeksu na uczniów.
* Zmiany `Model.Student` w `Model.Students` .

Aby sprawdzić, czy sortowanie działa:

* Uruchom aplikację i wybierz kartę **studenci** .
* Kliknij nagłówki kolumn.

## <a name="add-filtering"></a>Dodaj filtrowanie

Aby dodać filtrowanie do strony indeksu uczniów:

* Do strony zostanie dodany pole tekstowe i przycisk Prześlij Razor . Pole tekstowe dostarcza ciąg wyszukiwania na imię lub nazwisko.
* Model strony został zaktualizowany tak, aby używał wartości pola tekstowego.

### <a name="update-the-ongetasync-method"></a>Aktualizowanie metody OnGetAsync

Zastąp kod w *Students/index. cshtml. cs* następującym kodem, aby dodać filtrowanie:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=17,22,26-30)]

Powyższy kod ma następujące działanie:

* Dodaje `searchString` parametr do `OnGetAsync` metody i zapisuje wartość parametru we `CurrentFilter` właściwości. Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostało dodane w następnej sekcji.
* Dodaje do klauzuli LINQ instrukcji `Where` . `Where`Klauzula wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania. Instrukcja LINQ jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable a IEnumerable

Kod wywołuje <xref:System.Linq.Queryable.Where%2A> metodę na `IQueryable` obiekcie, a filtr jest przetwarzany na serwerze. W niektórych scenariuszach aplikacja może wywołać `Where` metodę jako metodę rozszerzenia w kolekcji w pamięci. Załóżmy na przykład, że `_context.Students` zmiany z EF Core `DbSet` do metody repozytorium, która zwraca `IEnumerable` kolekcję. Wyniki byłyby zwykle takie same, ale w niektórych przypadkach mogą być różne.

Na przykład implementacja .NET Framework `Contains` Domyślnie wykonuje porównanie z uwzględnieniem wielkości liter. W SQL Server `Contains` wielkość liter jest określana na podstawie ustawienia sortowania wystąpienia SQL Server. SQL Server domyślnie nie uwzględnia wielkości liter. Domyślna wielkość liter w programie SQLite. `ToUpper` można wywołać, aby test jawnie nie uwzględniał wielkości liter:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Poprzedni kod zagwarantuje, że w filtrze nie jest rozróżniana wielkość liter, nawet jeśli `Where` Metoda jest wywoływana w `IEnumerable` lub jest uruchamiana na komputerze SQLite.

Gdy `Contains` jest wywoływana w `IEnumerable` kolekcji, używana jest implementacja programu .NET Core. Gdy `Contains` jest wywoływana dla `IQueryable` obiektu, implementacja bazy danych jest używana.

Wywoływanie `Contains` na `IQueryable` jest zwykle preferowane ze względu na wydajność. W programie `IQueryable` Filtrowanie odbywa się przez serwer bazy danych. Jeśli `IEnumerable` zostanie utworzony pierwszy, wszystkie wiersze muszą zostać zwrócone z serwera bazy danych.

Istnieje spadek wydajności dotyczący wywoływania `ToUpper` . `ToUpper`Kod dodaje funkcję w klauzuli WHERE instrukcji TSQL SELECT. Dodana funkcja uniemożliwia Optymalizatorowi użycie indeksu. Po zainstalowaniu programu SQL jako nieuwzględniającego wielkości liter, najlepszym rozwiązaniem jest uniknięcie `ToUpper` wywołania, gdy nie jest to konieczne.

Aby uzyskać więcej informacji, zobacz [jak używać zapytania bez uwzględniania wielkości liter z dostawcą oprogramowania SQLite](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-no-locrazor-page"></a>Aktualizowanie Razor strony

Zastąp kod w obszarze *Pages/Students/index. cshtml* , aby dodać przycisk **wyszukiwania** .

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Poprzedni kod używa `<form>` [pomocnika tagów](xref:mvc/views/tag-helpers/intro) do dodawania pola tekstowego wyszukiwania i przycisku. Domyślnie `<form>` pomocnik tagów przesyła dane formularza z wpisem. W przypadku wpisu POST parametry są przesyłane w treści wiadomości HTTP, a nie w adresie URL. Gdy jest używany protokół HTTP GET, dane formularza są przesyłane w adresie URL jako ciągi zapytań. Przekazywanie danych za pomocą ciągów zapytań umożliwia użytkownikom tworzenie zakładek w adresie URL. [Wskazówki dotyczące W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) zaleca się, aby pobieranie nie było wynikiem aktualizacji.

Przetestuj aplikację:

* Wybierz kartę **studenci** i wprowadź ciąg wyszukiwania. Jeśli używasz oprogramowania SQLite, filtr nie uwzględnia wielkości liter tylko w przypadku zaimplementowania `ToUpper` wcześniej pokazanego kodu.

* Wybierz pozycję **Wyszukaj**.

Zwróć uwagę, że adres URL zawiera ciąg wyszukiwania. Na przykład:

```browser-address-bar
https://localhost:5001/Students?SearchString=an
```

Jeśli strona jest zakładką, zakładka zawiera adres URL strony i `SearchString` ciąg zapytania. `method="get"`W `form` tagu jest co spowodowało wygenerowanie ciągu zapytania.

Obecnie gdy jest zaznaczone łącze Sortuj w nagłówku kolumny, wartość filtru w polu **wyszukiwania** zostanie utracona. Wartość filtru utracony została rozwiązany w następnej sekcji.

## <a name="add-paging"></a>Dodaj stronicowanie

W tej sekcji `PaginatedList` Klasa została utworzona w celu obsługi stronicowania. `PaginatedList`Klasa używa `Skip` instrukcji and `Take` do filtrowania danych na serwerze, zamiast pobierać wszystkie wiersze tabeli. Na poniższej ilustracji przedstawiono przyciski stronicowania.

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Tworzenie klasy PaginatedList

W folderze projektu Utwórz `PaginatedList.cs` przy użyciu następującego kodu:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

`CreateAsync`Metoda w poprzednim kodzie Pobiera rozmiar strony i numer strony oraz stosuje odpowiednie `Skip` `Take` instrukcje i do `IQueryable` . Gdy `ToListAsync` jest wywoływana na `IQueryable` , zwraca listę zawierającą tylko żądaną stronę. Właściwości `HasPreviousPage` i `HasNextPage` są używane do włączania lub wyłączania **poprzednich** i **następnych** przycisków stronicowania.

`CreateAsync`Metoda jest używana do tworzenia `PaginatedList<T>` . Konstruktor nie może utworzyć `PaginatedList<T>` obiektu; konstruktory nie mogą uruchamiać kodu asynchronicznego.

### <a name="add-paging-to-the-pagemodel-class"></a>Dodawanie stronicowania do klasy PageModel

Zastąp kod w *Students/index. cshtml. cs* , aby dodać stronicowanie.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=15-20,23-30,57-59)]

Powyższy kod ma następujące działanie:

* Zmienia typ `Students` właściwości z `IList<Student>` na `PaginatedList<Student>` .
* Dodaje indeks strony, bieżącą `sortOrder` i `currentFilter` do `OnGetAsync` sygnatury metody.
* Zapisuje porządek sortowania we `CurrentSort` właściwości.
* Resetuje indeks strony do 1, gdy istnieje nowy ciąg wyszukiwania.
* Używa `PaginatedList` klasy do uzyskiwania jednostek ucznia.
* Ustawia `pageSize` na 3. Rzeczywista aplikacja użyje [konfiguracji](xref:fundamentals/configuration/index) w celu ustawienia wartości rozmiaru strony.

Wszystkie `OnGetAsync` otrzymane parametry mają wartość null, gdy:

* Strona jest wywoływana z linku **Students** .
* Użytkownik nie kliknął linku do stronicowania ani sortowania.

Po kliknięciu łącza stronicowania zmienna indeksu strony zawiera numer strony do wyświetlenia.

`CurrentSort`Właściwość zawiera Razor stronę z bieżącą kolejnością sortowania. Bieżąca kolejność sortowania musi być uwzględniona w łączach stronicowania, aby zachować porządek sortowania podczas stronicowania.

`CurrentFilter`Właściwość zawiera Razor stronę z bieżącym ciągiem filtru. `CurrentFilter`Wartość:

* Musi być uwzględniony w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania.
* Musi zostać przywrócone do pola tekstowego, gdy strona jest ponownie wyświetlana.

Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, Strona zostanie zresetowana do 1. Strona musi zostać zresetowana do 1, ponieważ nowy filtr może spowodować wyświetlenie innych danych. Gdy **zostanie wprowadzona wartość wyszukiwania i** zaznaczono:

  * Ciąg wyszukiwania został zmieniony.
  * `searchString`Parametr nie ma wartości null.

  `PaginatedList.CreateAsync`Metoda konwertuje studenta zapytania na jedną stronę uczniów w typie kolekcji, który obsługuje stronicowanie. Ta pojedyncza strona studentów jest przenoszona na Razor stronę.

  Dwa znaki zapytania po `pageIndex` `PaginatedList.CreateAsync` wywołaniu reprezentują [operator łączenia wartości null](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operator łączenia wartości null definiuje wartość domyślną dla typu dopuszczającego wartość null. Wyrażenie `pageIndex ?? 1` zwraca wartość `pageIndex` , jeśli ma wartość, w przeciwnym razie zwraca 1.

### <a name="add-paging-links-to-the-no-locrazor-page"></a>Dodaj linki stronicowania do Razor strony

Zastąp kod w *Students/index. cshtml* poniższym kodem. Zmiany są wyróżnione:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Linki nagłówka kolumny używają ciągu zapytania do przekazywania bieżącego ciągu wyszukiwania do `OnGetAsync` metody:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Przyciski stronicowania są wyświetlane przez pomocników tagów:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Uruchom aplikację i przejdź do strony uczniów.

* Aby upewnić się, że stronicowanie działa, kliknij linki stronicowania w różnych kolejności sortowania.
* Aby sprawdzić, czy stronicowanie działa prawidłowo z sortowaniem i filtrowaniem, wprowadź ciąg wyszukiwania i spróbuj stronicować.

![strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Dodaj grupowanie

Ta sekcja zawiera informacje na temat strony zawierającej liczbę studentów zarejestrowanych dla każdej daty rejestracji. Aktualizacja używa grupowania i obejmuje następujące kroki:

* Utwórz model widoku dla danych używanych przez stronę **informacje** .
* Zaktualizuj stronę informacje, aby użyć modelu widoku.

### <a name="create-the-view-model"></a>Tworzenie modelu widoku

Utwórz folder *models/SchoolViewModels* .

Utwórz *SchoolViewModels/EnrollmentDateGroup. cs* przy użyciu następującego kodu:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-no-locrazor-page"></a>Utwórz Razor stronę

Utwórz plik *Pages/about. cshtml* o następującym kodzie:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Tworzenie modelu strony

Zaktualizuj plik *Pages/about. Odpoznaj* się do następującego kodu:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w każdej grupie i zapisuje wyniki w kolekcji `EnrollmentDateGroup` obiektów modelu widoku.

Uruchom aplikację i przejdź do strony informacje. W tabeli zostanie wyświetlona liczba uczniów dla każdej daty rejestracji.

![Informacje o stronie](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Następne kroki

W następnym samouczku aplikacja będzie używać migracji w celu zaktualizowania modelu danych.

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/crud) 
>  [Następny samouczek](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku zostaną dodane funkcje sortowania, filtrowania, grupowania i stronicowania.

Na poniższej ilustracji przedstawiono kompletną stronę. Nagłówkami kolumn są łącza do klikania, aby posortować kolumnę. Kliknięcie nagłówka kolumny powoduje kilkakrotne przełączenie między rosnącą i malejącą kolejnością sortowania.

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

Jeśli wystąpią problemy, których nie można rozwiązać, Pobierz [ukończoną aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Dodawanie sortowania do strony indeksu

Dodaj ciągi do *uczniów/index. cshtml. cs* , `PageModel` aby zawierały parametry sortowania:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Zaktualizuj *uczniów/index. cshtml. cs* `OnGetAsync` przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Poprzedni kod otrzymuje `sortOrder` parametr z ciągu zapytania w adresie URL. Adres URL (łącznie z ciągiem zapytania) jest generowany przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

`sortOrder`Parametr ma wartość "name" lub "date". `sortOrder`Parametr jest opcjonalny, po którym następuje "_desc", aby określić kolejność malejącą. Domyślna kolejność sortowania to Ascending.

Gdy strona indeksu zostanie zażądana od linku **uczniów** , nie ma ciągu zapytania. Studenci są wyświetlani w porządku rosnącym według nazwiska. Kolejność rosnąca według nazwiska jest wartością domyślną (w przypadku `switch` przyciągania) w instrukcji. Gdy użytkownik kliknie łącze nagłówka kolumny, odpowiednia `sortOrder` wartość jest podana w wartości ciągu zapytania.

`NameSort` i `DateSort` są używane przez Razor stronę do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Poniższy kod zawiera operator Conditional języka C# [?:](/dotnet/csharp/language-reference/operators/conditional-operator)

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

Pierwszy wiersz określa, że gdy `sortOrder` ma wartość null lub jest pusty, `NameSort` jest ustawiony na wartość "name_desc". Jeśli `sortOrder` wartość **nie** jest równa null lub pusta, `NameSort` jest ustawiona na pusty ciąg.

`?: operator`Jest również znany jako operator Trzyelementowy.

Te dwie instrukcje umożliwiają stronie ustawienie hiperłączy nagłówka kolumny w następujący sposób:

| Bieżący porządek sortowania | Hiperłącze nazwisko | Hiperłącze daty |
|:--------------------:|:-------------------:|:--------------:|
| Nazwisko — rosnąco | descending        | ascending      |
| Nazwisko malejąco | ascending           | ascending      |
| Data rosnąca       | ascending           | descending     |
| Data malejąca      | ascending           | ascending      |

Metoda używa LINQ to Entities, aby określić kolumnę, według której ma zostać wykonane sortowanie. Kod inicjuje `IQueryable<Student>` instrukcję Switch i modyfikuje ją w instrukcji switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Po `IQueryable` utworzeniu lub zmodyfikowaniu nie są wysyłane żadne zapytania do bazy danych. Zapytanie nie jest wykonywane, dopóki `IQueryable` obiekt nie zostanie skonwertowany do kolekcji. `IQueryable` są konwertowane do kolekcji przez wywołanie metody, takiej jak `ToListAsync` . W związku z tym `IQueryable` kod skutkuje pojedynczym zapytaniem, które nie jest wykonywane do następującej instrukcji:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` może uzyskać pełne informacje o dużej liczbie kolumn do sortowania.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Dodawanie hiperłączy nagłówka kolumny do strony indeksu ucznia

Zastąp kod w *Students/index. cshtml*następującym wyróżnionym kodem:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Powyższy kod ma następujące działanie:

* Dodaje hiperłącza do `LastName` nagłówków i `EnrollmentDate` .
* Program używa informacji w `NameSort` i, `DateSort` Aby skonfigurować hiperłącza z bieżącymi wartościami kolejności sortowania.

Aby sprawdzić, czy sortowanie działa:

* Uruchom aplikację i wybierz kartę **studenci** .
* Kliknij pozycję **nazwisko**.
* Kliknij pozycję **Data rejestracji**.

Aby lepiej zrozumieć kod:

* W polu *studenci/index. cshtml. cs*Ustaw punkt przerwania na `switch (sortOrder)` .
* Dodaj czujkę dla `NameSort` i `DateSort` .
* W obszarze *uczniowie/index. cshtml*Ustaw punkt przerwania na `@Html.DisplayNameFor(model => model.Student[0].LastName)` .

Przechodzenie przez debuger.

## <a name="add-a-search-box-to-the-students-index-page"></a>Dodawanie pola wyszukiwania do strony indeksu uczniów

Aby dodać filtrowanie do strony indeksu uczniów:

* Do strony zostanie dodany pole tekstowe i przycisk Prześlij Razor . Pole tekstowe dostarcza ciąg wyszukiwania na imię lub nazwisko.
* Model strony został zaktualizowany tak, aby używał wartości pola tekstowego.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dodawanie funkcji filtrowania do metody index

Zaktualizuj *uczniów/index. cshtml. cs* `OnGetAsync` przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Powyższy kod ma następujące działanie:

* Dodaje `searchString` parametr do `OnGetAsync` metody. Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostało dodane w następnej sekcji.
* Dodano do klauzuli LINQ instrukcji `Where` . `Where`Klauzula wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania. Instrukcja LINQ jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.

Uwaga: Poprzedni kod wywołuje `Where` metodę na `IQueryable` obiekcie, a filtr jest przetwarzany na serwerze. W niektórych scenariuszach aplikacja może wywołać `Where` metodę jako metodę rozszerzenia w kolekcji w pamięci. Załóżmy na przykład, że `_context.Students` zmiany z EF Core `DbSet` do metody repozytorium, która zwraca `IEnumerable` kolekcję. Wyniki byłyby zwykle takie same, ale w niektórych przypadkach mogą być różne.

Na przykład implementacja .NET Framework `Contains` Domyślnie wykonuje porównanie z uwzględnieniem wielkości liter. W SQL Server `Contains` wielkość liter jest określana na podstawie ustawienia sortowania wystąpienia SQL Server. SQL Server domyślnie nie uwzględnia wielkości liter. `ToUpper` można wywołać, aby test jawnie nie uwzględniał wielkości liter:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Poprzedni kod gwarantuje, że w wynikach nie jest rozróżniana wielkość liter, jeśli kod ulegnie zmianie `IEnumerable` . Gdy `Contains` jest wywoływana w `IEnumerable` kolekcji, używana jest implementacja programu .NET Core. Gdy `Contains` jest wywoływana dla `IQueryable` obiektu, implementacja bazy danych jest używana. Zwrócenie `IEnumerable` z repozytorium może mieć znaczny spadek wydajności:

1. Wszystkie wiersze są zwracane z serwera bazy danych.
1. Filtr jest stosowany do wszystkich zwracanych wierszy w aplikacji.

Istnieje spadek wydajności dotyczący wywoływania `ToUpper` . `ToUpper`Kod dodaje funkcję w klauzuli WHERE instrukcji TSQL SELECT. Dodana funkcja uniemożliwia Optymalizatorowi użycie indeksu. Po zainstalowaniu programu SQL jako nieuwzględniającego wielkości liter, najlepszym rozwiązaniem jest uniknięcie `ToUpper` wywołania, gdy nie jest to konieczne.

### <a name="add-a-search-box-to-the-student-index-page"></a>Dodawanie pola wyszukiwania do strony indeksu uczniów

W obszarze *Pages/Students/index. cshtml*Dodaj następujący wyróżniony kod w celu utworzenia przycisku **wyszukiwania** i elementów Chrome.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Poprzedni kod używa `<form>` [pomocnika tagów](xref:mvc/views/tag-helpers/intro) do dodawania pola tekstowego wyszukiwania i przycisku. Domyślnie `<form>` pomocnik tagów przesyła dane formularza z wpisem. W przypadku wpisu POST parametry są przesyłane w treści wiadomości HTTP, a nie w adresie URL. Gdy jest używany protokół HTTP GET, dane formularza są przesyłane w adresie URL jako ciągi zapytań. Przekazywanie danych za pomocą ciągów zapytań umożliwia użytkownikom tworzenie zakładek w adresie URL. [Wskazówki dotyczące W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) zaleca się, aby pobieranie nie było wynikiem aktualizacji.

Przetestuj aplikację:

* Wybierz kartę **studenci** i wprowadź ciąg wyszukiwania.
* Wybierz pozycję **Wyszukaj**.

Zwróć uwagę, że adres URL zawiera ciąg wyszukiwania.

```html
http://localhost:5000/Students?SearchString=an
```

Jeśli strona jest zakładką, zakładka zawiera adres URL strony i `SearchString` ciąg zapytania. `method="get"`W `form` tagu jest co spowodowało wygenerowanie ciągu zapytania.

Obecnie gdy jest zaznaczone łącze Sortuj w nagłówku kolumny, wartość filtru w polu **wyszukiwania** zostanie utracona. Wartość filtru utracony została rozwiązany w następnej sekcji.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Dodawanie funkcji stronicowania do strony indeksu uczniów

W tej sekcji `PaginatedList` Klasa została utworzona w celu obsługi stronicowania. `PaginatedList`Klasa używa `Skip` instrukcji and `Take` do filtrowania danych na serwerze, zamiast pobierać wszystkie wiersze tabeli. Na poniższej ilustracji przedstawiono przyciski stronicowania.

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

W folderze projektu Utwórz `PaginatedList.cs` przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

`CreateAsync`Metoda w poprzednim kodzie Pobiera rozmiar strony i numer strony oraz stosuje odpowiednie `Skip` `Take` instrukcje i do `IQueryable` . Gdy `ToListAsync` jest wywoływana na `IQueryable` , zwraca listę zawierającą tylko żądaną stronę. Właściwości `HasPreviousPage` i `HasNextPage` są używane do włączania lub wyłączania **poprzednich** i **następnych** przycisków stronicowania.

`CreateAsync`Metoda jest używana do tworzenia `PaginatedList<T>` . Konstruktor nie może utworzyć `PaginatedList<T>` obiektu, konstruktory nie mogą uruchamiać kodu asynchronicznego.

## <a name="add-paging-functionality-to-the-index-method"></a>Dodawanie funkcji stronicowania do metody index

W obszarze *uczniowie/index. cshtml. cs*zaktualizuj typ `Student` z `IList<Student>` do `PaginatedList<Student>` :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Zaktualizuj *uczniów/index. cshtml. cs* `OnGetAsync` przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Poprzedni kod dodaje indeks strony, bieżącą `sortOrder` i `currentFilter` do sygnatury metody.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Wszystkie parametry mają wartość null, gdy:

* Strona jest wywoływana z linku **Students** .
* Użytkownik nie kliknął linku do stronicowania ani sortowania.

Po kliknięciu łącza stronicowania zmienna indeksu strony zawiera numer strony do wyświetlenia.

`CurrentSort` udostępnia Razor stronę z bieżącą kolejnością sortowania. Bieżąca kolejność sortowania musi być uwzględniona w łączach stronicowania, aby zachować porządek sortowania podczas stronicowania.

`CurrentFilter` udostępnia Razor stronę z bieżącym ciągiem filtru. `CurrentFilter`Wartość:

* Musi być uwzględniony w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania.
* Musi zostać przywrócone do pola tekstowego, gdy strona jest ponownie wyświetlana.

Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, Strona zostanie zresetowana do 1. Strona musi zostać zresetowana do 1, ponieważ nowy filtr może spowodować wyświetlenie innych danych. Gdy **zostanie wprowadzona wartość wyszukiwania i** zaznaczono:

* Ciąg wyszukiwania został zmieniony.
* `searchString`Parametr nie ma wartości null.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync`Metoda konwertuje studenta zapytania na jedną stronę uczniów w typie kolekcji, który obsługuje stronicowanie. Ta pojedyncza strona studentów jest przenoszona na Razor stronę.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Dwa znaki zapytania `PaginatedList.CreateAsync` reprezentują [operator łączenia wartości null](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operator łączenia wartości null definiuje wartość domyślną dla typu dopuszczającego wartość null. Wyrażenie `(pageIndex ?? 1)` oznacza zwrócenie wartości, `pageIndex` Jeśli ma wartość. Jeśli `pageIndex` nie ma wartości, zwróć 1.

## <a name="add-paging-links-to-the-student-no-locrazor-page"></a>Dodawanie linków stronicowania do strony ucznia Razor

Zaktualizuj znaczniki w *uczniów/index. cshtml*. Zmiany są wyróżnione:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Linki nagłówka kolumny używają ciągu zapytania do przekazywania bieżącego ciągu wyszukiwania do `OnGetAsync` metody, aby użytkownik mógł sortować wyniki filtru:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Przyciski stronicowania są wyświetlane przez pomocników tagów:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Uruchom aplikację i przejdź do strony uczniów.

* Aby upewnić się, że stronicowanie działa, kliknij linki stronicowania w różnych kolejności sortowania.
* Aby sprawdzić, czy stronicowanie działa prawidłowo z sortowaniem i filtrowaniem, wprowadź ciąg wyszukiwania i spróbuj stronicować.

![strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

Aby lepiej zrozumieć kod:

* W polu *studenci/index. cshtml. cs*Ustaw punkt przerwania na `switch (sortOrder)` .
* Dodaj czujkę dla `NameSort` , `DateSort` , `CurrentSort` , i `Model.Student.PageIndex` .
* W obszarze *uczniowie/index. cshtml*Ustaw punkt przerwania na `@Html.DisplayNameFor(model => model.Student[0].LastName)` .

Przechodzenie przez debuger.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualizowanie strony informacje w celu wyświetlenia statystyk uczniów

W tym kroku zostaną zaktualizowane *strony/informacje. cshtml* , aby wyświetlić liczbę uczniów zarejestrowanych dla każdej daty rejestracji. Aktualizacja używa grupowania i obejmuje następujące kroki:

* Utwórz model widoku dla danych używanych przez stronę **informacje** .
* Zaktualizuj stronę informacje, aby użyć modelu widoku.

### <a name="create-the-view-model"></a>Tworzenie modelu widoku

Utwórz folder *SchoolViewModels* w folderze *models* .

W folderze *SchoolViewModels* Dodaj *EnrollmentDateGroup.cs* o następującym kodzie:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualizowanie modelu strony informacji o

Szablony sieci Web w ASP.NET Core 2,2 nie zawierają strony informacje. Jeśli używasz ASP.NET Core 2,2, Utwórz Razor stronę informacje.

Zaktualizuj plik *Pages/about. Odpoznaj* się do następującego kodu:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w każdej grupie i zapisuje wyniki w kolekcji `EnrollmentDateGroup` obiektów modelu widoku.

### <a name="modify-the-about-no-locrazor-page"></a>Modyfikowanie strony informacje Razor

Zastąp kod w pliku *Pages/about. cshtml* następującym kodem:

[!code-cshtml[](intro/samples/cu21/Pages/About.cshtml)]

Uruchom aplikację i przejdź do strony informacje. W tabeli zostanie wyświetlona liczba uczniów dla każdej daty rejestracji.

Jeśli wystąpią problemy, których nie można rozwiązać, Pobierz [ukończoną aplikację dla tego etapu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Informacje o stronie](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Debugowanie ASP.NET Core 2. x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=MDs7PFpoMqI)

W następnym samouczku aplikacja będzie używać migracji w celu zaktualizowania modelu danych.

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/crud) 
>  [Dalej](xref:data/ef-rp/migrations)

::: moniker-end

