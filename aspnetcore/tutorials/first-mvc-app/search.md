---
title: Część 7, Dodawanie wyszukiwania do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 7 serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 83bd904f90190456d42453653d3420c4c572fe8c
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652758"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="cd1df-103">Część 7, Dodawanie wyszukiwania do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="cd1df-103">Part 7, add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="cd1df-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cd1df-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cd1df-105">W tej sekcji dodasz możliwość wyszukiwania do `Index` metody akcji, która umożliwia wyszukiwanie filmów według *gatunku* lub *nazwy*.</span><span class="sxs-lookup"><span data-stu-id="cd1df-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="cd1df-106">Zaktualizuj metodę znajdującą się `Index` wewnątrz *kontrolerów/MoviesController. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="cd1df-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="cd1df-107">Pierwszy wiersz `Index` metody akcji tworzy zapytanie [LINQ](/dotnet/standard/using-linq) do wybierania filmów:</span><span class="sxs-lookup"><span data-stu-id="cd1df-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="cd1df-108">Zapytanie jest zdefiniowane *tylko* w tym momencie, **nie** zostało uruchomione względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="cd1df-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="cd1df-109">Jeśli `searchString` parametr zawiera ciąg, zapytanie o filmy jest modyfikowane w celu filtrowania wartości ciągu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="cd1df-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="cd1df-110">`s => s.Title.Contains()`Powyższy kod jest [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="cd1df-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cd1df-111">Wyrażenia lambda są używane w kwerendach [LINQ](/dotnet/standard/using-linq) opartych na metodach jako argumenty dla standardowych metod operatora zapytań, takich jak Metoda [WHERE](/dotnet/api/system.linq.enumerable.where) lub `Contains` (używana w kodzie powyżej).</span><span class="sxs-lookup"><span data-stu-id="cd1df-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="cd1df-112">Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub są modyfikowane przez wywołanie metody takiej jak `Where` , `Contains` lub `OrderBy` .</span><span class="sxs-lookup"><span data-stu-id="cd1df-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="cd1df-113">Zamiast tego wykonywanie zapytania jest odroczone.</span><span class="sxs-lookup"><span data-stu-id="cd1df-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="cd1df-114">Oznacza to, że Obliczanie wyrażenia jest opóźnione do momentu rzeczywistego przełączenia jego wartości rzeczywistej lub `ToListAsync` wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="cd1df-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="cd1df-115">Aby uzyskać więcej informacji o odroczonym wykonywaniu zapytań, zobacz [wykonywanie zapytań](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="cd1df-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="cd1df-116">Uwaga: Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) jest uruchamiana w bazie danych, a nie w kodzie c# pokazanym powyżej.</span><span class="sxs-lookup"><span data-stu-id="cd1df-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="cd1df-117">Uwzględnianie wielkości liter w zapytaniu zależy od bazy danych i sortowania.</span><span class="sxs-lookup"><span data-stu-id="cd1df-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="cd1df-118">Na SQL Server [zawiera](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy do [programu SQL, takie jak](/sql/t-sql/language-elements/like-transact-sql), w przypadku których wielkość liter nie jest uwzględniana.</span><span class="sxs-lookup"><span data-stu-id="cd1df-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="cd1df-119">W ramach programu SQLite domyślne sortowanie uwzględnia wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="cd1df-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="cd1df-120">Przejdź do adresu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="cd1df-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="cd1df-121">Dołącz ciąg zapytania, taki jak `?searchString=Ghost` do adresu URL.</span><span class="sxs-lookup"><span data-stu-id="cd1df-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="cd1df-122">Wyświetlane są filtrowane filmy.</span><span class="sxs-lookup"><span data-stu-id="cd1df-122">The filtered movies are displayed.</span></span>

![Widok indeksu](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="cd1df-124">Jeśli zmienisz podpis `Index` metody w taki sposób, aby miał parametr o nazwie `id` , `id` parametr będzie zgodny z opcjonalnym `{id}` symbolem zastępczym dla tras domyślnych ustawionych w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cd1df-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="cd1df-125">Zmień parametr na `id` i wszystkie wystąpienia `searchString` zmiany na `id` .</span><span class="sxs-lookup"><span data-stu-id="cd1df-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="cd1df-126">Poprzednia `Index` Metoda:</span><span class="sxs-lookup"><span data-stu-id="cd1df-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="cd1df-127">Zaktualizowana `Index` Metoda z `id` parametrem:</span><span class="sxs-lookup"><span data-stu-id="cd1df-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="cd1df-128">Teraz możesz przekazać tytuł wyszukiwania jako dane trasy (segment adresu URL), a nie jako wartość ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="cd1df-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Widok indeksu z wyrazem Ghost dodany do adresu URL i zwrotną listą filmów dwóch filmów, Ghostbusters i Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="cd1df-130">Nie można jednak oczekiwać, aby użytkownicy modyfikują adres URL za każdym razem, gdy chcą wyszukać film.</span><span class="sxs-lookup"><span data-stu-id="cd1df-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="cd1df-131">Teraz dodasz elementy interfejsu użytkownika, aby ułatwić im filtrowanie filmów.</span><span class="sxs-lookup"><span data-stu-id="cd1df-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="cd1df-132">Jeśli zmieniono sygnaturę `Index` metody w celu przetestowania, jak przekazać parametr powiązany z trasą `ID` , należy zmienić go z powrotem tak, aby pobierał parametr o nazwie `searchString` :</span><span class="sxs-lookup"><span data-stu-id="cd1df-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="cd1df-133">Otwórz plik *views/filmy/index. cshtml* i Dodaj `<form>` wyróżniony poniżej znacznik:</span><span class="sxs-lookup"><span data-stu-id="cd1df-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="cd1df-134">Tag HTML `<form>` używa [pomocnika tagów formularza](xref:mvc/views/working-with-forms), dlatego podczas przesyłania formularza ciąg filtru jest ogłaszany w `Index` akcji kontrolera filmów.</span><span class="sxs-lookup"><span data-stu-id="cd1df-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="cd1df-135">Zapisz zmiany, a następnie przetestuj filtr.</span><span class="sxs-lookup"><span data-stu-id="cd1df-135">Save your changes and then test the filter.</span></span>

![Widok indeksu z słowem Ghost wpisanych do pola tekstowego filtru tytułu](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="cd1df-137">Nie istnieje `[HttpPost]` Przeciążenie `Index` metody w oczekiwany sposób.</span><span class="sxs-lookup"><span data-stu-id="cd1df-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="cd1df-138">Nie jest to potrzebne, ponieważ metoda nie zmienia stanu aplikacji, tylko filtrowanie danych.</span><span class="sxs-lookup"><span data-stu-id="cd1df-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="cd1df-139">Można dodać następującą `[HttpPost] Index` metodę.</span><span class="sxs-lookup"><span data-stu-id="cd1df-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="cd1df-140">`notUsed`Parametr służy do tworzenia przeciążenia dla `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="cd1df-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="cd1df-141">Będziemy mówić o tym w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="cd1df-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="cd1df-142">W przypadku dodania tej metody akcja Źródło będzie zgodna z `[HttpPost] Index` metodą, a `[HttpPost] Index` Metoda zostanie uruchomiona, jak pokazano na poniższej ilustracji.</span><span class="sxs-lookup"><span data-stu-id="cd1df-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Okno przeglądarki z odpowiedzią aplikacji z indeksu HttpPost: filtr dla elementu Ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="cd1df-144">Jednak nawet w przypadku dodania tej `[HttpPost]` wersji `Index` metody istnieje ograniczenie, w jaki sposób to wszystko zostało zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="cd1df-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="cd1df-145">Załóżmy, że chcesz utworzyć zakładką określonego wyszukiwania, lub chcesz wysłać link do znajomych, które mogą kliknąć, aby zobaczyć tę samą filtrowaną listę filmów.</span><span class="sxs-lookup"><span data-stu-id="cd1df-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="cd1df-146">Zwróć uwagę, że adres URL żądania HTTP POST jest taki sam jak adres URL żądania GET (localhost: {PORT}/filmy/indeks) — w adresie URL nie ma informacji o wyszukiwaniu.</span><span class="sxs-lookup"><span data-stu-id="cd1df-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="cd1df-147">Informacje o ciągu wyszukiwania są wysyłane do serwera jako [wartość pola formularza](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="cd1df-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="cd1df-148">Możesz sprawdzić, czy za pomocą narzędzi deweloperskich przeglądarki lub doskonałego [Narzędzia programu Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="cd1df-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="cd1df-149">Na poniższej ilustracji przedstawiono narzędzia deweloperskie przeglądarki Chrome:</span><span class="sxs-lookup"><span data-stu-id="cd1df-149">The image below shows the Chrome browser Developer tools:</span></span>

![Karta sieciowa Narzędzia deweloperskie w przeglądarce Microsoft Edge pokazująca treść żądania z Ciągwyszukiwania wartością Ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="cd1df-151">W treści żądania można zobaczyć parametr Search i token [XSRF](xref:security/anti-request-forgery) .</span><span class="sxs-lookup"><span data-stu-id="cd1df-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="cd1df-152">Uwaga, jak wspomniano w poprzednim samouczku, [pomocnik tagów formularza](xref:mvc/views/working-with-forms) generuje token [XSRF](xref:security/anti-request-forgery) chroniący przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="cd1df-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="cd1df-153">Dane nie są modyfikowane, więc nie trzeba sprawdzać tokenu w metodzie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cd1df-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="cd1df-154">Ponieważ parametr wyszukiwania znajduje się w treści żądania, a nie w adresie URL, nie można przechwytywać tych informacji wyszukiwania do zakładek lub udostępniania innym osobom.</span><span class="sxs-lookup"><span data-stu-id="cd1df-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="cd1df-155">Aby rozwiązać ten problem, należy określić żądanie należy `HTTP GET` znaleźć w pliku *viewss/filmów/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cd1df-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="cd1df-156">Teraz, gdy wyślesz wyszukiwanie, adres URL zawiera ciąg zapytania wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="cd1df-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="cd1df-157">Wyszukiwanie spowoduje również przejście do `HttpGet Index` metody akcji, nawet jeśli masz `HttpPost Index` metodę.</span><span class="sxs-lookup"><span data-stu-id="cd1df-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Okno przeglądarki pokazujące Ciągwyszukiwania = Ghost w adresie URL, a filmy zwrócone, Ghostbusters i Ghostbusters 2 zawierają słowo Ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="cd1df-159">Następujące znaczniki pokazują zmianę `form` znacznika:</span><span class="sxs-lookup"><span data-stu-id="cd1df-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="cd1df-160">Dodaj wyszukiwanie według gatunku</span><span class="sxs-lookup"><span data-stu-id="cd1df-160">Add Search by genre</span></span>

<span data-ttu-id="cd1df-161">Dodaj następującą `MovieGenreViewModel` klasę do folderu *models* :</span><span class="sxs-lookup"><span data-stu-id="cd1df-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="cd1df-162">Model widoku film-gatunek będzie zawierać następujące:</span><span class="sxs-lookup"><span data-stu-id="cd1df-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="cd1df-163">Lista filmów.</span><span class="sxs-lookup"><span data-stu-id="cd1df-163">A list of movies.</span></span>
* <span data-ttu-id="cd1df-164">A `SelectList` zawiera listę gatunku.</span><span class="sxs-lookup"><span data-stu-id="cd1df-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="cd1df-165">Dzięki temu użytkownik może wybrać gatunek z listy.</span><span class="sxs-lookup"><span data-stu-id="cd1df-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="cd1df-166">`MovieGenre`, który zawiera wybrany gatunek.</span><span class="sxs-lookup"><span data-stu-id="cd1df-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="cd1df-167">`SearchString`, który zawiera tekst wprowadzany przez użytkowników w polu tekstowym Wyszukaj.</span><span class="sxs-lookup"><span data-stu-id="cd1df-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="cd1df-168">Zastąp `Index` metodę w `MoviesController.cs` następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="cd1df-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="cd1df-169">Poniższy kod jest `LINQ` zapytaniem pobierającym wszystkie gatunki z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="cd1df-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="cd1df-170">`SelectList`Gatunek jest tworzony przez projekcję odrębnych gatunków (nie chcemy, aby nasze listy wyboru miały zduplikowane gatunek).</span><span class="sxs-lookup"><span data-stu-id="cd1df-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="cd1df-171">Gdy użytkownik wyszukuje element, wartość wyszukiwania jest zachowywana w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="cd1df-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="cd1df-172">Dodaj wyszukiwanie według gatunku do widoku indeksu</span><span class="sxs-lookup"><span data-stu-id="cd1df-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="cd1df-173">Aktualizacja `Index.cshtml` znaleziona w *widokach/filmach/* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="cd1df-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="cd1df-174">Bada wyrażenie lambda użyte w następującym Pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="cd1df-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="cd1df-175">W poprzednim kodzie `DisplayNameFor` pomocnik html sprawdza `Title` Właściwość, do której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="cd1df-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="cd1df-176">Ponieważ wyrażenie lambda jest sprawdzane zamiast oceniania, nie otrzymujesz naruszenia dostępu, gdy `model` , `model.Movies` , lub `model.Movies[0]` są `null` puste.</span><span class="sxs-lookup"><span data-stu-id="cd1df-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="cd1df-177">Gdy wyrażenie lambda zostanie obliczone (na przykład `@Html.DisplayFor(modelItem => item.Title)` ), wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="cd1df-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="cd1df-178">Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu tych elementów:</span><span class="sxs-lookup"><span data-stu-id="cd1df-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Okno przeglądarki z wynikamihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="cd1df-180">[Poprzedni](controller-methods-views.md) 
>  [Dalej](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="cd1df-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
