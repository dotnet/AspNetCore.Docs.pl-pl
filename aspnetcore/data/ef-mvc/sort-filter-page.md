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
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="111b1-104">Samouczek: Dodawanie sortowania, filtrowania i stronicowania - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="111b1-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="111b1-105">W poprzednim samouczku zaimplementowano zestaw stron sieci web dla podstawowych operacji CRUD dla jednostek studenta.</span><span class="sxs-lookup"><span data-stu-id="111b1-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="111b1-106">W tym samouczku dodasz funkcje sortowania, filtrowania i stronicowania do strony Indeks uczniów.</span><span class="sxs-lookup"><span data-stu-id="111b1-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="111b1-107">Utworzysz również stronę, która wykonuje proste grupowanie.</span><span class="sxs-lookup"><span data-stu-id="111b1-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="111b1-108">Na poniższej ilustracji przedstawiono, jak strona będzie wyglądać po zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="111b1-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="111b1-109">Nagłówki kolumn są łączami, które użytkownik może kliknąć, aby posortować według tej kolumny.</span><span class="sxs-lookup"><span data-stu-id="111b1-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="111b1-110">Kliknięcie nagłówka kolumny wielokrotnie przełącza się między kolejnością sortowania rosnącego i malejącego.</span><span class="sxs-lookup"><span data-stu-id="111b1-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Strona indeksu uczniów](sort-filter-page/_static/paging.png)

<span data-ttu-id="111b1-112">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="111b1-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="111b1-113">Dodawanie łączy sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="111b1-113">Add column sort links</span></span>
> * <span data-ttu-id="111b1-114">Dodawanie pola wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="111b1-114">Add a Search box</span></span>
> * <span data-ttu-id="111b1-115">Dodawanie stronicowania do indeksu uczniów</span><span class="sxs-lookup"><span data-stu-id="111b1-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="111b1-116">Dodawanie stronicowania do metody indeksu</span><span class="sxs-lookup"><span data-stu-id="111b1-116">Add paging to Index method</span></span>
> * <span data-ttu-id="111b1-117">Dodawanie łączy stronicowania</span><span class="sxs-lookup"><span data-stu-id="111b1-117">Add paging links</span></span>
> * <span data-ttu-id="111b1-118">Tworzenie strony Informacje</span><span class="sxs-lookup"><span data-stu-id="111b1-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="111b1-119">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="111b1-119">Prerequisites</span></span>

