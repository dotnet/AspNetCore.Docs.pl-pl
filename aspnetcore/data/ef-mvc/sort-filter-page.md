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
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f37a9-104">Samouczek: Dodawanie sortowania, filtrowania i stronicowania — ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="f37a9-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f37a9-105">W poprzednim samouczku zaimplementowano zestaw stron sieci Web dla podstawowych operacji CRUD dla jednostek uczniów.</span><span class="sxs-lookup"><span data-stu-id="f37a9-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="f37a9-106">W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania na stronie indeksu uczniów.</span><span class="sxs-lookup"><span data-stu-id="f37a9-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="f37a9-107">Utworzysz również stronę, która będzie prostą grupowaniem.</span><span class="sxs-lookup"><span data-stu-id="f37a9-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="f37a9-108">Na poniższej ilustracji przedstawiono, jak będzie wyglądać strona po zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="f37a9-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="f37a9-109">Nagłówki kolumn to linki, które użytkownik może kliknąć, aby posortować według tej kolumny.</span><span class="sxs-lookup"><span data-stu-id="f37a9-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="f37a9-110">Wielokrotne kliknięcie nagłówka kolumny powoduje przełączenie między rosnącą i malejącą kolejnością sortowania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

<span data-ttu-id="f37a9-112">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f37a9-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f37a9-113">Dodaj linki sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="f37a9-113">Add column sort links</span></span>
> * <span data-ttu-id="f37a9-114">Dodawanie pola wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="f37a9-114">Add a Search box</span></span>
> * <span data-ttu-id="f37a9-115">Dodaj stronicowanie do indeksu studentów</span><span class="sxs-lookup"><span data-stu-id="f37a9-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="f37a9-116">Dodaj stronicowanie do metody index</span><span class="sxs-lookup"><span data-stu-id="f37a9-116">Add paging to Index method</span></span>
> * <span data-ttu-id="f37a9-117">Dodaj linki stronicowania</span><span class="sxs-lookup"><span data-stu-id="f37a9-117">Add paging links</span></span>
> * <span data-ttu-id="f37a9-118">Tworzenie strony informacje</span><span class="sxs-lookup"><span data-stu-id="f37a9-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f37a9-119">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f37a9-119">Prerequisites</span></span>

