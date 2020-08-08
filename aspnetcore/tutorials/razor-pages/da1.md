---
title: Część 5. aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core
author: rick-anderson
description: Część 5 serii samouczków na Razor stronach.
ms.author: riande
ms.date: 12/20/2018
no-loc:
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
ms.openlocfilehash: d8f1a256bfa3491ff1cc7f38a23c61beb719bd49
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022098"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="deafa-103">Część 5. aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="deafa-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="deafa-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="deafa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="deafa-105">Aplikacja ze szkieletem jest dobrze uruchomiona, ale prezentacja nie jest idealnym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="deafa-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="deafa-106">**ReleaseDate** powinna być **datą wydania** (dwa wyrazy).</span><span class="sxs-lookup"><span data-stu-id="deafa-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikacja filmowa otwarta w przeglądarce Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="deafa-108">Aktualizowanie wygenerowanego kodu</span><span class="sxs-lookup"><span data-stu-id="deafa-108">Update the generated code</span></span>

<span data-ttu-id="deafa-109">Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze wyświetlane w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="deafa-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="deafa-110">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych umożliwia Entity Framework Core poprawne mapowanie `Price` na walutę w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="deafa-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="deafa-111">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="deafa-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="deafa-112">[Adnotacje DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) zostały omówione w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="deafa-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="deafa-113">Atrybut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="deafa-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="deafa-114">Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="deafa-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="deafa-115">Przejdź do stron/filmów i umieść kursor na linku **edycji** , aby zobaczyć docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="deafa-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym adresem URL linku http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="deafa-117">Linki **Edytuj**, **szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Films/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="deafa-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="deafa-118">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="deafa-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="deafa-119">W poprzednim kodzie, `AnchorTagHelper` dynamicznie generuje `href` wartość atrybutu HTML ze Razor strony (trasa jest względna), `asp-page` i identyfikator trasy ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="deafa-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="deafa-120">Aby uzyskać więcej informacji, zobacz [generowanie adresów URL na stronach](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="deafa-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="deafa-121">Użyj **widoku źródła** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki.</span><span class="sxs-lookup"><span data-stu-id="deafa-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="deafa-122">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="deafa-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="deafa-123">Dynamicznie generowane linki przekażą identyfikator filmu z ciągiem zapytania (na przykład `?id=1` w `https://localhost:5001/Movies/Details?id=1` ).</span><span class="sxs-lookup"><span data-stu-id="deafa-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="deafa-124">Dodawanie szablonu trasy</span><span class="sxs-lookup"><span data-stu-id="deafa-124">Add route template</span></span>

<span data-ttu-id="deafa-125">Zaktualizuj strony edytowania, szczegółów i usuwania, Razor Aby użyć szablonu trasy "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="deafa-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="deafa-126">Zmień dyrektywę Page dla każdej z tych stron z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="deafa-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="deafa-127">Uruchom aplikację, a następnie Wyświetl źródło.</span><span class="sxs-lookup"><span data-stu-id="deafa-127">Run the app and then view source.</span></span> <span data-ttu-id="deafa-128">Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="deafa-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="deafa-129">Żądanie do strony z szablonem trasy "{ID: int}", który **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="deafa-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="deafa-130">Na przykład zwróci `http://localhost:5000/Movies/Details` błąd 404.</span><span class="sxs-lookup"><span data-stu-id="deafa-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="deafa-131">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="deafa-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="deafa-132">Aby przetestować zachowanie `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="deafa-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="deafa-133">Ustaw dyrektywę Page na stronie */filmy/details. cshtml* na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="deafa-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="deafa-134">Ustaw punkt przerwania `public async Task<IActionResult> OnGetAsync(int? id)` (w obszarze *strony/filmy/szczegóły. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="deafa-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="deafa-135">Przejdź do adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="deafa-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="deafa-136">W przypadku `@page "{id:int}"` dyrektywy punkt przerwania nigdy nie trafi.</span><span class="sxs-lookup"><span data-stu-id="deafa-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="deafa-137">Aparat routingu zwraca protokół HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="deafa-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="deafa-138">Przy użyciu `@page "{id:int?}"` `OnGetAsync` Metoda zwraca `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="deafa-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="deafa-139">Przejrzyj obsługę wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="deafa-139">Review concurrency exception handling</span></span>

<span data-ttu-id="deafa-140">Przejrzyj `OnPostAsync` metodę w pliku *Pages/Films/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="deafa-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="deafa-141">Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a pozostałe wpisy wprowadzane przez klienta do filmu.</span><span class="sxs-lookup"><span data-stu-id="deafa-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="deafa-142">Aby przetestować `catch` blok:</span><span class="sxs-lookup"><span data-stu-id="deafa-142">To test the `catch` block:</span></span>

* <span data-ttu-id="deafa-143">Ustaw punkt przerwania na`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="deafa-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="deafa-144">Wybierz pozycję **Edytuj** dla filmu, wprowadź zmiany, ale nie wprowadzaj opcji **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="deafa-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="deafa-145">W innym oknie przeglądarki wybierz łącze **Usuń** dla tego samego filmu, a następnie usuń film.</span><span class="sxs-lookup"><span data-stu-id="deafa-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="deafa-146">W poprzednim oknie przeglądarki Opublikuj zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="deafa-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="deafa-147">Kod produkcyjny może chcieć wykryć konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="deafa-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="deafa-148">Aby uzyskać więcej informacji, zobacz sekcję [obsługa konfliktów współbieżności](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="deafa-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="deafa-149">Przegląd ogłaszania i powiązania</span><span class="sxs-lookup"><span data-stu-id="deafa-149">Posting and binding review</span></span>

<span data-ttu-id="deafa-150">Przejrzyj *stronę/filmy/plik Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="deafa-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="deafa-151">Gdy żądanie HTTP GET zostanie wysłane do strony filmy/Edycja (na przykład `http://localhost:5000/Movies/Edit/2` ):</span><span class="sxs-lookup"><span data-stu-id="deafa-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="deafa-152">`OnGetAsync`Metoda pobiera film z bazy danych i zwraca `Page` metodę.</span><span class="sxs-lookup"><span data-stu-id="deafa-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="deafa-153">`Page`Metoda renderuje stronę *stron/filmów/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="deafa-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="deafa-154">Plik *Pages/Movies/Edit. cshtml* zawiera dyrektywę model ( `@model RazorPagesMovie.Pages.Movies.EditModel` ), która sprawia, że model filmu jest dostępny na stronie.</span><span class="sxs-lookup"><span data-stu-id="deafa-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="deafa-155">Zostanie wyświetlony formularz edycji z wartościami z filmu.</span><span class="sxs-lookup"><span data-stu-id="deafa-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="deafa-156">Po opublikowaniu strony filmy/Edycja:</span><span class="sxs-lookup"><span data-stu-id="deafa-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="deafa-157">Wartości formularza na stronie są powiązane z `Movie` właściwością.</span><span class="sxs-lookup"><span data-stu-id="deafa-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="deafa-158">Ten `[BindProperty]` atrybut włącza [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="deafa-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="deafa-159">Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz jest ponownie wyświetlany z przesłanymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="deafa-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="deafa-160">Jeśli nie ma żadnych błędów modelu, film zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="deafa-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="deafa-161">Metody GET protokołu HTTP na stronach index, Create i DELETE są Razor zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="deafa-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="deafa-162">Metoda POST protokołu HTTP `OnPostAsync` na stronie Tworzenie jest Razor zgodna z podobnym wzorcem do `OnPostAsync` metody na Razor stronie edytowania.</span><span class="sxs-lookup"><span data-stu-id="deafa-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="deafa-163">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="deafa-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="deafa-164">[Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql) 
>  [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="deafa-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="deafa-165">Aplikacja ze szkieletem jest dobrze uruchomiona, ale prezentacja nie jest idealnym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="deafa-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="deafa-166">**ReleaseDate** powinna być **datą wydania** (dwa wyrazy).</span><span class="sxs-lookup"><span data-stu-id="deafa-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikacja filmowa otwarta w przeglądarce Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="deafa-168">Aktualizowanie wygenerowanego kodu</span><span class="sxs-lookup"><span data-stu-id="deafa-168">Update the generated code</span></span>

<span data-ttu-id="deafa-169">Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze wyświetlane w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="deafa-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="deafa-170">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych umożliwia Entity Framework Core poprawne mapowanie `Price` na walutę w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="deafa-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="deafa-171">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="deafa-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="deafa-172">[Adnotacje DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) zostały omówione w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="deafa-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="deafa-173">Atrybut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="deafa-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="deafa-174">Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="deafa-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="deafa-175">Przejdź do stron/filmów i umieść kursor na linku **edycji** , aby zobaczyć docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="deafa-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym adresem URL linku http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="deafa-177">Linki **Edytuj**, **szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Films/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="deafa-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="deafa-178">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="deafa-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="deafa-179">W poprzednim kodzie, `AnchorTagHelper` dynamicznie generuje `href` wartość atrybutu HTML ze Razor strony (trasa jest względna), `asp-page` i identyfikator trasy ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="deafa-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="deafa-180">Aby uzyskać więcej informacji, zobacz [generowanie adresów URL na stronach](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="deafa-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="deafa-181">Użyj **widoku źródła** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki.</span><span class="sxs-lookup"><span data-stu-id="deafa-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="deafa-182">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="deafa-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="deafa-183">Dynamicznie generowane linki przekażą identyfikator filmu z ciągiem zapytania (na przykład `?id=1` w `https://localhost:5001/Movies/Details?id=1` ).</span><span class="sxs-lookup"><span data-stu-id="deafa-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="deafa-184">Zaktualizuj strony edytowania, szczegółów i usuwania, Razor Aby użyć szablonu trasy "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="deafa-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="deafa-185">Zmień dyrektywę Page dla każdej z tych stron z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="deafa-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="deafa-186">Uruchom aplikację, a następnie Wyświetl źródło.</span><span class="sxs-lookup"><span data-stu-id="deafa-186">Run the app and then view source.</span></span> <span data-ttu-id="deafa-187">Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="deafa-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="deafa-188">Żądanie do strony z szablonem trasy "{ID: int}", który **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="deafa-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="deafa-189">Na przykład zwróci `http://localhost:5000/Movies/Details` błąd 404.</span><span class="sxs-lookup"><span data-stu-id="deafa-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="deafa-190">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="deafa-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="deafa-191">Aby przetestować zachowanie `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="deafa-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="deafa-192">Ustaw dyrektywę Page na stronie */filmy/details. cshtml* na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="deafa-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="deafa-193">Ustaw punkt przerwania `public async Task<IActionResult> OnGetAsync(int? id)` (w obszarze *strony/filmy/szczegóły. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="deafa-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="deafa-194">Przejdź do adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="deafa-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="deafa-195">W przypadku `@page "{id:int}"` dyrektywy punkt przerwania nigdy nie trafi.</span><span class="sxs-lookup"><span data-stu-id="deafa-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="deafa-196">Aparat routingu zwraca protokół HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="deafa-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="deafa-197">Przy użyciu `@page "{id:int?}"` `OnGetAsync` Metoda zwraca `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="deafa-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="deafa-198">Przejrzyj obsługę wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="deafa-198">Review concurrency exception handling</span></span>

<span data-ttu-id="deafa-199">Przejrzyj `OnPostAsync` metodę w pliku *Pages/Films/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="deafa-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="deafa-200">Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a pozostałe wpisy wprowadzane przez klienta do filmu.</span><span class="sxs-lookup"><span data-stu-id="deafa-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="deafa-201">Aby przetestować `catch` blok:</span><span class="sxs-lookup"><span data-stu-id="deafa-201">To test the `catch` block:</span></span>

* <span data-ttu-id="deafa-202">Ustaw punkt przerwania na`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="deafa-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="deafa-203">Wybierz pozycję **Edytuj** dla filmu, wprowadź zmiany, ale nie wprowadzaj opcji **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="deafa-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="deafa-204">W innym oknie przeglądarki wybierz łącze **Usuń** dla tego samego filmu, a następnie usuń film.</span><span class="sxs-lookup"><span data-stu-id="deafa-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="deafa-205">W poprzednim oknie przeglądarki Opublikuj zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="deafa-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="deafa-206">Kod produkcyjny może chcieć wykryć konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="deafa-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="deafa-207">Aby uzyskać więcej informacji, zobacz sekcję [obsługa konfliktów współbieżności](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="deafa-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="deafa-208">Przegląd ogłaszania i powiązania</span><span class="sxs-lookup"><span data-stu-id="deafa-208">Posting and binding review</span></span>

<span data-ttu-id="deafa-209">Przejrzyj *stronę/filmy/plik Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="deafa-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="deafa-210">Gdy żądanie HTTP GET zostanie wysłane do strony filmy/Edycja (na przykład `http://localhost:5000/Movies/Edit/2` ):</span><span class="sxs-lookup"><span data-stu-id="deafa-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="deafa-211">`OnGetAsync`Metoda pobiera film z bazy danych i zwraca `Page` metodę.</span><span class="sxs-lookup"><span data-stu-id="deafa-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="deafa-212">`Page`Metoda renderuje stronę *stron/filmów/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="deafa-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="deafa-213">Plik *Pages/Movies/Edit. cshtml* zawiera dyrektywę model ( `@model RazorPagesMovie.Pages.Movies.EditModel` ), która sprawia, że model filmu jest dostępny na stronie.</span><span class="sxs-lookup"><span data-stu-id="deafa-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="deafa-214">Zostanie wyświetlony formularz edycji z wartościami z filmu.</span><span class="sxs-lookup"><span data-stu-id="deafa-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="deafa-215">Po opublikowaniu strony filmy/Edycja:</span><span class="sxs-lookup"><span data-stu-id="deafa-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="deafa-216">Wartości formularza na stronie są powiązane z `Movie` właściwością.</span><span class="sxs-lookup"><span data-stu-id="deafa-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="deafa-217">Ten `[BindProperty]` atrybut włącza [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="deafa-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="deafa-218">Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz zostanie wyświetlony z przesłanymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="deafa-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="deafa-219">Jeśli nie ma żadnych błędów modelu, film zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="deafa-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="deafa-220">Metody GET protokołu HTTP na stronach index, Create i DELETE są Razor zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="deafa-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="deafa-221">Metoda POST protokołu HTTP `OnPostAsync` na stronie Tworzenie jest Razor zgodna z podobnym wzorcem do `OnPostAsync` metody na Razor stronie edytowania.</span><span class="sxs-lookup"><span data-stu-id="deafa-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="deafa-222">W następnym samouczku zostanie dodane Wyszukiwanie.</span><span class="sxs-lookup"><span data-stu-id="deafa-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="deafa-223">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="deafa-223">Additional resources</span></span>

* [<span data-ttu-id="deafa-224">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="deafa-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="deafa-225">[Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql) 
>  [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="deafa-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
