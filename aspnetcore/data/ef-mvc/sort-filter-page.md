---
title: 'Samouczek: Dodawanie sortowania, filtrowania i stronicowania — ASP.NET MVC z EF Core'
description: W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania na stronie indeksu uczniów. Utworzysz również stronę, która będzie prostą grupowaniem.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 24643931ed84ac257d98ceb8cc6c64d0767cfc14
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212597"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Samouczek: Dodawanie sortowania, filtrowania i stronicowania — ASP.NET MVC z EF Core

W poprzednim samouczku zaimplementowano zestaw stron sieci Web dla podstawowych operacji CRUD dla jednostek uczniów. W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania na stronie indeksu uczniów. Utworzysz również stronę, która będzie prostą grupowaniem.

Na poniższej ilustracji przedstawiono, jak będzie wyglądać strona po zakończeniu. Nagłówki kolumn to linki, które użytkownik może kliknąć, aby posortować według tej kolumny. Wielokrotne kliknięcie nagłówka kolumny powoduje przełączenie między rosnącą i malejącą kolejnością sortowania.

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dodaj linki sortowania kolumn
> * Dodawanie pola wyszukiwania
> * Dodaj stronicowanie do indeksu studentów
> * Dodaj stronicowanie do metody index
> * Dodaj linki stronicowania
> * Tworzenie strony informacje

## <a name="prerequisites"></a>Wymagania wstępne

* [Implementowanie funkcji CRUD](crud.md)

## <a name="add-column-sort-links"></a>Dodaj linki sortowania kolumn

Aby dodać sortowanie na stronie indeksu ucznia, należy zmienić `Index` metodę kontrolera uczniów i dodać kod do widoku indeksu studenta.

### <a name="add-sorting-functionality-to-the-index-method"></a>Dodawanie funkcji sortowania do metody index

W *StudentsController.cs*Zastąp `Index` metodę następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Ten kod otrzymuje `sortOrder` parametr z ciągu zapytania w adresie URL. Wartość ciągu zapytania jest dostarczana przez ASP.NET Core MVC jako parametr do metody akcji. Parametr będzie ciągiem "name" lub "date", opcjonalnie po znaku podkreślenia i ciągu "DESC" w celu określenia kolejności malejącej. Domyślna kolejność sortowania to Ascending.

Podczas pierwszego żądania strony indeksu nie ma ciągu zapytania. Studenci są wyświetlani w kolejności rosnącej według nazwiska, która jest wartością domyślną ustanowioną przez przypadek przypadania w `switch` instrukcji. Gdy użytkownik kliknie hiperlink nagłówka kolumny, odpowiednia `sortOrder` wartość jest podawana w ciągu zapytania.

Dwa `ViewData` elementy (NameSortParm i DateSortParm) są używane przez widok do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Są to "Trzyelementowy". Pierwszy z nich określa, że jeśli `sortOrder` parametr ma wartość null lub jest pusty, NameSortParm powinna być ustawiona na "name_desc"; w przeciwnym razie należy ustawić jako pusty ciąg. Te dwie instrukcje umożliwiają widokowi ustawienie hiperłączy nagłówka kolumny w następujący sposób:

|  Bieżący porządek sortowania  | Hiperłącze nazwisko | Hiperłącze daty |
|:--------------------:|:-------------------:|:--------------:|
| Nazwisko — rosnąco  | descending          | ascending      |
| Nazwisko malejąco | ascending           | ascending      |
| Data rosnąca       | ascending           | descending     |
| Data malejąca      | ascending           | ascending      |

Metoda używa LINQ to Entities, aby określić kolumnę, według której ma zostać wykonane sortowanie. Kod tworzy `IQueryable` zmienną przed instrukcją Switch, modyfikuje ją w instrukcji switch i wywołuje `ToListAsync` metodę po `switch` instrukcji. Podczas tworzenia i modyfikowania `IQueryable` zmiennych nie są wysyłane żadne zapytania do bazy danych. Zapytanie nie jest wykonywane do momentu przekonwertowania `IQueryable` obiektu do kolekcji przez wywołanie metody, takiej jak `ToListAsync` . W związku z tym ten kod skutkuje pojedynczym zapytaniem, które nie jest wykonywane do momentu wykonania `return View` instrukcji.