* [<span data-ttu-id="f37a9-120">Implementowanie funkcji CRUD</span><span class="sxs-lookup"><span data-stu-id="f37a9-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="f37a9-121">Dodaj linki sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="f37a9-121">Add column sort links</span></span>

<span data-ttu-id="f37a9-122">Aby dodać sortowanie na stronie indeksu ucznia, należy zmienić `Index` metodę kontrolera uczniów i dodać kod do widoku indeksu studenta.</span><span class="sxs-lookup"><span data-stu-id="f37a9-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="f37a9-123">Dodawanie funkcji sortowania do metody index</span><span class="sxs-lookup"><span data-stu-id="f37a9-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="f37a9-124">W *StudentsController.cs*Zastąp `Index` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f37a9-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="f37a9-125">Ten kod otrzymuje `sortOrder` parametr z ciągu zapytania w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="f37a9-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="f37a9-126">Wartość ciągu zapytania jest dostarczana przez ASP.NET Core MVC jako parametr do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="f37a9-127">Parametr będzie ciągiem "name" lub "date", opcjonalnie po znaku podkreślenia i ciągu "DESC" w celu określenia kolejności malejącej.</span><span class="sxs-lookup"><span data-stu-id="f37a9-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="f37a9-128">Domyślna kolejność sortowania to Ascending.</span><span class="sxs-lookup"><span data-stu-id="f37a9-128">The default sort order is ascending.</span></span>

<span data-ttu-id="f37a9-129">Podczas pierwszego żądania strony indeksu nie ma ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="f37a9-130">Studenci są wyświetlani w kolejności rosnącej według nazwiska, która jest wartością domyślną ustanowioną przez przypadek przypadania w `switch` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="f37a9-131">Gdy użytkownik kliknie hiperlink nagłówka kolumny, odpowiednia `sortOrder` wartość jest podawana w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="f37a9-132">Dwa `ViewData` elementy (NameSortParm i DateSortParm) są używane przez widok do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="f37a9-133">Są to "Trzyelementowy".</span><span class="sxs-lookup"><span data-stu-id="f37a9-133">These are ternary statements.</span></span> <span data-ttu-id="f37a9-134">Pierwszy z nich określa, że jeśli `sortOrder` parametr ma wartość null lub jest pusty, NameSortParm powinna być ustawiona na "name_desc"; w przeciwnym razie należy ustawić jako pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="f37a9-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="f37a9-135">Te dwie instrukcje umożliwiają widokowi ustawienie hiperłączy nagłówka kolumny w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="f37a9-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="f37a9-136">Bieżący porządek sortowania</span><span class="sxs-lookup"><span data-stu-id="f37a9-136">Current sort order</span></span>  | <span data-ttu-id="f37a9-137">Hiperłącze nazwisko</span><span class="sxs-lookup"><span data-stu-id="f37a9-137">Last Name Hyperlink</span></span> | <span data-ttu-id="f37a9-138">Hiperłącze daty</span><span class="sxs-lookup"><span data-stu-id="f37a9-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="f37a9-139">Nazwisko — rosnąco</span><span class="sxs-lookup"><span data-stu-id="f37a9-139">Last Name ascending</span></span>  | <span data-ttu-id="f37a9-140">descending</span><span class="sxs-lookup"><span data-stu-id="f37a9-140">descending</span></span>          | <span data-ttu-id="f37a9-141">ascending</span><span class="sxs-lookup"><span data-stu-id="f37a9-141">ascending</span></span>      |
| <span data-ttu-id="f37a9-142">Nazwisko malejąco</span><span class="sxs-lookup"><span data-stu-id="f37a9-142">Last Name descending</span></span> | <span data-ttu-id="f37a9-143">ascending</span><span class="sxs-lookup"><span data-stu-id="f37a9-143">ascending</span></span>           | <span data-ttu-id="f37a9-144">ascending</span><span class="sxs-lookup"><span data-stu-id="f37a9-144">ascending</span></span>      |
| <span data-ttu-id="f37a9-145">Data rosnąca</span><span class="sxs-lookup"><span data-stu-id="f37a9-145">Date ascending</span></span>       | <span data-ttu-id="f37a9-146">ascending</span><span class="sxs-lookup"><span data-stu-id="f37a9-146">ascending</span></span>           | <span data-ttu-id="f37a9-147">descending</span><span class="sxs-lookup"><span data-stu-id="f37a9-147">descending</span></span>     |
| <span data-ttu-id="f37a9-148">Data malejąca</span><span class="sxs-lookup"><span data-stu-id="f37a9-148">Date descending</span></span>      | <span data-ttu-id="f37a9-149">ascending</span><span class="sxs-lookup"><span data-stu-id="f37a9-149">ascending</span></span>           | <span data-ttu-id="f37a9-150">ascending</span><span class="sxs-lookup"><span data-stu-id="f37a9-150">ascending</span></span>      |

<span data-ttu-id="f37a9-151">Metoda używa LINQ to Entities, aby określić kolumnę, według której ma zostać wykonane sortowanie.</span><span class="sxs-lookup"><span data-stu-id="f37a9-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="f37a9-152">Kod tworzy `IQueryable` zmienną przed instrukcją Switch, modyfikuje ją w instrukcji switch i wywołuje `ToListAsync` metodę po `switch` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="f37a9-153">Podczas tworzenia i modyfikowania `IQueryable` zmiennych nie są wysyłane żadne zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f37a9-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="f37a9-154">Zapytanie nie jest wykonywane do momentu przekonwertowania `IQueryable` obiektu do kolekcji przez wywołanie metody, takiej jak `ToListAsync` .</span><span class="sxs-lookup"><span data-stu-id="f37a9-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="f37a9-155">W związku z tym ten kod skutkuje pojedynczym zapytaniem, które nie jest wykonywane do momentu wykonania `return View` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="f37a9-156">Ten kod może uzyskać pełne informacje z dużą liczbą kolumn.</span><span class="sxs-lookup"><span data-stu-id="f37a9-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="f37a9-157">[W ostatnim samouczku w tej serii](advanced.md#dynamic-linq) pokazano, jak napisać kod, który umożliwia przekazanie nazwy `OrderBy` kolumny w zmiennej ciągu.</span><span class="sxs-lookup"><span data-stu-id="f37a9-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="f37a9-158">Dodawanie hiperłączy nagłówka kolumny do widoku indeksu ucznia</span><span class="sxs-lookup"><span data-stu-id="f37a9-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="f37a9-159">Zastąp kod w *widokach/uczniów/index. cshtml*, używając poniższego kodu do dodawania hiperłączy nagłówka kolumny.</span><span class="sxs-lookup"><span data-stu-id="f37a9-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="f37a9-160">Zmienione wiersze są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="f37a9-160">The changed lines are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="f37a9-161">Ten kod używa informacji we `ViewData` właściwościach do konfigurowania hiperłączy z odpowiednimi wartościami ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="f37a9-162">Uruchom aplikację, wybierz kartę **studenci** , a następnie kliknij nagłówek kolumny **nazwisko** i **Data rejestracji** , aby sprawdzić, czy sortowanie działa.</span><span class="sxs-lookup"><span data-stu-id="f37a9-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Strona indeksu uczniów w kolejności nazw](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="f37a9-164">Dodawanie pola wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="f37a9-164">Add a Search box</span></span>

<span data-ttu-id="f37a9-165">Aby dodać filtrowanie do strony indeksu uczniów, należy dodać pole tekstowe i przycisk Prześlij do widoku i wprowadzić odpowiednie zmiany w `Index` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f37a9-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="f37a9-166">Pole tekstowe umożliwia wprowadzenie ciągu do wyszukania w polach Imię i nazwisko.</span><span class="sxs-lookup"><span data-stu-id="f37a9-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="f37a9-167">Dodawanie funkcji filtrowania do metody index</span><span class="sxs-lookup"><span data-stu-id="f37a9-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="f37a9-168">W *StudentsController.cs*Zastąp `Index` metodę następującym kodem (zmiany są wyróżnione).</span><span class="sxs-lookup"><span data-stu-id="f37a9-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="f37a9-169">Dodano `searchString` parametr do `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="f37a9-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="f37a9-170">Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostanie dodane do widoku indeksu.</span><span class="sxs-lookup"><span data-stu-id="f37a9-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="f37a9-171">Dodano również do instrukcji LINQ klauzula WHERE, która wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="f37a9-172">Instrukcja, która dodaje klauzulę WHERE, jest wykonywana tylko wtedy, gdy istnieje wartość do wyszukania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="f37a9-173">W tym miejscu wywoływana jest `Where` Metoda `IQueryable` obiektu, a filtr zostanie przetworzony na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f37a9-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="f37a9-174">W niektórych scenariuszach może być wywoływana `Where` Metoda jako metoda rozszerzająca w kolekcji w pamięci.</span><span class="sxs-lookup"><span data-stu-id="f37a9-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="f37a9-175">(Na przykład załóżmy, że zmienisz odwołanie na `_context.Students` tak, aby zamiast EF `DbSet` odwołuje się do metody repozytorium, która zwraca `IEnumerable` kolekcję). Wyniki byłyby zwykle takie same, ale w niektórych przypadkach mogą być różne.</span><span class="sxs-lookup"><span data-stu-id="f37a9-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="f37a9-176">Na przykład .NET Framework implementacja `Contains` metody domyślnie wykonuje porównanie z uwzględnieniem wielkości liter, ale w SQL Server jest określana przez ustawienie sortowania wystąpienia SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f37a9-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="f37a9-177">Ustawienie domyślne nie uwzględnia wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="f37a9-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="f37a9-178">Można wywołać metodę, `ToUpper` Aby test jawnie nie uwzględniał wielkości liter: *gdzie (s => s. LastName. ToUpper (). Zawiera (Ciągwyszukiwania. ToUpper ())*.</span><span class="sxs-lookup"><span data-stu-id="f37a9-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="f37a9-179">Dzięki temu wyniki są takie same, jeśli później zmienisz kod w celu użycia repozytorium, które zwraca `IEnumerable` kolekcję zamiast `IQueryable` obiektu.</span><span class="sxs-lookup"><span data-stu-id="f37a9-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="f37a9-180">(Po wywołaniu `Contains` metody w `IEnumerable` kolekcji jest pobierana .NET Framework implementacja. po wywołaniu dla `IQueryable` obiektu zostanie wykorzystana implementacja dostawcy bazy danych). Istnieje jednak spadek wydajności dla tego rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="f37a9-181">`ToUpper`Kod mógłby umieścić funkcję w klauzuli WHERE instrukcji SELECT TSQL.</span><span class="sxs-lookup"><span data-stu-id="f37a9-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="f37a9-182">Uniemożliwi to Optymalizatorowi użycie indeksu.</span><span class="sxs-lookup"><span data-stu-id="f37a9-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="f37a9-183">Mając na względzie, że program SQL jest przede wszystkim instalowany jako bez uwzględniania wielkości liter, najlepszym rozwiązaniem jest uniknięcie `ToUpper` kodu do momentu przeprowadzenia migracji do magazynu danych z uwzględnieniem wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="f37a9-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="f37a9-184">Dodawanie pola wyszukiwania do widoku indeksu ucznia</span><span class="sxs-lookup"><span data-stu-id="f37a9-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="f37a9-185">W obszarze *widoki/uczniów/index. cshtml*Dodaj wyróżniony kod bezpośrednio przed otwierającym tagiem tabeli, aby utworzyć podpis, pole tekstowe i przycisk **wyszukiwania** .</span><span class="sxs-lookup"><span data-stu-id="f37a9-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="f37a9-186">Ten kod używa `<form>` [pomocnika tagów](xref:mvc/views/tag-helpers/intro) do dodawania pola tekstowego wyszukiwania i przycisku.</span><span class="sxs-lookup"><span data-stu-id="f37a9-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="f37a9-187">Domyślnie `<form>` pomocnik tagów przesyła dane formularza z wpisem, co oznacza, że parametry są przesyłane w treści wiadomości HTTP, a nie w adresie URL jako ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="f37a9-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="f37a9-188">Po określeniu protokołu HTTP GET dane formularza są przesyłane w adresie URL jako ciągi zapytań, co umożliwia użytkownikom tworzenie zakładek w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="f37a9-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="f37a9-189">Wskazówki dotyczące W3C zaleca się użycie GET, gdy akcja nie spowoduje aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="f37a9-190">Uruchom aplikację, wybierz kartę **studenci** , wprowadź ciąg wyszukiwania, a następnie kliknij przycisk Wyszukaj, aby sprawdzić, czy filtrowanie działa.</span><span class="sxs-lookup"><span data-stu-id="f37a9-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Strona indeksu uczniów z filtrowaniem](sort-filter-page/_static/filtering.png)

<span data-ttu-id="f37a9-192">Zwróć uwagę, że adres URL zawiera ciąg wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="f37a9-193">Jeśli utworzysz zakładkę na tej stronie, otrzymasz przefiltrowaną listę, gdy używasz zakładki.</span><span class="sxs-lookup"><span data-stu-id="f37a9-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="f37a9-194">Dodanie `method="get"` do `form` tagu wskazuje, co spowodowało wygenerowanie ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="f37a9-195">Jeśli na tym etapie klikniesz link sortowania nagłówka kolumny, utracisz wartość filtru wprowadzoną w polu **wyszukiwania** .</span><span class="sxs-lookup"><span data-stu-id="f37a9-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="f37a9-196">Należy rozwiązać ten problem w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="f37a9-197">Dodaj stronicowanie do indeksu studentów</span><span class="sxs-lookup"><span data-stu-id="f37a9-197">Add paging to Students Index</span></span>

<span data-ttu-id="f37a9-198">Aby dodać stronicowanie do strony indeksu uczniów, utworzysz `PaginatedList` klasę, która używa `Skip` `Take` instrukcji i do filtrowania danych na serwerze, a nie zawsze pobiera wszystkie wiersze tabeli.</span><span class="sxs-lookup"><span data-stu-id="f37a9-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="f37a9-199">Następnie wprowadzisz dodatkowe zmiany w `Index` metodzie i dodasz przyciski stronicowania do `Index` widoku.</span><span class="sxs-lookup"><span data-stu-id="f37a9-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="f37a9-200">Na poniższej ilustracji przedstawiono przyciski stronicowania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-200">The following illustration shows the paging buttons.</span></span>

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

<span data-ttu-id="f37a9-202">W folderze projektu Utwórz `PaginatedList.cs` , a następnie zastąp kod szablonu poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="f37a9-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="f37a9-203">`CreateAsync`Metoda w tym kodzie przyjmuje rozmiar strony i numer strony oraz stosuje odpowiednie `Skip` `Take` instrukcje i do `IQueryable` .</span><span class="sxs-lookup"><span data-stu-id="f37a9-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="f37a9-204">Gdy `ToListAsync` jest wywoływana na `IQueryable` , zwróci listę zawierającą tylko żądaną stronę.</span><span class="sxs-lookup"><span data-stu-id="f37a9-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="f37a9-205">Właściwości `HasPreviousPage` i `HasNextPage` mogą służyć do włączania lub wyłączania przycisków **poprzedniego** i **następnego** stronicowania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="f37a9-206">`CreateAsync`Metoda jest używana zamiast konstruktora do utworzenia `PaginatedList<T>` obiektu, ponieważ konstruktory nie mogą uruchomić kodu asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="f37a9-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="f37a9-207">Dodaj stronicowanie do metody index</span><span class="sxs-lookup"><span data-stu-id="f37a9-207">Add paging to Index method</span></span>

<span data-ttu-id="f37a9-208">W *StudentsController.cs*Zastąp `Index` metodę poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="f37a9-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="f37a9-209">Ten kod dodaje parametr numeru strony, bieżący parametr kolejności sortowania i bieżący parametr filtru do sygnatury metody.</span><span class="sxs-lookup"><span data-stu-id="f37a9-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="f37a9-210">Gdy strona jest wyświetlana po raz pierwszy lub jeśli użytkownik nie kliknął linku stronicowania ani sortowania, wszystkie parametry będą mieć wartość null.</span><span class="sxs-lookup"><span data-stu-id="f37a9-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="f37a9-211">Jeśli zostanie kliknięty link stronicowania, zmienna strony będzie zawierać numer strony do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="f37a9-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="f37a9-212">`ViewData`Element o nazwie CurrentSort zapewnia widok z bieżącą kolejnością sortowania, ponieważ musi być uwzględniony w łączach stronicowania, aby zachować porządek sortowania w tym samym czasie podczas stronicowania.</span><span class="sxs-lookup"><span data-stu-id="f37a9-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="f37a9-213">`ViewData`Element o nazwie CurrentFilter zapewnia widok z bieżącym ciągiem filtru.</span><span class="sxs-lookup"><span data-stu-id="f37a9-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="f37a9-214">Ta wartość musi być uwzględniona w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania, i musi zostać przywrócone do pola tekstowego, gdy strona jest ponownie wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="f37a9-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="f37a9-215">Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, należy zresetować stronę do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych.</span><span class="sxs-lookup"><span data-stu-id="f37a9-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="f37a9-216">Ciąg wyszukiwania jest zmieniany, gdy wartość zostanie wprowadzona w polu tekstowym, a przycisk Prześlij zostanie naciśnięty.</span><span class="sxs-lookup"><span data-stu-id="f37a9-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="f37a9-217">W takim przypadku `searchString` parametr nie ma wartości null.</span><span class="sxs-lookup"><span data-stu-id="f37a9-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="f37a9-218">Na końcu `Index` metody `PaginatedList.CreateAsync` Metoda konwertuje zapytanie ucznia na pojedynczą stronę uczniów w typie kolekcji, który obsługuje stronicowanie.</span><span class="sxs-lookup"><span data-stu-id="f37a9-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="f37a9-219">Ta pojedyncza strona studentów jest następnie przenoszona do widoku.</span><span class="sxs-lookup"><span data-stu-id="f37a9-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="f37a9-220">`PaginatedList.CreateAsync`Metoda przyjmuje numer strony.</span><span class="sxs-lookup"><span data-stu-id="f37a9-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="f37a9-221">Dwa znaki zapytania reprezentują operator łączenia wartości null.</span><span class="sxs-lookup"><span data-stu-id="f37a9-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="f37a9-222">Operator łączenia wartości null definiuje wartość domyślną dla typu dopuszczającego wartość null; wyrażenie `(pageNumber ?? 1)` oznacza zwrócenie wartości `pageNumber` , jeśli ma wartość, lub zwraca wartość 1, jeśli `pageNumber` jest równa null.</span><span class="sxs-lookup"><span data-stu-id="f37a9-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="f37a9-223">Dodaj linki stronicowania</span><span class="sxs-lookup"><span data-stu-id="f37a9-223">Add paging links</span></span>

<span data-ttu-id="f37a9-224">W obszarze *widoki/studenci/index. cshtml*Zastąp istniejący kod następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="f37a9-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="f37a9-225">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="f37a9-225">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="f37a9-226">`@model`Instrukcja w górnej części strony określa, że widok pobiera teraz `PaginatedList<T>` obiekt zamiast `List<T>` obiektu.</span><span class="sxs-lookup"><span data-stu-id="f37a9-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="f37a9-227">Linki nagłówka kolumny używają ciągu zapytania, aby przekazać bieżący ciąg wyszukiwania do kontrolera, tak aby użytkownik mógł sortować wyniki filtrów:</span><span class="sxs-lookup"><span data-stu-id="f37a9-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="f37a9-228">Przyciski stronicowania są wyświetlane przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="f37a9-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="f37a9-229">Uruchom aplikację i przejdź do strony uczniów.</span><span class="sxs-lookup"><span data-stu-id="f37a9-229">Run the app and go to the Students page.</span></span>

![Strona indeksu uczniów z linkami stronicowania](sort-filter-page/_static/paging.png)

<span data-ttu-id="f37a9-231">Kliknij linki stronicowania w różnych kolejności sortowania, aby upewnić się, że stronicowanie działa.</span><span class="sxs-lookup"><span data-stu-id="f37a9-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="f37a9-232">Następnie wprowadź ciąg wyszukiwania i spróbuj ponownie wykonać stronicowanie, aby sprawdzić, czy stronicowanie działa prawidłowo z sortowaniem i filtrowaniem.</span><span class="sxs-lookup"><span data-stu-id="f37a9-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="f37a9-233">Tworzenie strony informacje</span><span class="sxs-lookup"><span data-stu-id="f37a9-233">Create an About page</span></span>

<span data-ttu-id="f37a9-234">Na stronie **Informacje o** witrynie sieci Web dla uniwersytetów firmy Contoso można wyświetlić liczbę studentów zarejestrowanych dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="f37a9-235">Wymaga to grupowania i prostych obliczeń dla grup.</span><span class="sxs-lookup"><span data-stu-id="f37a9-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="f37a9-236">Aby to osiągnąć, należy wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f37a9-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="f37a9-237">Utwórz klasę modelu widoku dla danych, które należy przekazać do widoku.</span><span class="sxs-lookup"><span data-stu-id="f37a9-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="f37a9-238">Utwórz metodę about na kontrolerze głównym.</span><span class="sxs-lookup"><span data-stu-id="f37a9-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="f37a9-239">Utwórz widok informacje.</span><span class="sxs-lookup"><span data-stu-id="f37a9-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="f37a9-240">Tworzenie modelu widoku</span><span class="sxs-lookup"><span data-stu-id="f37a9-240">Create the view model</span></span>

<span data-ttu-id="f37a9-241">Utwórz folder *SchoolViewModels* w folderze *models* .</span><span class="sxs-lookup"><span data-stu-id="f37a9-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="f37a9-242">W nowym folderze Dodaj plik klasy *EnrollmentDateGroup.cs* i Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f37a9-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="f37a9-243">Modyfikowanie kontrolera macierzystego</span><span class="sxs-lookup"><span data-stu-id="f37a9-243">Modify the Home Controller</span></span>

<span data-ttu-id="f37a9-244">W *HomeController.cs*Dodaj następujące instrukcje using na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="f37a9-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="f37a9-245">Dodaj zmienną klasy dla kontekstu bazy danych bezpośrednio po otwierającym nawiasie klamrowym dla klasy i Pobierz wystąpienie kontekstu z ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="f37a9-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="f37a9-246">Dodaj `About` metodę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="f37a9-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="f37a9-247">Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w każdej grupie i zapisuje wyniki w kolekcji `EnrollmentDateGroup` obiektów modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="f37a9-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="f37a9-248">Tworzenie widoku informacje</span><span class="sxs-lookup"><span data-stu-id="f37a9-248">Create the About View</span></span>

<span data-ttu-id="f37a9-249">Dodaj plik *views/Home/about. cshtml* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="f37a9-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="f37a9-250">Uruchom aplikację i przejdź do strony informacje.</span><span class="sxs-lookup"><span data-stu-id="f37a9-250">Run the app and go to the About page.</span></span> <span data-ttu-id="f37a9-251">W tabeli zostanie wyświetlona liczba uczniów dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f37a9-252">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="f37a9-252">Get the code</span></span>

[<span data-ttu-id="f37a9-253">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="f37a9-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="f37a9-254">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="f37a9-254">Next steps</span></span>

<span data-ttu-id="f37a9-255">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f37a9-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f37a9-256">Dodano linki sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="f37a9-256">Added column sort links</span></span>
> * <span data-ttu-id="f37a9-257">Dodano pole wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="f37a9-257">Added a Search box</span></span>
> * <span data-ttu-id="f37a9-258">Dodano stronicowanie do indeksu studentów</span><span class="sxs-lookup"><span data-stu-id="f37a9-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="f37a9-259">Dodano stronicowanie do metody index</span><span class="sxs-lookup"><span data-stu-id="f37a9-259">Added paging to Index method</span></span>
> * <span data-ttu-id="f37a9-260">Dodano linki stronicowania</span><span class="sxs-lookup"><span data-stu-id="f37a9-260">Added paging links</span></span>
> * <span data-ttu-id="f37a9-261">Utworzono stronę o</span><span class="sxs-lookup"><span data-stu-id="f37a9-261">Created an About page</span></span>

<span data-ttu-id="f37a9-262">Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać zmiany modelu danych przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="f37a9-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f37a9-263">Dalej: Obsługuj zmiany modelu danych</span><span class="sxs-lookup"><span data-stu-id="f37a9-263">Next: Handle data model changes</span></span>](migrations.md)
