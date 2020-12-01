---
title: Część 5, aktualizowanie wygenerowanych stron
author: rick-anderson
description: Część 5 serii samouczków na Razor stronach.
ms.author: riande
ms.date: 09/20/2020
no-loc:
- Index
- appsettings.json
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 460950413d1dd2d3539c1d62b0eb11f6bb5144a9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419970"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="7ca4f-103">Część 5. aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ca4f-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="7ca4f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ca4f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ca4f-105">Aplikacja ze szkieletem jest dobrze uruchomiona, ale prezentacja nie jest idealnym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="7ca4f-106">**ReleaseDate** powinny mieć dwa słowa — **Data wydania**.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplikacja filmowa otwarta w przeglądarce Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="7ca4f-108">Aktualizowanie wygenerowanego kodu</span><span class="sxs-lookup"><span data-stu-id="7ca4f-108">Update the generated code</span></span>

<span data-ttu-id="7ca4f-109">Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze wyświetlane w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="7ca4f-110">W poprzednim kodzie:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-110">In the previous code:</span></span>

* <span data-ttu-id="7ca4f-111">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych umożliwia Entity Framework Core poprawne mapowanie `Price` na walutę w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="7ca4f-112">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="7ca4f-113">Atrybut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) określa nazwę wyświetlaną pola.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="7ca4f-114">W poprzednim kodzie "Data wydania" zamiast "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="7ca4f-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="7ca4f-115">Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7ca4f-116">Informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="7ca4f-117">[Adnotacje DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) zostały omówione w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="7ca4f-118">Przejdź do *stron/filmów* i umieść kursor na linku **edycji** , aby zobaczyć docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym adresem URL linku https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="7ca4f-120">Linki **Edytuj**, **szczegóły** i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Films/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="7ca4f-121">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="7ca4f-122">W powyższym kodzie [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamicznie generuje `href` wartość atrybutu HTML ze Razor strony (trasa jest względna), `asp-page` i identyfikator trasy ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="7ca4f-123">Aby uzyskać więcej informacji, zobacz temat [generowanie adresów URL dla stron](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="7ca4f-124">Użyj **widoku źródła** z przeglądarki, aby sprawdzić wygenerowane znaczniki.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="7ca4f-125">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="7ca4f-126">Linki dynamicznie generowane przekażą identyfikator filmu ciągiem zapytania.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="7ca4f-127">Na przykład, `?id=1` w `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="7ca4f-128">Dodawanie szablonu trasy</span><span class="sxs-lookup"><span data-stu-id="7ca4f-128">Add route template</span></span>

<span data-ttu-id="7ca4f-129">Zaktualizuj strony Edytuj, szczegóły i Usuń, Razor Aby użyć `{id:int}` szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="7ca4f-130">Zmień dyrektywę Page dla każdej z tych stron z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="7ca4f-131">Uruchom aplikację, a następnie Wyświetl źródło.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-131">Run the app and then view source.</span></span>

<span data-ttu-id="7ca4f-132">Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="7ca4f-133">Żądanie do strony z `{id:int}` szablonem trasy, który **nie** zawiera liczby całkowitej zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="7ca4f-134">Na przykład zwróci `https://localhost:5001/Movies/Details` błąd 404.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="7ca4f-135">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="7ca4f-136">Przetestuj zachowanie `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="7ca4f-137">Ustaw dyrektywę Page na stronie */filmy/details. cshtml* na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="7ca4f-138">Ustaw punkt przerwania w elemencie `public async Task<IActionResult> OnGetAsync(int? id)` *Pages/Films/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="7ca4f-139">Przejdź do adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="7ca4f-140">W przypadku `@page "{id:int}"` dyrektywy punkt przerwania nigdy nie trafi.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="7ca4f-141">Aparat routingu zwraca protokół HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="7ca4f-142">Przy użyciu `@page "{id:int?}"` `OnGetAsync` Metoda zwraca `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="7ca4f-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="7ca4f-143">Przejrzyj obsługę wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="7ca4f-143">Review concurrency exception handling</span></span>

