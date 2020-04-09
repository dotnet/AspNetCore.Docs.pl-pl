---
title: Dodawanie wyszukiwania do aplikacji core mvc ASP.NET
author: rick-anderson
description: Pokazuje, jak dodać wyszukiwanie do podstawowej aplikacji Core MVC ASP.NET
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662870"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="91564-103">Dodawanie wyszukiwania do aplikacji core mvc ASP.NET</span><span class="sxs-lookup"><span data-stu-id="91564-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="91564-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91564-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="91564-105">W tej sekcji do tej sekcji `Index` można dodać funkcję wyszukiwania do metody akcji, która umożliwia wyszukiwanie filmów według *gatunku* lub *nazwy*.</span><span class="sxs-lookup"><span data-stu-id="91564-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="91564-106">Zaktualizuj metodę znalezioną `Index` w *controllers/MoviesController.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="91564-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="91564-107">Pierwszy wiersz metody `Index` akcji tworzy kwerendę [LINQ,](/dotnet/standard/using-linq) aby wybrać filmy:</span><span class="sxs-lookup"><span data-stu-id="91564-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="91564-108">Kwerenda jest zdefiniowana *tylko* w tym momencie, **nie** został uruchomiony względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="91564-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="91564-109">Jeśli `searchString` parametr zawiera ciąg, kwerenda filmów jest modyfikowana w celu filtrowania wartości ciągu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="91564-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="91564-110">Powyższy `s => s.Title.Contains()` kod jest [wyrażeniem Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="91564-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="91564-111">Lambdas są używane w opartych na metodach zapytań [LINQ](/dotnet/standard/using-linq) jako `Contains` argumenty do standardowych metod operatora kwerendy, takich jak [Where](/dotnet/api/system.linq.enumerable.where) metody lub (używane w kodzie powyżej).</span><span class="sxs-lookup"><span data-stu-id="91564-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="91564-112">Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub gdy są `Where`modyfikowane przez wywołanie metody, takiej jak , `Contains`lub `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="91564-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="91564-113">Zamiast tego wykonywanie kwerendy jest odroczone.</span><span class="sxs-lookup"><span data-stu-id="91564-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="91564-114">Oznacza to, że ocena wyrażenia jest opóźniona, dopóki jego zrealizowana `ToListAsync` wartość jest rzeczywiście iterowana lub metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="91564-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="91564-115">Aby uzyskać więcej informacji na temat odroczonego wykonywania kwerendy, zobacz [Wykonywanie kwerend](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="91564-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="91564-116">Uwaga: [Zawiera](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda jest uruchamiana w bazie danych, a nie w kodzie c# pokazano powyżej.</span><span class="sxs-lookup"><span data-stu-id="91564-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="91564-117">Wielkość liter w kwerendzie zależy od bazy danych i sortowania.</span><span class="sxs-lookup"><span data-stu-id="91564-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="91564-118">Na SQL Server, [Zawiera](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy do [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), który jest bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="91564-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="91564-119">W SQLite, z domyślnym sortowania, to rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="91564-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="91564-120">Przejdź do adresu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="91564-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="91564-121">Dołącz ciąg zapytania, `?searchString=Ghost` taki jak adres URL.</span><span class="sxs-lookup"><span data-stu-id="91564-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="91564-122">Wyświetlane są filtrowane filmy.</span><span class="sxs-lookup"><span data-stu-id="91564-122">The filtered movies are displayed.</span></span>

![Widok indeksu](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="91564-124">Jeśli zmienisz podpis `Index` metody, aby mieć `id`parametr `id` o nazwie, `{id}` parametr będzie zgodny z opcjonalnym symbolem zastępczym dla tras domyślnych ustawionych w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="91564-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="91564-125">Zmień parametr `id` na i wszystkie `searchString` wystąpienia `id`zmiany na .</span><span class="sxs-lookup"><span data-stu-id="91564-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="91564-126">Poprzednia `Index` metoda:</span><span class="sxs-lookup"><span data-stu-id="91564-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="91564-127">Zaktualizowana `Index` metoda `id` z parametrem:</span><span class="sxs-lookup"><span data-stu-id="91564-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="91564-128">Teraz możesz przekazać tytuł wyszukiwania jako dane trasy (segment adresu URL), a nie jako wartość ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="91564-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Widok indeksu ze słowem ghost dodanym do adresu URL i zwróconą listą filmów z dwoma filmami, Ghostbusters i Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="91564-130">Nie można jednak oczekiwać, że użytkownicy będą modyfikować adres URL za każdym razem, gdy chcą wyszukać film.</span><span class="sxs-lookup"><span data-stu-id="91564-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="91564-131">Teraz dodasz elementy interfejsu użytkownika, aby ułatwić im filtrowanie filmów.</span><span class="sxs-lookup"><span data-stu-id="91564-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="91564-132">Jeśli zmieniono podpis `Index` metody, aby przetestować sposób `ID` przekazywania parametru związanego z trasą, zmień go z powrotem, tak aby miał parametr o nazwie: `searchString`</span><span class="sxs-lookup"><span data-stu-id="91564-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="91564-133">Otwórz plik *Widoki/Filmy/Index.cshtml* i `<form>` dodaj znaczniki wyróżnione poniżej:</span><span class="sxs-lookup"><span data-stu-id="91564-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="91564-134">Tag `<form>` HTML używa [pomocnika znacznika formularza,](xref:mvc/views/working-with-forms)więc podczas przesyłania formularza ciąg filtru jest księgowy do `Index` akcji kontrolera filmów.</span><span class="sxs-lookup"><span data-stu-id="91564-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="91564-135">Zapisz zmiany, a następnie przetestuj filtr.</span><span class="sxs-lookup"><span data-stu-id="91564-135">Save your changes and then test the filter.</span></span>

![Widok indeksu ze słowem ghost wpisanym w polu tekstowym filtru tytuł](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="91564-137">Nie ma `[HttpPost]` przeciążenia `Index` metody, jak można się spodziewać.</span><span class="sxs-lookup"><span data-stu-id="91564-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="91564-138">Nie jest to potrzebne, ponieważ metoda nie zmienia stanu aplikacji, tylko filtrowanie danych.</span><span class="sxs-lookup"><span data-stu-id="91564-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="91564-139">Można dodać następującą `[HttpPost] Index` metodę.</span><span class="sxs-lookup"><span data-stu-id="91564-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="91564-140">Parametr `notUsed` jest używany do tworzenia przeciążenia `Index` dla metody.</span><span class="sxs-lookup"><span data-stu-id="91564-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="91564-141">Porozmawiamy o tym w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="91564-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="91564-142">Jeśli dodasz tę metodę, wywoływacz `[HttpPost] Index` akcji będzie `[HttpPost] Index` zgodny z metodą, a metoda będzie działać, jak pokazano na obrazku poniżej.</span><span class="sxs-lookup"><span data-stu-id="91564-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Okno przeglądarki z odpowiedzią aplikacji z indeksu HttpPost: filtr na duchu](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="91564-144">Jednak nawet jeśli dodasz `[HttpPost]` tę `Index` wersję metody, istnieje ograniczenie w jaki sposób to wszystko zostało zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="91564-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="91564-145">Wyobraź sobie, że chcesz dołekować określone wyszukiwanie lub wysłać link do znajomych, który mogą kliknąć, aby zobaczyć tę samą filtrowane listy filmów.</span><span class="sxs-lookup"><span data-stu-id="91564-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="91564-146">Należy zauważyć, że adres URL żądania HTTP POST jest taki sam jak adres URL żądania GET (localhost:{PORT}/Movies/Index) - w adresie URL nie ma żadnych informacji o wyszukiwaniu.</span><span class="sxs-lookup"><span data-stu-id="91564-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="91564-147">Informacje o ciągu wyszukiwania są wysyłane do serwera jako [wartość pola formularza](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="91564-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="91564-148">Możesz to sprawdzić za pomocą narzędzi programistycznych przeglądarki lub doskonałego [narzędzia Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="91564-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="91564-149">Na poniższej ilustracji przedstawiono narzędzia programistyczne przeglądarki Chrome:</span><span class="sxs-lookup"><span data-stu-id="91564-149">The image below shows the Chrome browser Developer tools:</span></span>

![Karta Sieć narzędzi deweloperskich w programie Microsoft Edge przedstawiająca treść żądania z wyszukiwanąciątą wartością ducha](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="91564-151">Parametr wyszukiwania i token [XSRF](xref:security/anti-request-forgery) można zobaczyć w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="91564-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="91564-152">Uwaga, jak wspomniano w poprzednim samouczku, [Pomocnik znaczników formularza](xref:mvc/views/working-with-forms) generuje token antyzwiązany z fałszerszem [XSRF.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="91564-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="91564-153">Nie modyfikujemy danych, więc nie musimy sprawdzać poprawności tokenu w metodzie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="91564-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="91564-154">Ponieważ parametr wyszukiwania znajduje się w treści żądania, a nie w adresie URL, nie można przechwycić tych informacji wyszukiwania w celu dołek lub udostępnienia innym osobom.</span><span class="sxs-lookup"><span data-stu-id="91564-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="91564-155">Rozwiąż to, określając `HTTP GET` żądanie, które należy znaleźć w pliku *Views/Movies/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="91564-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="91564-156">Teraz po przesłaniu wyszukiwania adres URL zawiera ciąg zapytania wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="91564-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="91564-157">Wyszukiwanie przejdzie również `HttpGet Index` do metody akcji, nawet `HttpPost Index` jeśli masz metodę.</span><span class="sxs-lookup"><span data-stu-id="91564-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Okno przeglądarki pokazujące searchString = ghost w adresie URL i filmy wrócił, Ghostbusters i Ghostbusters 2, zawierają słowo duch](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="91564-159">Następujące znaczniki pokazuje zmiany `form` do tagu:</span><span class="sxs-lookup"><span data-stu-id="91564-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="91564-160">Dodaj wyszukiwanie według gatunku</span><span class="sxs-lookup"><span data-stu-id="91564-160">Add Search by genre</span></span>

<span data-ttu-id="91564-161">Dodaj następującą `MovieGenreViewModel` klasę do folderu *Modele:*</span><span class="sxs-lookup"><span data-stu-id="91564-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="91564-162">Model widoku gatunku filmu będzie zawierał:</span><span class="sxs-lookup"><span data-stu-id="91564-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="91564-163">Lista filmów.</span><span class="sxs-lookup"><span data-stu-id="91564-163">A list of movies.</span></span>
* <span data-ttu-id="91564-164">A `SelectList` zawierający listę gatunków.</span><span class="sxs-lookup"><span data-stu-id="91564-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="91564-165">Dzięki temu użytkownik może wybrać gatunek z listy.</span><span class="sxs-lookup"><span data-stu-id="91564-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="91564-166">`MovieGenre`, który zawiera wybrany gatunek.</span><span class="sxs-lookup"><span data-stu-id="91564-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="91564-167">`SearchString`, który zawiera tekst wprowadzany przez użytkowników w polu tekstowym wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="91564-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="91564-168">`Index` Zastąp `MoviesController.cs` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="91564-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="91564-169">Poniższy kod `LINQ` jest kwerendą, która pobiera wszystkie gatunki z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="91564-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="91564-170">Gatunki `SelectList` są tworzone przez projekcję różnych gatunków (nie chcemy, aby nasza lista wybranych miała zduplikowane gatunki).</span><span class="sxs-lookup"><span data-stu-id="91564-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="91564-171">Gdy użytkownik wyszukuje element, wartość wyszukiwania jest zachowywana w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="91564-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="91564-172">Dodawanie wyszukiwania według gatunku do widoku Indeks</span><span class="sxs-lookup"><span data-stu-id="91564-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="91564-173">Aktualizacja `Index.cshtml` znaleziona w *widokach/filmach/* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="91564-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="91564-174">Sprawdź wyrażenie lambda używane w następującym pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="91564-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="91564-175">W poprzednim kodzie `DisplayNameFor` Pomocnik HTML sprawdza `Title` właściwość, do którego odwołuje się wyrażenie lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="91564-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="91564-176">Ponieważ wyrażenie lambda jest kontrolowane, a nie oceniane, nie `model`otrzymasz naruszenia zasad dostępu, gdy , `model.Movies`lub są `model.Movies[0]` `null` lub puste.</span><span class="sxs-lookup"><span data-stu-id="91564-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="91564-177">Gdy wyrażenie lambda jest oceniane `@Html.DisplayFor(modelItem => item.Title)`(na przykład), wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="91564-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="91564-178">Przetestuj aplikację, wyszukując według gatunku, według tytułu filmu i przez oba:</span><span class="sxs-lookup"><span data-stu-id="91564-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Okno przeglądarki z wynikamihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="91564-180">[Poprzedni](controller-methods-views.md)
> [następny](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="91564-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