Ten kod może uzyskać pełne informacje z dużą liczbą kolumn. [W ostatnim samouczku w tej serii](advanced.md#dynamic-linq) pokazano, jak napisać kod, który umożliwia przekazanie nazwy `OrderBy` kolumny w zmiennej ciągu.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Dodawanie hiperłączy nagłówka kolumny do widoku indeksu ucznia

Zastąp kod w *widokach/uczniów/index. cshtml*, używając poniższego kodu do dodawania hiperłączy nagłówka kolumny. Zmienione wiersze są wyróżnione.

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Ten kod używa informacji we `ViewData` właściwościach do konfigurowania hiperłączy z odpowiednimi wartościami ciągu zapytania.

Uruchom aplikację, wybierz kartę **studenci** , a następnie kliknij nagłówek kolumny **nazwisko** i **Data rejestracji** , aby sprawdzić, czy sortowanie działa.

![Strona indeksu uczniów w kolejności nazw](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Dodawanie pola wyszukiwania

Aby dodać filtrowanie do strony indeksu uczniów, należy dodać pole tekstowe i przycisk Prześlij do widoku i wprowadzić odpowiednie zmiany w `Index` metodzie. Pole tekstowe umożliwia wprowadzenie ciągu do wyszukania w polach Imię i nazwisko.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dodawanie funkcji filtrowania do metody index

W *StudentsController.cs*Zastąp `Index` metodę następującym kodem (zmiany są wyróżnione).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Dodano `searchString` parametr do `Index` metody. Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostanie dodane do widoku indeksu. Dodano również do instrukcji LINQ klauzula WHERE, która wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania. Instrukcja, która dodaje klauzulę WHERE, jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.

> [!NOTE]
> W tym miejscu wywoływana jest `Where` Metoda `IQueryable` obiektu, a filtr zostanie przetworzony na serwerze. W niektórych scenariuszach może być wywoływana `Where` Metoda jako metoda rozszerzająca w kolekcji w pamięci. (Na przykład załóżmy, że zmienisz odwołanie na `_context.Students` tak, aby zamiast EF `DbSet` odwołuje się do metody repozytorium, która zwraca `IEnumerable` kolekcję). Wyniki byłyby zwykle takie same, ale w niektórych przypadkach mogą być różne.
>
>Na przykład .NET Framework implementacja `Contains` metody domyślnie wykonuje porównanie z uwzględnieniem wielkości liter, ale w SQL Server jest określana przez ustawienie sortowania wystąpienia SQL Server. Ustawienie domyślne nie uwzględnia wielkości liter. Można wywołać metodę, `ToUpper` Aby test jawnie nie uwzględniał wielkości liter: *gdzie (s => s. LastName. ToUpper (). Zawiera (Ciągwyszukiwania. ToUpper ())*. Dzięki temu wyniki są takie same, jeśli później zmienisz kod w celu użycia repozytorium, które zwraca `IEnumerable` kolekcję zamiast `IQueryable` obiektu. (Po wywołaniu `Contains` metody w `IEnumerable` kolekcji jest pobierana .NET Framework implementacja. po wywołaniu dla `IQueryable` obiektu zostanie wykorzystana implementacja dostawcy bazy danych). Istnieje jednak spadek wydajności dla tego rozwiązania. `ToUpper`Kod mógłby umieścić funkcję w klauzuli WHERE instrukcji SELECT TSQL. Uniemożliwi to Optymalizatorowi użycie indeksu. Mając na względzie, że program SQL jest przede wszystkim instalowany jako bez uwzględniania wielkości liter, najlepszym rozwiązaniem jest uniknięcie `ToUpper` kodu do momentu przeprowadzenia migracji do magazynu danych z uwzględnieniem wielkości liter.

### <a name="add-a-search-box-to-the-student-index-view"></a>Dodawanie pola wyszukiwania do widoku indeksu ucznia

W obszarze *widoki/uczniów/index. cshtml*Dodaj wyróżniony kod bezpośrednio przed otwierającym tagiem tabeli, aby utworzyć podpis, pole tekstowe i przycisk **wyszukiwania** .

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Ten kod używa `<form>` [pomocnika tagów](xref:mvc/views/tag-helpers/intro) do dodawania pola tekstowego wyszukiwania i przycisku. Domyślnie `<form>` pomocnik tagów przesyła dane formularza z wpisem, co oznacza, że parametry są przesyłane w treści wiadomości HTTP, a nie w adresie URL jako ciągi zapytań. Po określeniu protokołu HTTP GET dane formularza są przesyłane w adresie URL jako ciągi zapytań, co umożliwia użytkownikom tworzenie zakładek w adresie URL. Wskazówki dotyczące W3C zaleca się użycie GET, gdy akcja nie spowoduje aktualizacji.

Uruchom aplikację, wybierz kartę **studenci** , wprowadź ciąg wyszukiwania, a następnie kliknij przycisk Wyszukaj, aby sprawdzić, czy filtrowanie działa.

![Strona indeksu uczniów z filtrowaniem](sort-filter-page/_static/filtering.png)

Zwróć uwagę, że adres URL zawiera ciąg wyszukiwania.

```html
http://localhost:5813/Students?SearchString=an
```

Jeśli utworzysz zakładkę na tej stronie, otrzymasz przefiltrowaną listę, gdy używasz zakładki. Dodanie `method="get"` do `form` tagu wskazuje, co spowodowało wygenerowanie ciągu zapytania.

Jeśli na tym etapie klikniesz link sortowania nagłówka kolumny, utracisz wartość filtru wprowadzoną w polu **wyszukiwania** . Należy rozwiązać ten problem w następnej sekcji.

## <a name="add-paging-to-students-index"></a>Dodaj stronicowanie do indeksu studentów

Aby dodać stronicowanie do strony indeksu uczniów, utworzysz `PaginatedList` klasę, która używa `Skip` `Take` instrukcji i do filtrowania danych na serwerze, a nie zawsze pobiera wszystkie wiersze tabeli. Następnie wprowadzisz dodatkowe zmiany w `Index` metodzie i dodasz przyciski stronicowania do `Index` widoku. Na poniższej ilustracji przedstawiono przyciski stronicowania.

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

W folderze projektu Utwórz `PaginatedList.cs` , a następnie zastąp kod szablonu poniższym kodem.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

`CreateAsync`Metoda w tym kodzie przyjmuje rozmiar strony i numer strony oraz stosuje odpowiednie `Skip` `Take` instrukcje i do `IQueryable` . Gdy `ToListAsync` jest wywoływana na `IQueryable` , zwróci listę zawierającą tylko żądaną stronę. Właściwości `HasPreviousPage` i `HasNextPage` mogą służyć do włączania lub wyłączania przycisków **poprzedniego** i **następnego** stronicowania.

`CreateAsync`Metoda jest używana zamiast konstruktora do utworzenia `PaginatedList<T>` obiektu, ponieważ konstruktory nie mogą uruchomić kodu asynchronicznego.

## <a name="add-paging-to-index-method"></a>Dodaj stronicowanie do metody index

W *StudentsController.cs*Zastąp `Index` metodę poniższym kodem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Ten kod dodaje parametr numeru strony, bieżący parametr kolejności sortowania i bieżący parametr filtru do sygnatury metody.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Gdy strona jest wyświetlana po raz pierwszy lub jeśli użytkownik nie kliknął linku stronicowania ani sortowania, wszystkie parametry będą mieć wartość null.  Jeśli zostanie kliknięty link stronicowania, zmienna strony będzie zawierać numer strony do wyświetlenia.

`ViewData`Element o nazwie CurrentSort zapewnia widok z bieżącą kolejnością sortowania, ponieważ musi być uwzględniony w łączach stronicowania, aby zachować porządek sortowania w tym samym czasie podczas stronicowania.

`ViewData`Element o nazwie CurrentFilter zapewnia widok z bieżącym ciągiem filtru. Ta wartość musi być uwzględniona w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania, i musi zostać przywrócone do pola tekstowego, gdy strona jest ponownie wyświetlana.

Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, należy zresetować stronę do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych. Ciąg wyszukiwania jest zmieniany, gdy wartość zostanie wprowadzona w polu tekstowym, a przycisk Prześlij zostanie naciśnięty. W takim przypadku `searchString` parametr nie ma wartości null.

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

Na końcu `Index` metody `PaginatedList.CreateAsync` Metoda konwertuje zapytanie ucznia na pojedynczą stronę uczniów w typie kolekcji, który obsługuje stronicowanie. Ta pojedyncza strona studentów jest następnie przenoszona do widoku.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

`PaginatedList.CreateAsync`Metoda przyjmuje numer strony. Dwa znaki zapytania reprezentują operator łączenia wartości null. Operator łączenia wartości null definiuje wartość domyślną dla typu dopuszczającego wartość null; wyrażenie `(pageNumber ?? 1)` oznacza zwrócenie wartości `pageNumber` , jeśli ma wartość, lub zwraca wartość 1, jeśli `pageNumber` jest równa null.

## <a name="add-paging-links"></a>Dodaj linki stronicowania

W obszarze *widoki/studenci/index. cshtml*Zastąp istniejący kod następującym kodem. Zmiany są wyróżnione.

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

`@model`Instrukcja w górnej części strony określa, że widok pobiera teraz `PaginatedList<T>` obiekt zamiast `List<T>` obiektu.

Linki nagłówka kolumny używają ciągu zapytania, aby przekazać bieżący ciąg wyszukiwania do kontrolera, tak aby użytkownik mógł sortować wyniki filtrów:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Przyciski stronicowania są wyświetlane przez pomocników tagów:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Uruchom aplikację i przejdź do strony uczniów.

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

Kliknij linki stronicowania w różnych kolejności sortowania, aby upewnić się, że stronicowanie działa. Następnie wprowadź ciąg wyszukiwania i spróbuj ponownie wykonać stronicowanie, aby sprawdzić, czy stronicowanie działa prawidłowo z sortowaniem i filtrowaniem.

## <a name="create-an-about-page"></a>Tworzenie strony informacje

Na stronie **Informacje o** witrynie sieci Web dla uniwersytetów firmy Contoso można wyświetlić liczbę studentów zarejestrowanych dla każdej daty rejestracji. Wymaga to grupowania i prostych obliczeń dla grup. Aby to osiągnąć, należy wykonać następujące czynności:

* Utwórz klasę modelu widoku dla danych, które należy przekazać do widoku.
* Utwórz metodę about na kontrolerze głównym.
* Utwórz widok informacje.

### <a name="create-the-view-model"></a>Tworzenie modelu widoku

Utwórz folder *SchoolViewModels* w folderze *models* .

W nowym folderze Dodaj plik klasy *EnrollmentDateGroup.cs* i Zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Modyfikowanie kontrolera macierzystego

W *HomeController.cs*Dodaj następujące instrukcje using na początku pliku:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Dodaj zmienną klasy dla kontekstu bazy danych bezpośrednio po otwierającym nawiasie klamrowym dla klasy i Pobierz wystąpienie kontekstu z ASP.NET Core DI:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Dodaj `About` metodę o następującym kodzie:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w każdej grupie i zapisuje wyniki w kolekcji `EnrollmentDateGroup` obiektów modelu widoku.

### <a name="create-the-about-view"></a>Tworzenie widoku informacje

Dodaj plik *views/Home/about. cshtml* o następującym kodzie:

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

Uruchom aplikację i przejdź do strony informacje. W tabeli zostanie wyświetlona liczba uczniów dla każdej daty rejestracji.

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dodano linki sortowania kolumn
> * Dodano pole wyszukiwania
> * Dodano stronicowanie do indeksu studentów
> * Dodano stronicowanie do metody index
> * Dodano linki stronicowania
> * Utworzono stronę o

Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać zmiany modelu danych przy użyciu migracji.

> [!div class="nextstepaction"]
> [Dalej: Obsługuj zmiany modelu danych](migrations.md)