<span data-ttu-id="7ca4f-144">Przejrzyj `OnPostAsync` metodę w pliku *Pages/Films/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="7ca4f-145">Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a drugi wpisuje zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="7ca4f-146">Aby przetestować `catch` blok:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="7ca4f-147">Ustaw punkt przerwania na `catch (DbUpdateConcurrencyException)` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="7ca4f-148">Wybierz pozycję **Edytuj** dla filmu, wprowadź zmiany, ale nie wprowadzaj opcji **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="7ca4f-149">W innym oknie przeglądarki wybierz łącze **Usuń** dla tego samego filmu, a następnie usuń film.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="7ca4f-150">W poprzednim oknie przeglądarki Opublikuj zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="7ca4f-151">Kod produkcyjny może chcieć wykryć konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="7ca4f-152">Aby uzyskać więcej informacji, zobacz sekcję [obsługa konfliktów współbieżności](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="7ca4f-153">Przegląd ogłaszania i powiązania</span><span class="sxs-lookup"><span data-stu-id="7ca4f-153">Posting and binding review</span></span>

<span data-ttu-id="7ca4f-154">Przejrzyj *stronę/filmy/plik Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="7ca4f-155">Gdy żądanie HTTP GET zostanie wysłane do strony filmy/Edycja, na przykład `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="7ca4f-156">`OnGetAsync`Metoda pobiera film z bazy danych i zwraca `Page` metodę.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="7ca4f-157">`Page`Metoda renderuje stronę *stron/filmów/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="7ca4f-158">Plik *Pages/Movies/Edit. cshtml* zawiera dyrektywę model `@model RazorPagesMovie.Pages.Movies.EditModel` , która sprawia, że model filmu jest dostępny na stronie.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="7ca4f-159">Zostanie wyświetlony formularz edycji z wartościami z filmu.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="7ca4f-160">Po opublikowaniu strony filmy/Edycja:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="7ca4f-161">Wartości formularza na stronie są powiązane z `Movie` właściwością.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="7ca4f-162">Ten `[BindProperty]` atrybut włącza [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="7ca4f-163">W przypadku wystąpienia błędów w stanie modelu, na przykład, `ReleaseDate` nie można przekonwertować na datę, formularz jest ponownie wyświetlany z przesłanymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="7ca4f-164">Jeśli nie ma żadnych błędów modelu, film zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="7ca4f-165">Metody GET protokołu HTTP na Index stronach, Create i DELETE są Razor zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="7ca4f-166">Metoda POST protokołu HTTP `OnPostAsync` na stronie Tworzenie jest Razor zgodna z podobnym wzorcem do `OnPostAsync` metody na Razor stronie edytowania.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ca4f-167">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7ca4f-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7ca4f-168">[Poprzedni: Pracuj z bazą danych](xref:tutorials/razor-pages/sql) 
>  [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="7ca4f-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ca4f-169">Aplikacja ze szkieletem jest dobrze uruchomiona, ale prezentacja nie jest idealnym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="7ca4f-170">**ReleaseDate** powinny mieć dwa słowa — **Data wydania**.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplikacja filmowa otwarta w przeglądarce Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="7ca4f-172">Aktualizowanie wygenerowanego kodu</span><span class="sxs-lookup"><span data-stu-id="7ca4f-172">Update the generated code</span></span>

<span data-ttu-id="7ca4f-173">Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze wyświetlane w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="7ca4f-174">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych umożliwia Entity Framework Core poprawne mapowanie `Price` na walutę w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="7ca4f-175">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="7ca4f-176">[Adnotacje DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) zostały omówione w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="7ca4f-177">Atrybut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) określa, co ma być wyświetlane dla nazwy pola, w tym przypadku "Data wydania" zamiast "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="7ca4f-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="7ca4f-178">Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ), więc informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="7ca4f-179">Przejdź do stron/filmów i umieść kursor na linku **edycji** , aby zobaczyć docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym adresem URL linku http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="7ca4f-181">Linki **Edytuj**, **szczegóły** i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Films/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="7ca4f-182">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="7ca4f-183">W poprzednim kodzie, `AnchorTagHelper` dynamicznie generuje `href` wartość atrybutu HTML ze Razor strony (trasa jest względna), `asp-page` i identyfikator trasy ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="7ca4f-184">Aby uzyskać więcej informacji, zobacz [generowanie adresów URL na stronach](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="7ca4f-185">Użyj **widoku źródła** z przeglądarki, aby sprawdzić wygenerowane znaczniki.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="7ca4f-186">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="7ca4f-187">Linki dynamicznie generowane przekażą identyfikator filmu ciągiem zapytania.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="7ca4f-188">Na przykład, `?id=1` w  `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="7ca4f-189">Zaktualizuj strony edytowania, szczegółów i usuwania, Razor Aby użyć szablonu trasy "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="7ca4f-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="7ca4f-190">Zmień dyrektywę Page dla każdej z tych stron z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="7ca4f-191">Uruchom aplikację, a następnie Wyświetl źródło.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-191">Run the app and then view source.</span></span> <span data-ttu-id="7ca4f-192">Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="7ca4f-193">Żądanie do strony z szablonem trasy "{ID: int}", który **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="7ca4f-194">Na przykład zwróci `https://localhost:5001/Movies/Details` błąd 404.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="7ca4f-195">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="7ca4f-196">Aby przetestować zachowanie `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="7ca4f-197">Ustaw dyrektywę Page na stronie */filmy/details. cshtml* na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="7ca4f-198">Ustaw punkt przerwania w elemencie `public async Task<IActionResult> OnGetAsync(int? id)` *Pages/Films/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="7ca4f-199">Przejdź do adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="7ca4f-200">W przypadku `@page "{id:int}"` dyrektywy punkt przerwania nigdy nie trafi.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="7ca4f-201">Aparat routingu zwraca protokół HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="7ca4f-202">Przy użyciu `@page "{id:int?}"` `OnGetAsync` Metoda zwraca `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="7ca4f-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="7ca4f-203">Przejrzyj obsługę wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="7ca4f-203">Review concurrency exception handling</span></span>

<span data-ttu-id="7ca4f-204">Przejrzyj `OnPostAsync` metodę w pliku *Pages/Films/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="7ca4f-205">Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a pozostałe wpisy wprowadzane przez klienta do filmu.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="7ca4f-206">Aby przetestować `catch` blok:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-206">To test the `catch` block:</span></span>

* <span data-ttu-id="7ca4f-207">Ustaw punkt przerwania na `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="7ca4f-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="7ca4f-208">Wybierz pozycję **Edytuj** dla filmu, wprowadź zmiany, ale nie wprowadzaj opcji **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="7ca4f-209">W innym oknie przeglądarki wybierz łącze **Usuń** dla tego samego filmu, a następnie usuń film.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="7ca4f-210">W poprzednim oknie przeglądarki Opublikuj zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="7ca4f-211">Kod produkcyjny może chcieć wykryć konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="7ca4f-212">Aby uzyskać więcej informacji, zobacz sekcję [obsługa konfliktów współbieżności](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="7ca4f-213">Przegląd ogłaszania i powiązania</span><span class="sxs-lookup"><span data-stu-id="7ca4f-213">Posting and binding review</span></span>

<span data-ttu-id="7ca4f-214">Przejrzyj *stronę/filmy/plik Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="7ca4f-215">Gdy żądanie HTTP GET zostanie wysłane do strony filmy/Edycja, na przykład `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="7ca4f-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="7ca4f-216">`OnGetAsync`Metoda pobiera film z bazy danych i zwraca `Page` metodę.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="7ca4f-217">`Page`Metoda renderuje stronę *stron/filmów/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="7ca4f-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="7ca4f-218">Plik *Pages/Movies/Edit. cshtml* zawiera dyrektywę model `@model RazorPagesMovie.Pages.Movies.EditModel` , która sprawia, że model filmu jest dostępny na stronie.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="7ca4f-219">Zostanie wyświetlony formularz edycji z wartościami z filmu.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="7ca4f-220">Po opublikowaniu strony filmy/Edycja:</span><span class="sxs-lookup"><span data-stu-id="7ca4f-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="7ca4f-221">Wartości formularza na stronie są powiązane z `Movie` właściwością.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="7ca4f-222">Ten `[BindProperty]` atrybut włącza [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="7ca4f-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="7ca4f-223">W przypadku wystąpienia błędów w stanie modelu na przykład `ReleaseDate` nie można przekonwertować ich na datę, formularz zostanie wyświetlony z przesłanymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="7ca4f-224">Jeśli nie ma żadnych błędów modelu, film zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="7ca4f-225">Metody GET protokołu HTTP na Index stronach, Create i DELETE są Razor zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="7ca4f-226">Metoda POST protokołu HTTP `OnPostAsync` na stronie Tworzenie jest Razor zgodna z podobnym wzorcem do `OnPostAsync` metody na Razor stronie edytowania.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="7ca4f-227">W następnym samouczku zostanie dodane Wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="7ca4f-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ca4f-228">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7ca4f-228">Additional resources</span></span>

* [<span data-ttu-id="7ca4f-229">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="7ca4f-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="7ca4f-230">[Poprzedni: Pracuj z bazą danych](xref:tutorials/razor-pages/sql) 
>  [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="7ca4f-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
