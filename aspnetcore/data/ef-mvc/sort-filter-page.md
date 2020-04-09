---
title: 'Samouczek: Dodawanie sortowania, filtrowania i stronicowania - ASP.NET MVC z EF Core'
description: W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania do strony Indeks uczniów. Utworzysz również stronę, która wykonuje proste grupowanie.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 99bf9ed59b47e8fbba838b97c3e032b9808f6a94
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657137"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Samouczek: Dodawanie sortowania, filtrowania i stronicowania - ASP.NET MVC z EF Core

W poprzednim samouczku zaimplementowano zestaw stron sieci web dla podstawowych operacji CRUD dla jednostek studenta. W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania do strony Indeks uczniów. Utworzysz również stronę, która wykonuje proste grupowanie.

Na poniższej ilustracji przedstawiono, jak strona będzie wyglądać po zakończeniu. Nagłówki kolumn są łączami, które użytkownik może kliknąć, aby posortować według tej kolumny. Kliknięcie nagłówka kolumny wielokrotnie przełącza się między kolejnością sortowania rosnącego i malejącego.

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dodawanie łączy sortowania kolumn
> * Dodawanie pola wyszukiwania
> * Dodawanie stronicowania do indeksu uczniów
> * Dodawanie stronicowania do metody indeksu
> * Dodawanie łączy stronicowania
> * Tworzenie strony Informacje

## <a name="prerequisites"></a>Wymagania wstępne

* [Wdrażanie funkcji CRUD](crud.md)

## <a name="add-column-sort-links"></a>Dodawanie łączy sortowania kolumn

Aby dodać sortowanie do strony Indeks uczniów, `Index` zmienisz metodę kontrolera Studenci i dodasz kod do widoku Indeks uczniów.

### <a name="add-sorting-functionality-to-the-index-method"></a>Dodawanie funkcji sortowania do metody Indeks

W *StudentsController.cs*, zastąp `Index` metodę następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Ten kod `sortOrder` odbiera parametr z ciągu zapytania w adresie URL. Wartość ciągu zapytania jest dostarczana przez ASP.NET Core MVC jako parametr metody akcji. Parametr będzie ciągiem, który jest "Name" lub "Date", opcjonalnie następuje podkreślenie i ciąg "desc", aby określić malejącą kolejność. Domyślna kolejność sortowania jest rosnąca.

Po pierwszym żądaniu strony indeksu nie ma ciągu zapytania. Uczniowie są wyświetlani w porządku rosnącym według nazwiska, co jest domyślne, jak `switch` ustalono w przypadku fall-through w oświadczeniu. Gdy użytkownik kliknie hiperłącze nagłówka kolumny, odpowiednia `sortOrder` wartość jest podana w ciągu zapytania.

Dwa `ViewData` elementy (NameSortParm i DateSortParm) są używane przez widok do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Są to oświadczenia trójskładniowe. Pierwszy z nich określa, `sortOrder` że jeśli parametr jest null lub empty, NameSortParm należy ustawić na "name_desc"; w przeciwnym razie należy ustawić na pusty ciąg. Te dwie instrukcje umożliwiają widokowi ustawienie hiperłączy nagłówka kolumny w następujący sposób:

|  Bieżąca kolejność sortowania  | Hiperłącze do nazwiska | Daty Hiperłącze |
|:--------------------:|:-------------------:|:--------------:|
| Nazwisko rosnące  | descending          | ascending      |
| Nazwisko malejące | ascending           | ascending      |
| Data rosnąca       | ascending           | descending     |
| Data malejąco      | ascending           | ascending      |

Metoda używa LINQ do jednostek, aby określić kolumnę do sortowania według. Kod tworzy `IQueryable` zmienną przed switch instrukcji, modyfikuje go w `ToListAsync` instrukcji `switch` switch i wywołuje metodę po instrukcji. Podczas tworzenia i `IQueryable` modyfikowania zmiennych do bazy danych nie jest wysyłana żadna kwerenda. Kwerenda nie jest wykonywana, `IQueryable` dopóki obiekt nie zostanie przekonwertowany na kolekcję, wywołując metodę, taką jak `ToListAsync`. W związku z tym ten kod powoduje pojedyncze `return View` zapytanie, które nie jest wykonywane, dopóki instrukcja.

