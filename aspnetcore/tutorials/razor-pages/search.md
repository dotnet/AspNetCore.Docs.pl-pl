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
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="0dc4e-103">Dodawanie wyszukiwania do ASP.NET podstawowych stron razor</span><span class="sxs-lookup"><span data-stu-id="0dc4e-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="0dc4e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0dc4e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="0dc4e-105">W poniższych sekcjach dodawane jest wyszukiwanie filmów według *gatunku* lub *nazwy.*</span><span class="sxs-lookup"><span data-stu-id="0dc4e-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="0dc4e-106">Dodaj następujące wyróżnione właściwości do *strony/filmy/index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="0dc4e-107">`SearchString`: zawiera tekst wprowadzony przez użytkowników w polu tekstowym wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="0dc4e-108">`SearchString`ma [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atrybut.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="0dc4e-109">`[BindProperty]`wiąże wartości formularza i ciągi zapytań o takiej samej nazwie jak właściwość.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="0dc4e-110">`(SupportsGet = true)`jest wymagane do powiązania z żądaniami GET.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="0dc4e-111">`Genres`: zawiera listę gatunków.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="0dc4e-112">`Genres`umożliwia użytkownikowi wybranie gatunku z listy.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="0dc4e-113">`SelectList`Wymaga`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="0dc4e-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="0dc4e-114">`MovieGenre`: zawiera określony gatunek wybrany przez użytkownika (na przykład "Western").</span><span class="sxs-lookup"><span data-stu-id="0dc4e-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="0dc4e-115">`Genres`i `MovieGenre` są używane w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="0dc4e-116">Zaktualizuj `OnGetAsync` metodę strony Indeks za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="0dc4e-117">Pierwszy wiersz `OnGetAsync` metody tworzy kwerendę [LINQ,](/dotnet/csharp/programming-guide/concepts/linq/) aby wybrać filmy:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="0dc4e-118">Kwerenda jest zdefiniowana *tylko* w tym momencie, **nie** został uruchomiony względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="0dc4e-119">Jeśli `SearchString` właściwość nie ma wartości null lub jest pusta, kwerenda filmów jest modyfikowana w celu filtrowania ciągu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="0dc4e-120">Kod `s => s.Title.Contains()` jest [wyrażeniem Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0dc4e-121">Lambdas są używane w opartych na metodach zapytań [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) jako `Contains` argumenty do standardowych metod operatora kwerendy, takich jak [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metody lub (używane w poprzednim kodzie).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="0dc4e-122">Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub gdy są modyfikowane `Contains` `OrderBy`przez wywołanie metody (na przykład `Where`, lub ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="0dc4e-123">Zamiast tego wykonywanie kwerendy jest odroczone.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="0dc4e-124">Oznacza to, że ocena wyrażenia jest opóźniona, dopóki jego `ToListAsync` zrealizowana wartość nie zostanie przesuń ponad lub metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="0dc4e-125">Zobacz [wykonywanie kwerend, aby](/dotnet/framework/data/adonet/ef/language-reference/query-execution) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0dc4e-126">Zawiera [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda jest uruchamiana w bazie danych, a nie w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="0dc4e-127">Wielkość liter w kwerendzie zależy od bazy danych i sortowania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="0dc4e-128">Na SQL `Contains` Server, mapuje do [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), który jest bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="0dc4e-129">W SQLite, z domyślnym sortowania, to rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="0dc4e-130">Przejdź do strony Filmy i dołącz ciąg `?searchString=Ghost` zapytania, `https://localhost:5001/Movies?searchString=Ghost`taki jak adres URL (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="0dc4e-131">Wyświetlane są filtrowane filmy.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-131">The filtered movies are displayed.</span></span>

![Widok indeksu](search/_static/ghost.png)

<span data-ttu-id="0dc4e-133">Jeśli do strony Indeks zostanie dodany następujący szablon trasy, ciąg wyszukiwania może zostać `https://localhost:5001/Movies/Ghost`przekazany jako segment adresu URL (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="0dc4e-134">Poprzednie ograniczenie trasy umożliwia wyszukiwanie tytułu jako danych trasy (segmentu adresu URL), a nie jako wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="0dc4e-135">In `?` `"{searchString?}"` oznacza, że jest to opcjonalny parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Widok indeksu ze słowem ghost dodanym do adresu URL i zwróconą listą filmów z dwoma filmami, Ghostbusters i Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="0dc4e-137">Środowisko uruchomieniowe ASP.NET Core używa [powiązania modelu](xref:mvc/models/model-binding) do `SearchString` ustawiania wartości właściwości`?searchString=Ghost`z ciągu`https://localhost:5001/Movies/Ghost`zapytania ( ) lub danych trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="0dc4e-138">W przypadku powiązania modelu nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="0dc4e-139">Nie można jednak oczekiwać, że użytkownicy zmodyfikują adres URL w celu wyszukania filmu.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="0dc4e-140">W tym kroku interfejs użytkownika jest dodawany do filtrowania filmów.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="0dc4e-141">Jeśli dodano ograniczenie `"{searchString?}"`trasy, usuń je.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="0dc4e-142">Otwórz plik *Pages/Movies/Index.cshtml* i `<form>` dodaj znaczniki wyróżnione w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="0dc4e-143">Tag `<form>` HTML używa następujących [pomocników tagów:](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="0dc4e-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="0dc4e-144">[Pomocnik znacznika formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="0dc4e-145">Po przesłaniu formularza ciąg filtru jest wysyłany do strony *Strony/Filmy/Indeks* za pomocą ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="0dc4e-146">Pomocnik tagu wejściowego</span><span class="sxs-lookup"><span data-stu-id="0dc4e-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="0dc4e-147">Zapisz zmiany i przetestuj filtr.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-147">Save the changes and test the filter.</span></span>

![Widok indeksu ze słowem ghost wpisanym w polu tekstowym filtru tytuł](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="0dc4e-149">Szukaj według gatunku</span><span class="sxs-lookup"><span data-stu-id="0dc4e-149">Search by genre</span></span>

<span data-ttu-id="0dc4e-150">Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="0dc4e-151">Poniższy kod jest kwerendą LINQ, która pobiera wszystkie gatunki z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="0dc4e-152">Gatunki `SelectList` są tworzone przez projekcję różnych gatunków.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="0dc4e-153">Dodawanie wyszukiwania według gatunku do strony Razor</span><span class="sxs-lookup"><span data-stu-id="0dc4e-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="0dc4e-154">Aktualizuj *index.cshtml w* następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="0dc4e-155">Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0dc4e-156">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0dc4e-156">Additional resources</span></span>

* [<span data-ttu-id="0dc4e-157">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="0dc4e-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="0dc4e-158">[Poprzedni: Aktualizowanie stron](xref:tutorials/razor-pages/da1)
> [Dalej: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="0dc4e-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="0dc4e-159">W poniższych sekcjach dodawane jest wyszukiwanie filmów według *gatunku* lub *nazwy.*</span><span class="sxs-lookup"><span data-stu-id="0dc4e-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="0dc4e-160">Dodaj następujące wyróżnione właściwości do *strony/filmy/index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="0dc4e-161">`SearchString`: zawiera tekst wprowadzony przez użytkowników w polu tekstowym wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="0dc4e-162">`SearchString`ma [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atrybut.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="0dc4e-163">`[BindProperty]`wiąże wartości formularza i ciągi zapytań o takiej samej nazwie jak właściwość.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="0dc4e-164">`(SupportsGet = true)`jest wymagane do powiązania z żądaniami GET.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="0dc4e-165">`Genres`: zawiera listę gatunków.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="0dc4e-166">`Genres`umożliwia użytkownikowi wybranie gatunku z listy.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="0dc4e-167">`SelectList`Wymaga`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="0dc4e-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="0dc4e-168">`MovieGenre`: zawiera określony gatunek wybrany przez użytkownika (na przykład "Western").</span><span class="sxs-lookup"><span data-stu-id="0dc4e-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="0dc4e-169">`Genres`i `MovieGenre` są używane w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="0dc4e-170">Zaktualizuj `OnGetAsync` metodę strony Indeks za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="0dc4e-171">Pierwszy wiersz `OnGetAsync` metody tworzy kwerendę [LINQ,](/dotnet/csharp/programming-guide/concepts/linq/) aby wybrać filmy:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="0dc4e-172">Kwerenda jest zdefiniowana *tylko* w tym momencie, **nie** został uruchomiony względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="0dc4e-173">Jeśli `SearchString` właściwość nie ma wartości null lub jest pusta, kwerenda filmów jest modyfikowana w celu filtrowania ciągu wyszukiwania:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="0dc4e-174">Kod `s => s.Title.Contains()` jest [wyrażeniem Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0dc4e-175">Lambdas są używane w opartych na metodach zapytań [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) jako `Contains` argumenty do standardowych metod operatora kwerendy, takich jak [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metody lub (używane w poprzednim kodzie).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="0dc4e-176">Zapytania LINQ nie są wykonywane, gdy są zdefiniowane lub gdy są modyfikowane `Contains` `OrderBy`przez wywołanie metody (na przykład `Where`, lub ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="0dc4e-177">Zamiast tego wykonywanie kwerendy jest odroczone.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="0dc4e-178">Oznacza to, że ocena wyrażenia jest opóźniona, dopóki jego `ToListAsync` zrealizowana wartość nie zostanie przesuń ponad lub metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="0dc4e-179">Zobacz [wykonywanie kwerend, aby](/dotnet/framework/data/adonet/ef/language-reference/query-execution) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="0dc4e-180">**Uwaga:** Zawiera [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda jest uruchamiana w bazie danych, a nie w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="0dc4e-181">Wielkość liter w kwerendzie zależy od bazy danych i sortowania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="0dc4e-182">Na SQL `Contains` Server, mapuje do [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), który jest bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="0dc4e-183">W SQLite, z domyślnym sortowania, to rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="0dc4e-184">Przejdź do strony Filmy i dołącz ciąg `?searchString=Ghost` zapytania, `https://localhost:5001/Movies?searchString=Ghost`taki jak adres URL (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="0dc4e-185">Wyświetlane są filtrowane filmy.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-185">The filtered movies are displayed.</span></span>

![Widok indeksu](search/_static/ghost.png)

<span data-ttu-id="0dc4e-187">Jeśli do strony Indeks zostanie dodany następujący szablon trasy, ciąg wyszukiwania może zostać `https://localhost:5001/Movies/Ghost`przekazany jako segment adresu URL (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="0dc4e-188">Poprzednie ograniczenie trasy umożliwia wyszukiwanie tytułu jako danych trasy (segmentu adresu URL), a nie jako wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="0dc4e-189">In `?` `"{searchString?}"` oznacza, że jest to opcjonalny parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Widok indeksu ze słowem ghost dodanym do adresu URL i zwróconą listą filmów z dwoma filmami, Ghostbusters i Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="0dc4e-191">Środowisko uruchomieniowe ASP.NET Core używa [powiązania modelu](xref:mvc/models/model-binding) do `SearchString` ustawiania wartości właściwości`?searchString=Ghost`z ciągu`https://localhost:5001/Movies/Ghost`zapytania ( ) lub danych trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="0dc4e-192">W przypadku powiązania modelu nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="0dc4e-193">Nie można jednak oczekiwać, że użytkownicy zmodyfikują adres URL w celu wyszukania filmu.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="0dc4e-194">W tym kroku interfejs użytkownika jest dodawany do filtrowania filmów.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="0dc4e-195">Jeśli dodano ograniczenie `"{searchString?}"`trasy, usuń je.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="0dc4e-196">Otwórz plik *Pages/Movies/Index.cshtml* i `<form>` dodaj znaczniki wyróżnione w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="0dc4e-197">Tag `<form>` HTML używa następujących [pomocników tagów:](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="0dc4e-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="0dc4e-198">[Pomocnik znacznika formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0dc4e-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="0dc4e-199">Po przesłaniu formularza ciąg filtru jest wysyłany do strony *Strony/Filmy/Indeks* za pomocą ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="0dc4e-200">Pomocnik tagu wejściowego</span><span class="sxs-lookup"><span data-stu-id="0dc4e-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="0dc4e-201">Zapisz zmiany i przetestuj filtr.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-201">Save the changes and test the filter.</span></span>

![Widok indeksu ze słowem ghost wpisanym w polu tekstowym filtru tytuł](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="0dc4e-203">Szukaj według gatunku</span><span class="sxs-lookup"><span data-stu-id="0dc4e-203">Search by genre</span></span>

<span data-ttu-id="0dc4e-204">Zaktualizuj `OnGetAsync` metodę za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="0dc4e-205">Poniższy kod jest kwerendą LINQ, która pobiera wszystkie gatunki z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="0dc4e-206">Gatunki `SelectList` są tworzone przez projekcję różnych gatunków.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="0dc4e-207">Dodawanie wyszukiwania według gatunku do strony Razor</span><span class="sxs-lookup"><span data-stu-id="0dc4e-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="0dc4e-208">Aktualizuj *index.cshtml w* następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0dc4e-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="0dc4e-209">Przetestuj aplikację, wyszukując według gatunku, tytułu filmu i obu.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="0dc4e-210">Poprzedni kod używa [pomocnika wybierania znaczników](xref:mvc/views/working-with-forms#the-select-tag-helper) i pomocnika znaczników opcji.</span><span class="sxs-lookup"><span data-stu-id="0dc4e-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0dc4e-211">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0dc4e-211">Additional resources</span></span>

* [<span data-ttu-id="0dc4e-212">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="0dc4e-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="0dc4e-213">[Poprzedni: Aktualizowanie stron](xref:tutorials/razor-pages/da1)
> [Dalej: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="0dc4e-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
