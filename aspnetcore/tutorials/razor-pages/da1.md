---
title: Aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zaktualizować wygenerowane strony w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 0f6535462fe2d308825bf7289c10d2b0690cebd4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666216"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="6cb5c-103">Aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6cb5c-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="6cb5c-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6cb5c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6cb5c-105">Aplikacja filmowa ma dobry początek, ale prezentacja nie jest idealna.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="6cb5c-106">**ReleaseDate** powinna być **data wydania** (dwa słowa).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikacja filmowa otwarta w Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="6cb5c-108">Aktualizowanie wygenerowanego kodu</span><span class="sxs-lookup"><span data-stu-id="6cb5c-108">Update the generated code</span></span>

<span data-ttu-id="6cb5c-109">Otwórz plik *Models/Movie.cs* i dodaj wyróżnione linie pokazane w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="6cb5c-110">Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych umożliwia Entity Framework `Price` Core poprawnie mapować do waluty w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="6cb5c-111">Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="6cb5c-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) jest omówione w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="6cb5c-113">[Atrybut Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="6cb5c-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="6cb5c-114">[Atrybut DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="6cb5c-115">Przejdź do stron/filmów i umieść wskaźnik myszy na linku **Edytuj,** aby wyświetlić docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z kursorem myszy na http://localhost:1234/Movies/Edit/5 łącze Edytuj i wyświetlonym adresem URL łącza](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="6cb5c-117">Łącza **Edytuj,** **Szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Movies/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6cb5c-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="6cb5c-118">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6cb5c-119">W poprzednim kodzie `AnchorTagHelper` dynamicznie generuje wartość `href` atrybutu HTML ze strony Razor (trasa `asp-page`jest względna),`asp-route-id`identyfikatora trasy i trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="6cb5c-120">Więcej informacji [można znaleźć w obszarze Generowanie adresów URL dla stron.](xref:razor-pages/index#url-generation-for-pages)</span><span class="sxs-lookup"><span data-stu-id="6cb5c-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="6cb5c-121">Użyj **view source** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="6cb5c-122">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="6cb5c-123">Łącza generowane dynamicznie przekazują identyfikator filmu z ciągiem zapytania `?id=1` `https://localhost:5001/Movies/Details?id=1`(na przykład w ).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="6cb5c-124">Dodaj szablon trasy</span><span class="sxs-lookup"><span data-stu-id="6cb5c-124">Add route template</span></span>

<span data-ttu-id="6cb5c-125">Zaktualizuj strony Edytuj, Szczegóły i Usuń razor, aby użyć szablonu trasy "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="6cb5c-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="6cb5c-126">Zmień dyrektywę strony dla każdej `@page` `@page "{id:int}"`z tych stron z .</span><span class="sxs-lookup"><span data-stu-id="6cb5c-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="6cb5c-127">Uruchom aplikację, a następnie wyświetl źródło.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-127">Run the app and then view source.</span></span> <span data-ttu-id="6cb5c-128">Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="6cb5c-129">Żądanie na stronie z szablonem trasy "{id:int}", które **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6cb5c-130">Na przykład `http://localhost:5000/Movies/Details` zwróci błąd 404.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="6cb5c-131">Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6cb5c-132">Aby przetestować `@page "{id:int?}"`zachowanie:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="6cb5c-133">Ustaw dyrektywę strony w *pages/movies/details.cshtml* na `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="6cb5c-134">Ustaw punkt przerwania (w `public async Task<IActionResult> OnGetAsync(int? id)` *pages/movies/details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="6cb5c-135">Przejdź do adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="6cb5c-136">Dzięki `@page "{id:int}"` dyrektywie punkt przerwania nigdy nie zostanie trafiony.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="6cb5c-137">Aparat routingu zwraca http 404.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="6cb5c-138">Za `@page "{id:int?}"`pomocą `OnGetAsync` , `NotFound` zwraca metodę (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="6cb5c-139">Przejrzyj obsługę wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="6cb5c-139">Review concurrency exception handling</span></span>

<span data-ttu-id="6cb5c-140">Przejrzyj `OnPostAsync` metodę w pliku *Pages/Movies/Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6cb5c-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6cb5c-141">Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a inny klient księguje zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="6cb5c-142">Aby przetestować `catch` blok:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-142">To test the `catch` block:</span></span>

* <span data-ttu-id="6cb5c-143">Ustawianie punktu przerwania`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="6cb5c-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="6cb5c-144">Wybierz **pozycję Edytuj** dla filmu, wprowadź zmiany, ale nie **wprowadzaj przycisku Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="6cb5c-145">W innym oknie przeglądarki zaznacz **łącze Usuń** dla tego samego filmu, a następnie usuń film.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="6cb5c-146">W poprzednim oknie przeglądarki opublikuj zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="6cb5c-147">Kod produkcji może chcieć wykryć konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="6cb5c-148">Zobacz [Obsługa konfliktów współbieżności,](xref:data/ef-rp/concurrency) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="6cb5c-149">Księgowanie i przegląd wiążący</span><span class="sxs-lookup"><span data-stu-id="6cb5c-149">Posting and binding review</span></span>

<span data-ttu-id="6cb5c-150">Sprawdź plik *Pages/Movies/Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6cb5c-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="6cb5c-151">Gdy na stronie Filmy/Edycja jest składane żądanie `http://localhost:5000/Movies/Edit/2`HTTP GET (na przykład):</span><span class="sxs-lookup"><span data-stu-id="6cb5c-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="6cb5c-152">Metoda `OnGetAsync` pobiera film z bazy danych i `Page` zwraca metodę.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="6cb5c-153">Metoda `Page` renderuje *stronę Pages/Movies/Edit.cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="6cb5c-154">Plik *Pages/Movies/Edit.cshtml* zawiera dyrektywę`@model RazorPagesMovie.Pages.Movies.EditModel`modelu ( ), która udostępnia model filmu na stronie.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="6cb5c-155">Formularz Edycja jest wyświetlany z wartościami z filmu.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="6cb5c-156">Po opublikowaniu strony Filmy/Edycja:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="6cb5c-157">Wartości formularza na stronie są `Movie` powiązane z właściwością.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="6cb5c-158">Atrybut `[BindProperty]` umożliwia [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="6cb5c-159">Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz jest ponownie wyeksponowany z przesłanych wartości.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="6cb5c-160">Jeśli nie ma błędów modelu, film zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="6cb5c-161">Metody HTTP GET na stronach Indeks, Tworzenie i Usuwanie Maszynki Do golenia mają podobny wzorzec.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="6cb5c-162">Metoda HTTP `OnPostAsync` POST na stronie Utwórz maszynkę `OnPostAsync` do golenia jest zgodna z podobnym wzorcem do metody na stronie Edytuj razor.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cb5c-163">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6cb5c-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6cb5c-164">[Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql)
> [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="6cb5c-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6cb5c-165">Aplikacja filmowa ma dobry początek, ale prezentacja nie jest idealna.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="6cb5c-166">**ReleaseDate** powinna być **data wydania** (dwa słowa).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikacja filmowa otwarta w Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="6cb5c-168">Aktualizowanie wygenerowanego kodu</span><span class="sxs-lookup"><span data-stu-id="6cb5c-168">Update the generated code</span></span>

<span data-ttu-id="6cb5c-169">Otwórz plik *Models/Movie.cs* i dodaj wyróżnione linie pokazane w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="6cb5c-170">Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych umożliwia Entity Framework `Price` Core poprawnie mapować do waluty w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="6cb5c-171">Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="6cb5c-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) jest omówione w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="6cb5c-173">[Atrybut Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="6cb5c-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="6cb5c-174">[Atrybut DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="6cb5c-175">Przejdź do stron/filmów i umieść wskaźnik myszy na linku **Edytuj,** aby wyświetlić docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z kursorem myszy na http://localhost:1234/Movies/Edit/5 łącze Edytuj i wyświetlonym adresem URL łącza](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="6cb5c-177">Łącza **Edytuj,** **Szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Movies/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6cb5c-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="6cb5c-178">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6cb5c-179">W poprzednim kodzie `AnchorTagHelper` dynamicznie generuje wartość `href` atrybutu HTML ze strony Razor (trasa `asp-page`jest względna),`asp-route-id`identyfikatora trasy i trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="6cb5c-180">Więcej informacji [można znaleźć w obszarze Generowanie adresów URL dla stron.](xref:razor-pages/index#url-generation-for-pages)</span><span class="sxs-lookup"><span data-stu-id="6cb5c-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="6cb5c-181">Użyj **view source** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="6cb5c-182">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="6cb5c-183">Łącza generowane dynamicznie przekazują identyfikator filmu z ciągiem zapytania `?id=1` `https://localhost:5001/Movies/Details?id=1`(na przykład w ).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="6cb5c-184">Zaktualizuj strony Edytuj, Szczegóły i Usuń razor, aby użyć szablonu trasy "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="6cb5c-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="6cb5c-185">Zmień dyrektywę strony dla każdej `@page` `@page "{id:int}"`z tych stron z .</span><span class="sxs-lookup"><span data-stu-id="6cb5c-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="6cb5c-186">Uruchom aplikację, a następnie wyświetl źródło.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-186">Run the app and then view source.</span></span> <span data-ttu-id="6cb5c-187">Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="6cb5c-188">Żądanie na stronie z szablonem trasy "{id:int}", które **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6cb5c-189">Na przykład `http://localhost:5000/Movies/Details` zwróci błąd 404.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="6cb5c-190">Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6cb5c-191">Aby przetestować `@page "{id:int?}"`zachowanie:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="6cb5c-192">Ustaw dyrektywę strony w *pages/movies/details.cshtml* na `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="6cb5c-193">Ustaw punkt przerwania (w `public async Task<IActionResult> OnGetAsync(int? id)` *pages/movies/details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="6cb5c-194">Przejdź do adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="6cb5c-195">Dzięki `@page "{id:int}"` dyrektywie punkt przerwania nigdy nie zostanie trafiony.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="6cb5c-196">Aparat routingu zwraca http 404.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="6cb5c-197">Za `@page "{id:int?}"`pomocą `OnGetAsync` , `NotFound` zwraca metodę (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="6cb5c-198">Przejrzyj obsługę wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="6cb5c-198">Review concurrency exception handling</span></span>

<span data-ttu-id="6cb5c-199">Przejrzyj `OnPostAsync` metodę w pliku *Pages/Movies/Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6cb5c-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6cb5c-200">Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a inny klient księguje zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="6cb5c-201">Aby przetestować `catch` blok:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-201">To test the `catch` block:</span></span>

* <span data-ttu-id="6cb5c-202">Ustawianie punktu przerwania`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="6cb5c-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="6cb5c-203">Wybierz **pozycję Edytuj** dla filmu, wprowadź zmiany, ale nie **wprowadzaj przycisku Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="6cb5c-204">W innym oknie przeglądarki zaznacz **łącze Usuń** dla tego samego filmu, a następnie usuń film.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="6cb5c-205">W poprzednim oknie przeglądarki opublikuj zmiany w filmie.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="6cb5c-206">Kod produkcji może chcieć wykryć konflikty współbieżności.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="6cb5c-207">Zobacz [Obsługa konfliktów współbieżności,](xref:data/ef-rp/concurrency) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="6cb5c-208">Księgowanie i przegląd wiążący</span><span class="sxs-lookup"><span data-stu-id="6cb5c-208">Posting and binding review</span></span>

<span data-ttu-id="6cb5c-209">Sprawdź plik *Pages/Movies/Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6cb5c-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="6cb5c-210">Gdy na stronie Filmy/Edycja jest składane żądanie `http://localhost:5000/Movies/Edit/2`HTTP GET (na przykład):</span><span class="sxs-lookup"><span data-stu-id="6cb5c-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="6cb5c-211">Metoda `OnGetAsync` pobiera film z bazy danych i `Page` zwraca metodę.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="6cb5c-212">Metoda `Page` renderuje *stronę Pages/Movies/Edit.cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="6cb5c-213">Plik *Pages/Movies/Edit.cshtml* zawiera dyrektywę`@model RazorPagesMovie.Pages.Movies.EditModel`modelu ( ), która udostępnia model filmu na stronie.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="6cb5c-214">Formularz Edycja jest wyświetlany z wartościami z filmu.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="6cb5c-215">Po opublikowaniu strony Filmy/Edycja:</span><span class="sxs-lookup"><span data-stu-id="6cb5c-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="6cb5c-216">Wartości formularza na stronie są `Movie` powiązane z właściwością.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="6cb5c-217">Atrybut `[BindProperty]` umożliwia [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="6cb5c-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="6cb5c-218">Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz jest wyświetlany z przesłane wartości.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="6cb5c-219">Jeśli nie ma błędów modelu, film zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="6cb5c-220">Metody HTTP GET na stronach Indeks, Tworzenie i Usuwanie Maszynki Do golenia mają podobny wzorzec.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="6cb5c-221">Metoda HTTP `OnPostAsync` POST na stronie Utwórz maszynkę `OnPostAsync` do golenia jest zgodna z podobnym wzorcem do metody na stronie Edytuj razor.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="6cb5c-222">Wyszukiwanie jest dodawane w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="6cb5c-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cb5c-223">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6cb5c-223">Additional resources</span></span>

* [<span data-ttu-id="6cb5c-224">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="6cb5c-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="6cb5c-225">[Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql)
> [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="6cb5c-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