Ten kod może uzyskać pełne z dużą liczbę kolumn. [Ostatni samouczek z tej serii](advanced.md#dynamic-linq) pokazuje, jak napisać `OrderBy` kod, który pozwala przekazać nazwę kolumny w zmiennej ciągu.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Dodawanie hiperłączy nagłówka kolumny do widoku Indeks ucznia

Zastąp kod w *widokach/uczniach/index.cshtml*, następującym kodem, aby dodać hiperłącza nagłówków kolumn. Zmienione linie są wyróżnione.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Ten kod używa informacji `ViewData` we właściwościach do konfigurowania hiperłączy z odpowiednimi wartościami ciągu zapytania.

Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij nagłówki kolumn **Nazwisko** i **Data rejestracji,** aby sprawdzić, czy sortowanie działa.

![Strona indeksu uczniów w kolejności nazw](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Dodawanie pola wyszukiwania

Aby dodać filtrowanie do strony Indeks uczniów, dodasz do widoku pole tekstowe i `Index` przycisk prześlij i wniesiesz odpowiednie zmiany w tej metodzie. Pole tekstowe umożliwia wprowadzenie ciągu do wyszukania w polach imienia i nazwiska.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dodawanie funkcji filtrowania do metody Indeks

W *StudentsController.cs*, zastąp `Index` metodę następującym kodem (zmiany są wyróżnione).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Dodano `searchString` parametr do `Index` metody. Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostanie dodane do widoku indeksu. Dodano również do instrukcji LINQ klauzulę where, która wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania. Instrukcja, która dodaje where klauzuli jest wykonywany tylko wtedy, gdy istnieje wartość do wyszukania.

> [!NOTE]
> W tym miejscu `Where` wywołujesz `IQueryable` metodę na obiekcie, a filtr zostanie przetworzony na serwerze. W niektórych scenariuszach może `Where` być wywołanie metody jako metody rozszerzenia w kolekcji w pamięci. (Na przykład załóżmy, `_context.Students` że odwołanie do tak, aby zamiast EF `DbSet` odwołuje się `IEnumerable` do metody repozytorium, która zwraca kolekcję.) Wynik byłby zwykle taki sam, ale w niektórych przypadkach może być inny.
>
>Na przykład implementacja .NET `Contains` Framework metody wykonuje porównanie z uwzględnieniem wielkości liter domyślnie, ale w programie SQL Server jest to określane przez ustawienie sortowania wystąpienia programu SQL Server. To ustawienie domyślnie jest niewrażliwe na wielkości liter. Można wywołać `ToUpper` metodę, aby test jawnie bez uwzględniania wielkości liter: *Where(s => s.LastName.ToUpper(). Contains(searchString.ToUpper())*. Dzięki temu wyniki pozostaną takie same, jeśli później zmienisz kod, `IEnumerable` aby użyć repozytorium, które zwraca kolekcję zamiast obiektu. `IQueryable` (Po wywołaniu `Contains` metody `IEnumerable` w kolekcji, otrzymasz .NET Framework implementacji; po wywołaniu go na obiekcie, `IQueryable` otrzymasz implementacji dostawcy bazy danych.) Istnieje jednak kara za wydajność dla tego rozwiązania. Kod `ToUpper` będzie umieścić funkcję w klauzuli WHERE instrukcji TSQL SELECT. Uniemożliwiłoby to optymalizatora przy użyciu indeksu. Biorąc pod uwagę, że SQL jest najczęściej instalowany jako bez `ToUpper` uwzględniania wielkości liter, najlepiej unikać kodu, dopóki nie zostanie migrowany do magazynu danych z uwzględnieniem wielkości liter.

### <a name="add-a-search-box-to-the-student-index-view"></a>Dodawanie pola wyszukiwania do widoku indeksu ucznia

W *obszarze Widoki/Student/Index.cshtml*dodaj wyróżniony kod bezpośrednio przed tagiem tabeli otwierającej, aby utworzyć podpis, pole tekstowe i przycisk **Wyszukaj.**

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Ten kod używa `<form>` [pomocnika tagu,](xref:mvc/views/tag-helpers/intro) aby dodać pole tekstowe wyszukiwania i przycisk. Domyślnie pomocnik `<form>` tagu przesyła dane formularza z post, co oznacza, że parametry są przekazywane w treści wiadomości HTTP, a nie w adresie URL jako ciągi zapytania. Po określeniu protokołu HTTP GET dane formularza są przekazywane w adresie URL jako ciągi zapytań, co umożliwia użytkownikom dodawanie do zakładek adresu URL. Wytyczne W3C zaleca się, aby używać GET, gdy akcja nie powoduje aktualizacji.

Uruchom aplikację, wybierz kartę **Uczniowie,** wprowadź ciąg wyszukiwania i kliknij przycisk Wyszukaj, aby sprawdzić, czy filtrowanie działa.

![Strona indeksowania uczniów z filtrem](sort-filter-page/_static/filtering.png)

Należy zauważyć, że adres URL zawiera ciąg wyszukiwania.

```html
http://localhost:5813/Students?SearchString=an
```

Jeśli dodasz tę stronę do zakładek, otrzymasz filtrowane listy podczas korzystania z zakładki. Dodanie `method="get"` do `form` tagu jest przyczyną ciągu zapytania do wygenerowania.

Na tym etapie kliknięcie łącza do sortowania nagłówka kolumny spowoduje utratę wartości filtru wprowadzonej w polu **Wyszukiwania.** Naprawisz to w następnej sekcji.

## <a name="add-paging-to-students-index"></a>Dodawanie stronicowania do indeksu uczniów

Aby dodać stronicowanie do strony Indeks uczniów, utworzysz `PaginatedList` klasę, która używa `Skip` i `Take` instrukcji do filtrowania danych na serwerze, zamiast zawsze pobierać wszystkie wiersze tabeli. Następnie należy wprowadzić dodatkowe zmiany `Index` w metodzie i `Index` dodać przyciski stronicowania do widoku. Na poniższej ilustracji przedstawiono przyciski stronicowania.

![Strona indeksu uczniów z łączami stronicowania](sort-filter-page/_static/paging.png)

W folderze projektu `PaginatedList.cs`utwórz , a następnie zastąp kod szablonu następującym kodem.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

Metoda `CreateAsync` w tym kodzie przyjmuje rozmiar strony i `Skip` `Take` numer strony `IQueryable`i stosuje odpowiednie instrukcje do . Po `ToListAsync` wywołaniu `IQueryable`na programie zwraca listę zawierającą tylko żądaną stronę. Właściwości `HasPreviousPage` i `HasNextPage` może służyć do włączania lub wyłączania **poprzedni** i **następny** przyciski stronicowania.

Metoda `CreateAsync` jest używana zamiast konstruktora `PaginatedList<T>` do tworzenia obiektu, ponieważ konstruktory nie można uruchomić kodu asynchroniczne.

## <a name="add-paging-to-index-method"></a>Dodawanie stronicowania do metody indeksu

W *StudentsController.cs*, zastąp `Index` metodę następującym kodem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Ten kod dodaje parametr numeru strony, parametr bieżącej kolejności sortowania i bieżący parametr filtru do podpisu metody.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Przy pierwszym wyświetleniu strony lub jeśli użytkownik nie kliknął łącza stronicowania lub sortowania, wszystkie parametry będą zerowe.  Jeśli kliknie łącze stronicowania, zmienna strony będzie zawierać numer strony do wyświetlenia.

Element `ViewData` o nazwie CurrentSort zapewnia widok z bieżącej kolejności sortowania, ponieważ musi to być zawarte w łączach stronicowania, aby zachować kolejność sortowania tak samo podczas stronicowania.

Element `ViewData` o nazwie CurrentFilter udostępnia widok z bieżącym ciągiem filtru. Ta wartość musi być uwzględniona w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania, i musi zostać przywrócona do pola tekstowego po ponownym wyświetleniu strony.

Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, strona musi zostać zresetowana do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych. Ciąg wyszukiwania jest zmieniany po wprowadzeniu wartości w polu tekstowym i naciśnięciu przycisku Prześlij. W takim przypadku `searchString` parametr nie jest null.

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

Na końcu `Index` metody `PaginatedList.CreateAsync` konwertuje zapytanie ucznia do jednej strony uczniów w typie kolekcji, który obsługuje stronicowania. Ta pojedyncza strona uczniów jest następnie przekazywana do widoku.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

Metoda `PaginatedList.CreateAsync` przyjmuje numer strony. Dwa znaki zapytania reprezentują operatora zrzeszania się wartości null. Operator scalania wartości null definiuje wartość domyślną dla typu możliwego do wartości null; wyrażenie `(pageNumber ?? 1)` oznacza zwraca `pageNumber` wartość, jeśli ma wartość lub zwraca `pageNumber` 1, jeśli jest null.

## <a name="add-paging-links"></a>Dodawanie łączy stronicowania

W *views/students/index.cshtml*, zastąp istniejący kod następującym kodem. Zmiany są wyróżnione.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Instrukcja `@model` w górnej części strony określa, że `PaginatedList<T>` widok pobiera teraz `List<T>` obiekt zamiast obiektu.

Łącza nagłówka kolumny używają ciągu zapytania do przekazywania bieżącego ciągu wyszukiwania do kontrolera, aby użytkownik mógł sortować w wynikach filtru:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Przyciski stronicowania są wyświetlane przez pomocników znaczników:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Uruchom aplikację i przejdź do strony Uczniowie.

![Strona indeksu uczniów z łączami stronicowania](sort-filter-page/_static/paging.png)

Kliknij łącza stronicowania w różnych zamówieniach sortowania, aby upewnić się, że stronicowanie działa. Następnie wprowadź ciąg wyszukiwania i spróbuj ponownie stronicować, aby sprawdzić, czy stronicowanie działa poprawnie również z sortowaniem i filtrowaniem.

## <a name="create-an-about-page"></a>Tworzenie strony Informacje

Na stronie **Informacje o** witrynie Uniwersytetu Contoso zostanie wyświetlona liczba studentów, którzy zarejestrowali się dla każdej daty rejestracji. Wymaga to grupowania i prostych obliczeń na grupach. Aby to osiągnąć, wykonaj następujące czynności:

* Utwórz klasę modelu widoku dla danych, które należy przekazać do widoku.
* Utwórz ochocie metody w kontrolerze macierzystym.
* Utwórz widok Informacje.

### <a name="create-the-view-model"></a>Tworzenie modelu widoku

Utwórz folder *SchoolViewModels* w folderze *Modele.*

W nowym folderze dodaj plik klasy *EnrollmentDateGroup.cs* i zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Modyfikowanie kontrolera głównego

W *HomeController.cs*, dodaj następujące instrukcje w górnej części pliku:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Dodaj zmienną klasy dla kontekstu bazy danych natychmiast po otwarciu nawiasu klamrowego dla klasy i uzyskać wystąpienie kontekstu z ASP.NET Core DI:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Dodaj `About` metodę z następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w `EnrollmentDateGroup` każdej grupie i przechowuje wyniki w kolekcji obiektów modelu widoku.

### <a name="create-the-about-view"></a>Tworzenie widoku Informacje

Dodaj plik *Views/Home/About.cshtml* z następującym kodem:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Uruchom aplikację i przejdź do strony Informacje. Liczba uczniów dla każdej daty rejestracji jest wyświetlana w tabeli.

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dodano łącza sortowania kolumn
> * Dodano pole wyszukiwania
> * Dodano stronicowanie do indeksu uczniów
> * Dodano stronicowanie do metody indeksu
> * Dodano łącza stronicowania
> * Utworzono stronę Informacje

Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać zmiany modelu danych przy użyciu migracji.

> [!div class="nextstepaction"]
> [Dalej: Obsługa zmian modelu danych](migrations.md)