* [<span data-ttu-id="111b1-120">Wdrażanie funkcji CRUD</span><span class="sxs-lookup"><span data-stu-id="111b1-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="111b1-121">Dodawanie łączy sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="111b1-121">Add column sort links</span></span>

<span data-ttu-id="111b1-122">Aby dodać sortowanie do strony Indeks uczniów, `Index` zmienisz metodę kontrolera Studenci i dodasz kod do widoku Indeks uczniów.</span><span class="sxs-lookup"><span data-stu-id="111b1-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="111b1-123">Dodawanie funkcji sortowania do metody Indeks</span><span class="sxs-lookup"><span data-stu-id="111b1-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="111b1-124">W *StudentsController.cs*, zastąp `Index` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="111b1-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="111b1-125">Ten kod `sortOrder` odbiera parametr z ciągu zapytania w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="111b1-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="111b1-126">Wartość ciągu zapytania jest dostarczana przez ASP.NET Core MVC jako parametr metody akcji.</span><span class="sxs-lookup"><span data-stu-id="111b1-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="111b1-127">Parametr będzie ciągiem, który jest "Name" lub "Date", opcjonalnie następuje podkreślenie i ciąg "desc", aby określić malejącą kolejność.</span><span class="sxs-lookup"><span data-stu-id="111b1-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="111b1-128">Domyślna kolejność sortowania jest rosnąca.</span><span class="sxs-lookup"><span data-stu-id="111b1-128">The default sort order is ascending.</span></span>

<span data-ttu-id="111b1-129">Po pierwszym żądaniu strony indeksu nie ma ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="111b1-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="111b1-130">Uczniowie są wyświetlani w porządku rosnącym według nazwiska, co jest domyślne, jak `switch` ustalono w przypadku fall-through w oświadczeniu.</span><span class="sxs-lookup"><span data-stu-id="111b1-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="111b1-131">Gdy użytkownik kliknie hiperłącze nagłówka kolumny, odpowiednia `sortOrder` wartość jest podana w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="111b1-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="111b1-132">Dwa `ViewData` elementy (NameSortParm i DateSortParm) są używane przez widok do konfigurowania hiperłączy nagłówka kolumny z odpowiednimi wartościami ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="111b1-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="111b1-133">Są to oświadczenia trójskładniowe.</span><span class="sxs-lookup"><span data-stu-id="111b1-133">These are ternary statements.</span></span> <span data-ttu-id="111b1-134">Pierwszy z nich określa, `sortOrder` że jeśli parametr jest null lub empty, NameSortParm należy ustawić na "name_desc"; w przeciwnym razie należy ustawić na pusty ciąg.</span><span class="sxs-lookup"><span data-stu-id="111b1-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="111b1-135">Te dwie instrukcje umożliwiają widokowi ustawienie hiperłączy nagłówka kolumny w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="111b1-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="111b1-136">Bieżąca kolejność sortowania</span><span class="sxs-lookup"><span data-stu-id="111b1-136">Current sort order</span></span>  | <span data-ttu-id="111b1-137">Hiperłącze do nazwiska</span><span class="sxs-lookup"><span data-stu-id="111b1-137">Last Name Hyperlink</span></span> | <span data-ttu-id="111b1-138">Daty Hiperłącze</span><span class="sxs-lookup"><span data-stu-id="111b1-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="111b1-139">Nazwisko rosnące</span><span class="sxs-lookup"><span data-stu-id="111b1-139">Last Name ascending</span></span>  | <span data-ttu-id="111b1-140">descending</span><span class="sxs-lookup"><span data-stu-id="111b1-140">descending</span></span>          | <span data-ttu-id="111b1-141">ascending</span><span class="sxs-lookup"><span data-stu-id="111b1-141">ascending</span></span>      |
| <span data-ttu-id="111b1-142">Nazwisko malejące</span><span class="sxs-lookup"><span data-stu-id="111b1-142">Last Name descending</span></span> | <span data-ttu-id="111b1-143">ascending</span><span class="sxs-lookup"><span data-stu-id="111b1-143">ascending</span></span>           | <span data-ttu-id="111b1-144">ascending</span><span class="sxs-lookup"><span data-stu-id="111b1-144">ascending</span></span>      |
| <span data-ttu-id="111b1-145">Data rosnąca</span><span class="sxs-lookup"><span data-stu-id="111b1-145">Date ascending</span></span>       | <span data-ttu-id="111b1-146">ascending</span><span class="sxs-lookup"><span data-stu-id="111b1-146">ascending</span></span>           | <span data-ttu-id="111b1-147">descending</span><span class="sxs-lookup"><span data-stu-id="111b1-147">descending</span></span>     |
| <span data-ttu-id="111b1-148">Data malejąco</span><span class="sxs-lookup"><span data-stu-id="111b1-148">Date descending</span></span>      | <span data-ttu-id="111b1-149">ascending</span><span class="sxs-lookup"><span data-stu-id="111b1-149">ascending</span></span>           | <span data-ttu-id="111b1-150">ascending</span><span class="sxs-lookup"><span data-stu-id="111b1-150">ascending</span></span>      |

<span data-ttu-id="111b1-151">Metoda używa LINQ do jednostek, aby określić kolumnę do sortowania według.</span><span class="sxs-lookup"><span data-stu-id="111b1-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="111b1-152">Kod tworzy `IQueryable` zmienną przed switch instrukcji, modyfikuje go w `ToListAsync` instrukcji `switch` switch i wywołuje metodę po instrukcji.</span><span class="sxs-lookup"><span data-stu-id="111b1-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="111b1-153">Podczas tworzenia i `IQueryable` modyfikowania zmiennych do bazy danych nie jest wysyłana żadna kwerenda.</span><span class="sxs-lookup"><span data-stu-id="111b1-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="111b1-154">Kwerenda nie jest wykonywana, `IQueryable` dopóki obiekt nie zostanie przekonwertowany na kolekcję, wywołując metodę, taką jak `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="111b1-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="111b1-155">W związku z tym ten kod powoduje pojedyncze `return View` zapytanie, które nie jest wykonywane, dopóki instrukcja.</span><span class="sxs-lookup"><span data-stu-id="111b1-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="111b1-156">Ten kod może uzyskać pełne z dużą liczbę kolumn.</span><span class="sxs-lookup"><span data-stu-id="111b1-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="111b1-157">[Ostatni samouczek z tej serii](advanced.md#dynamic-linq) pokazuje, jak napisać `OrderBy` kod, który pozwala przekazać nazwę kolumny w zmiennej ciągu.</span><span class="sxs-lookup"><span data-stu-id="111b1-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="111b1-158">Dodawanie hiperłączy nagłówka kolumny do widoku Indeks ucznia</span><span class="sxs-lookup"><span data-stu-id="111b1-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="111b1-159">Zastąp kod w *widokach/uczniach/index.cshtml*, następującym kodem, aby dodać hiperłącza nagłówków kolumn.</span><span class="sxs-lookup"><span data-stu-id="111b1-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="111b1-160">Zmienione linie są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="111b1-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="111b1-161">Ten kod używa informacji `ViewData` we właściwościach do konfigurowania hiperłączy z odpowiednimi wartościami ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="111b1-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="111b1-162">Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij nagłówki kolumn **Nazwisko** i **Data rejestracji,** aby sprawdzić, czy sortowanie działa.</span><span class="sxs-lookup"><span data-stu-id="111b1-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Strona indeksu uczniów w kolejności nazw](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="111b1-164">Dodawanie pola wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="111b1-164">Add a Search box</span></span>

<span data-ttu-id="111b1-165">Aby dodać filtrowanie do strony Indeks uczniów, dodasz do widoku pole tekstowe i `Index` przycisk prześlij i wniesiesz odpowiednie zmiany w tej metodzie.</span><span class="sxs-lookup"><span data-stu-id="111b1-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="111b1-166">Pole tekstowe umożliwia wprowadzenie ciągu do wyszukania w polach imienia i nazwiska.</span><span class="sxs-lookup"><span data-stu-id="111b1-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="111b1-167">Dodawanie funkcji filtrowania do metody Indeks</span><span class="sxs-lookup"><span data-stu-id="111b1-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="111b1-168">W *StudentsController.cs*, zastąp `Index` metodę następującym kodem (zmiany są wyróżnione).</span><span class="sxs-lookup"><span data-stu-id="111b1-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="111b1-169">Dodano `searchString` parametr do `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="111b1-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="111b1-170">Wartość ciągu wyszukiwania jest odbierana z pola tekstowego, które zostanie dodane do widoku indeksu.</span><span class="sxs-lookup"><span data-stu-id="111b1-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="111b1-171">Dodano również do instrukcji LINQ klauzulę where, która wybiera tylko uczniów, których imię lub nazwisko zawiera ciąg wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="111b1-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="111b1-172">Instrukcja, która dodaje where klauzuli jest wykonywany tylko wtedy, gdy istnieje wartość do wyszukania.</span><span class="sxs-lookup"><span data-stu-id="111b1-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="111b1-173">W tym miejscu `Where` wywołujesz `IQueryable` metodę na obiekcie, a filtr zostanie przetworzony na serwerze.</span><span class="sxs-lookup"><span data-stu-id="111b1-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="111b1-174">W niektórych scenariuszach może `Where` być wywołanie metody jako metody rozszerzenia w kolekcji w pamięci.</span><span class="sxs-lookup"><span data-stu-id="111b1-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="111b1-175">(Na przykład załóżmy, `_context.Students` że odwołanie do tak, aby zamiast EF `DbSet` odwołuje się `IEnumerable` do metody repozytorium, która zwraca kolekcję.) Wynik byłby zwykle taki sam, ale w niektórych przypadkach może być inny.</span><span class="sxs-lookup"><span data-stu-id="111b1-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="111b1-176">Na przykład implementacja .NET `Contains` Framework metody wykonuje porównanie z uwzględnieniem wielkości liter domyślnie, ale w programie SQL Server jest to określane przez ustawienie sortowania wystąpienia programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="111b1-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="111b1-177">To ustawienie domyślnie jest niewrażliwe na wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="111b1-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="111b1-178">Można wywołać `ToUpper` metodę, aby test jawnie bez uwzględniania wielkości liter: *Where(s => s.LastName.ToUpper(). Contains(searchString.ToUpper())*.</span><span class="sxs-lookup"><span data-stu-id="111b1-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="111b1-179">Dzięki temu wyniki pozostaną takie same, jeśli później zmienisz kod, `IEnumerable` aby użyć repozytorium, które zwraca kolekcję zamiast obiektu. `IQueryable`</span><span class="sxs-lookup"><span data-stu-id="111b1-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="111b1-180">(Po wywołaniu `Contains` metody `IEnumerable` w kolekcji, otrzymasz .NET Framework implementacji; po wywołaniu go na obiekcie, `IQueryable` otrzymasz implementacji dostawcy bazy danych.) Istnieje jednak kara za wydajność dla tego rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="111b1-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="111b1-181">Kod `ToUpper` będzie umieścić funkcję w klauzuli WHERE instrukcji TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="111b1-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="111b1-182">Uniemożliwiłoby to optymalizatora przy użyciu indeksu.</span><span class="sxs-lookup"><span data-stu-id="111b1-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="111b1-183">Biorąc pod uwagę, że SQL jest najczęściej instalowany jako bez `ToUpper` uwzględniania wielkości liter, najlepiej unikać kodu, dopóki nie zostanie migrowany do magazynu danych z uwzględnieniem wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="111b1-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="111b1-184">Dodawanie pola wyszukiwania do widoku indeksu ucznia</span><span class="sxs-lookup"><span data-stu-id="111b1-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="111b1-185">W *obszarze Widoki/Student/Index.cshtml*dodaj wyróżniony kod bezpośrednio przed tagiem tabeli otwierającej, aby utworzyć podpis, pole tekstowe i przycisk **Wyszukaj.**</span><span class="sxs-lookup"><span data-stu-id="111b1-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="111b1-186">Ten kod używa `<form>` [pomocnika tagu,](xref:mvc/views/tag-helpers/intro) aby dodać pole tekstowe wyszukiwania i przycisk.</span><span class="sxs-lookup"><span data-stu-id="111b1-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="111b1-187">Domyślnie pomocnik `<form>` tagu przesyła dane formularza z post, co oznacza, że parametry są przekazywane w treści wiadomości HTTP, a nie w adresie URL jako ciągi zapytania.</span><span class="sxs-lookup"><span data-stu-id="111b1-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="111b1-188">Po określeniu protokołu HTTP GET dane formularza są przekazywane w adresie URL jako ciągi zapytań, co umożliwia użytkownikom dodawanie do zakładek adresu URL.</span><span class="sxs-lookup"><span data-stu-id="111b1-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="111b1-189">Wytyczne W3C zaleca się, aby używać GET, gdy akcja nie powoduje aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="111b1-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="111b1-190">Uruchom aplikację, wybierz kartę **Uczniowie,** wprowadź ciąg wyszukiwania i kliknij przycisk Wyszukaj, aby sprawdzić, czy filtrowanie działa.</span><span class="sxs-lookup"><span data-stu-id="111b1-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Strona indeksowania uczniów z filtrem](sort-filter-page/_static/filtering.png)

<span data-ttu-id="111b1-192">Należy zauważyć, że adres URL zawiera ciąg wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="111b1-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="111b1-193">Jeśli dodasz tę stronę do zakładek, otrzymasz filtrowane listy podczas korzystania z zakładki.</span><span class="sxs-lookup"><span data-stu-id="111b1-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="111b1-194">Dodanie `method="get"` do `form` tagu jest przyczyną ciągu zapytania do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="111b1-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="111b1-195">Na tym etapie kliknięcie łącza do sortowania nagłówka kolumny spowoduje utratę wartości filtru wprowadzonej w polu **Wyszukiwania.**</span><span class="sxs-lookup"><span data-stu-id="111b1-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="111b1-196">Naprawisz to w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="111b1-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="111b1-197">Dodawanie stronicowania do indeksu uczniów</span><span class="sxs-lookup"><span data-stu-id="111b1-197">Add paging to Students Index</span></span>

<span data-ttu-id="111b1-198">Aby dodać stronicowanie do strony Indeks uczniów, utworzysz `PaginatedList` klasę, która używa `Skip` i `Take` instrukcji do filtrowania danych na serwerze, zamiast zawsze pobierać wszystkie wiersze tabeli.</span><span class="sxs-lookup"><span data-stu-id="111b1-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="111b1-199">Następnie należy wprowadzić dodatkowe zmiany `Index` w metodzie i `Index` dodać przyciski stronicowania do widoku.</span><span class="sxs-lookup"><span data-stu-id="111b1-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="111b1-200">Na poniższej ilustracji przedstawiono przyciski stronicowania.</span><span class="sxs-lookup"><span data-stu-id="111b1-200">The following illustration shows the paging buttons.</span></span>

![Strona indeksu uczniów z łączami stronicowania](sort-filter-page/_static/paging.png)

<span data-ttu-id="111b1-202">W folderze projektu `PaginatedList.cs`utwórz , a następnie zastąp kod szablonu następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="111b1-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="111b1-203">Metoda `CreateAsync` w tym kodzie przyjmuje rozmiar strony i `Skip` `Take` numer strony `IQueryable`i stosuje odpowiednie instrukcje do .</span><span class="sxs-lookup"><span data-stu-id="111b1-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="111b1-204">Po `ToListAsync` wywołaniu `IQueryable`na programie zwraca listę zawierającą tylko żądaną stronę.</span><span class="sxs-lookup"><span data-stu-id="111b1-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="111b1-205">Właściwości `HasPreviousPage` i `HasNextPage` może służyć do włączania lub wyłączania **poprzedni** i **następny** przyciski stronicowania.</span><span class="sxs-lookup"><span data-stu-id="111b1-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="111b1-206">Metoda `CreateAsync` jest używana zamiast konstruktora `PaginatedList<T>` do tworzenia obiektu, ponieważ konstruktory nie można uruchomić kodu asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="111b1-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="111b1-207">Dodawanie stronicowania do metody indeksu</span><span class="sxs-lookup"><span data-stu-id="111b1-207">Add paging to Index method</span></span>

<span data-ttu-id="111b1-208">W *StudentsController.cs*, zastąp `Index` metodę następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="111b1-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="111b1-209">Ten kod dodaje parametr numeru strony, parametr bieżącej kolejności sortowania i bieżący parametr filtru do podpisu metody.</span><span class="sxs-lookup"><span data-stu-id="111b1-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="111b1-210">Przy pierwszym wyświetleniu strony lub jeśli użytkownik nie kliknął łącza stronicowania lub sortowania, wszystkie parametry będą zerowe.</span><span class="sxs-lookup"><span data-stu-id="111b1-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="111b1-211">Jeśli kliknie łącze stronicowania, zmienna strony będzie zawierać numer strony do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="111b1-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="111b1-212">Element `ViewData` o nazwie CurrentSort zapewnia widok z bieżącej kolejności sortowania, ponieważ musi to być zawarte w łączach stronicowania, aby zachować kolejność sortowania tak samo podczas stronicowania.</span><span class="sxs-lookup"><span data-stu-id="111b1-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="111b1-213">Element `ViewData` o nazwie CurrentFilter udostępnia widok z bieżącym ciągiem filtru.</span><span class="sxs-lookup"><span data-stu-id="111b1-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="111b1-214">Ta wartość musi być uwzględniona w łączach stronicowania, aby zachować ustawienia filtru podczas stronicowania, i musi zostać przywrócona do pola tekstowego po ponownym wyświetleniu strony.</span><span class="sxs-lookup"><span data-stu-id="111b1-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="111b1-215">Jeśli ciąg wyszukiwania zostanie zmieniony podczas stronicowania, strona musi zostać zresetowana do 1, ponieważ nowy filtr może spowodować wyświetlenie różnych danych.</span><span class="sxs-lookup"><span data-stu-id="111b1-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="111b1-216">Ciąg wyszukiwania jest zmieniany po wprowadzeniu wartości w polu tekstowym i naciśnięciu przycisku Prześlij.</span><span class="sxs-lookup"><span data-stu-id="111b1-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="111b1-217">W takim przypadku `searchString` parametr nie jest null.</span><span class="sxs-lookup"><span data-stu-id="111b1-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="111b1-218">Na końcu `Index` metody `PaginatedList.CreateAsync` konwertuje zapytanie ucznia do jednej strony uczniów w typie kolekcji, który obsługuje stronicowania.</span><span class="sxs-lookup"><span data-stu-id="111b1-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="111b1-219">Ta pojedyncza strona uczniów jest następnie przekazywana do widoku.</span><span class="sxs-lookup"><span data-stu-id="111b1-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="111b1-220">Metoda `PaginatedList.CreateAsync` przyjmuje numer strony.</span><span class="sxs-lookup"><span data-stu-id="111b1-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="111b1-221">Dwa znaki zapytania reprezentują operatora zrzeszania się wartości null.</span><span class="sxs-lookup"><span data-stu-id="111b1-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="111b1-222">Operator scalania wartości null definiuje wartość domyślną dla typu możliwego do wartości null; wyrażenie `(pageNumber ?? 1)` oznacza zwraca `pageNumber` wartość, jeśli ma wartość lub zwraca `pageNumber` 1, jeśli jest null.</span><span class="sxs-lookup"><span data-stu-id="111b1-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="111b1-223">Dodawanie łączy stronicowania</span><span class="sxs-lookup"><span data-stu-id="111b1-223">Add paging links</span></span>

<span data-ttu-id="111b1-224">W *views/students/index.cshtml*, zastąp istniejący kod następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="111b1-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="111b1-225">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="111b1-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="111b1-226">Instrukcja `@model` w górnej części strony określa, że `PaginatedList<T>` widok pobiera teraz `List<T>` obiekt zamiast obiektu.</span><span class="sxs-lookup"><span data-stu-id="111b1-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="111b1-227">Łącza nagłówka kolumny używają ciągu zapytania do przekazywania bieżącego ciągu wyszukiwania do kontrolera, aby użytkownik mógł sortować w wynikach filtru:</span><span class="sxs-lookup"><span data-stu-id="111b1-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="111b1-228">Przyciski stronicowania są wyświetlane przez pomocników znaczników:</span><span class="sxs-lookup"><span data-stu-id="111b1-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="111b1-229">Uruchom aplikację i przejdź do strony Uczniowie.</span><span class="sxs-lookup"><span data-stu-id="111b1-229">Run the app and go to the Students page.</span></span>

![Strona indeksu uczniów z łączami stronicowania](sort-filter-page/_static/paging.png)

<span data-ttu-id="111b1-231">Kliknij łącza stronicowania w różnych zamówieniach sortowania, aby upewnić się, że stronicowanie działa.</span><span class="sxs-lookup"><span data-stu-id="111b1-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="111b1-232">Następnie wprowadź ciąg wyszukiwania i spróbuj ponownie stronicować, aby sprawdzić, czy stronicowanie działa poprawnie również z sortowaniem i filtrowaniem.</span><span class="sxs-lookup"><span data-stu-id="111b1-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="111b1-233">Tworzenie strony Informacje</span><span class="sxs-lookup"><span data-stu-id="111b1-233">Create an About page</span></span>

<span data-ttu-id="111b1-234">Na stronie **Informacje o** witrynie Uniwersytetu Contoso zostanie wyświetlona liczba studentów, którzy zarejestrowali się dla każdej daty rejestracji.</span><span class="sxs-lookup"><span data-stu-id="111b1-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="111b1-235">Wymaga to grupowania i prostych obliczeń na grupach.</span><span class="sxs-lookup"><span data-stu-id="111b1-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="111b1-236">Aby to osiągnąć, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="111b1-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="111b1-237">Utwórz klasę modelu widoku dla danych, które należy przekazać do widoku.</span><span class="sxs-lookup"><span data-stu-id="111b1-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="111b1-238">Utwórz ochocie metody w kontrolerze macierzystym.</span><span class="sxs-lookup"><span data-stu-id="111b1-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="111b1-239">Utwórz widok Informacje.</span><span class="sxs-lookup"><span data-stu-id="111b1-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="111b1-240">Tworzenie modelu widoku</span><span class="sxs-lookup"><span data-stu-id="111b1-240">Create the view model</span></span>

<span data-ttu-id="111b1-241">Utwórz folder *SchoolViewModels* w folderze *Modele.*</span><span class="sxs-lookup"><span data-stu-id="111b1-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="111b1-242">W nowym folderze dodaj plik klasy *EnrollmentDateGroup.cs* i zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="111b1-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="111b1-243">Modyfikowanie kontrolera głównego</span><span class="sxs-lookup"><span data-stu-id="111b1-243">Modify the Home Controller</span></span>

<span data-ttu-id="111b1-244">W *HomeController.cs*, dodaj następujące instrukcje w górnej części pliku:</span><span class="sxs-lookup"><span data-stu-id="111b1-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="111b1-245">Dodaj zmienną klasy dla kontekstu bazy danych natychmiast po otwarciu nawiasu klamrowego dla klasy i uzyskać wystąpienie kontekstu z ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="111b1-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="111b1-246">Dodaj `About` metodę z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="111b1-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="111b1-247">Instrukcja LINQ grupuje jednostki studenta według daty rejestracji, oblicza liczbę jednostek w `EnrollmentDateGroup` każdej grupie i przechowuje wyniki w kolekcji obiektów modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="111b1-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="111b1-248">Tworzenie widoku Informacje</span><span class="sxs-lookup"><span data-stu-id="111b1-248">Create the About View</span></span>

<span data-ttu-id="111b1-249">Dodaj plik *Views/Home/About.cshtml* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="111b1-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="111b1-250">Uruchom aplikację i przejdź do strony Informacje.</span><span class="sxs-lookup"><span data-stu-id="111b1-250">Run the app and go to the About page.</span></span> <span data-ttu-id="111b1-251">Liczba uczniów dla każdej daty rejestracji jest wyświetlana w tabeli.</span><span class="sxs-lookup"><span data-stu-id="111b1-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="111b1-252">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="111b1-252">Get the code</span></span>

[<span data-ttu-id="111b1-253">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="111b1-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="111b1-254">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="111b1-254">Next steps</span></span>

<span data-ttu-id="111b1-255">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="111b1-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="111b1-256">Dodano łącza sortowania kolumn</span><span class="sxs-lookup"><span data-stu-id="111b1-256">Added column sort links</span></span>
> * <span data-ttu-id="111b1-257">Dodano pole wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="111b1-257">Added a Search box</span></span>
> * <span data-ttu-id="111b1-258">Dodano stronicowanie do indeksu uczniów</span><span class="sxs-lookup"><span data-stu-id="111b1-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="111b1-259">Dodano stronicowanie do metody indeksu</span><span class="sxs-lookup"><span data-stu-id="111b1-259">Added paging to Index method</span></span>
> * <span data-ttu-id="111b1-260">Dodano łącza stronicowania</span><span class="sxs-lookup"><span data-stu-id="111b1-260">Added paging links</span></span>
> * <span data-ttu-id="111b1-261">Utworzono stronę Informacje</span><span class="sxs-lookup"><span data-stu-id="111b1-261">Created an About page</span></span>

<span data-ttu-id="111b1-262">Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać zmiany modelu danych przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="111b1-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="111b1-263">Dalej: Obsługa zmian modelu danych</span><span class="sxs-lookup"><span data-stu-id="111b1-263">Next: Handle data model changes</span></span>](migrations.md)
