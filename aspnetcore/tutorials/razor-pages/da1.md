---
title: Aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zaktualizować wygenerowane strony w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 12/20/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/da1
ms.openlocfilehash: d9ab20b7ed4b394c154141efe3a94481efaf063c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774551"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>Aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Aplikacja ze szkieletem jest dobrze uruchomiona, ale prezentacja nie jest idealnym rozwiązaniem. **ReleaseDate** powinna być **datą wydania** (dwa wyrazy).

![Aplikacja filmowa otwarta w przeglądarce Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizowanie wygenerowanego kodu

Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze wyświetlane w następującym kodzie:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych umożliwia Entity Framework Core poprawne mapowanie `Price` na walutę w bazie danych. Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).

[Adnotacje DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) zostały omówione w następnym samouczku. Atrybut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate"). Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.

Przejdź do stron/filmów i umieść kursor na linku **edycji** , aby zobaczyć docelowy adres URL.

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym http://localhost:1234/Movies/Edit/5 adresem URL linku](~/tutorials/razor-pages/da1/edit7.png)

Linki **Edytuj**, **szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Films/index. cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML Razor w plikach. W poprzednim `AnchorTagHelper` kodzie, dynamicznie generuje wartość atrybutu HTML `href` ze Razor strony (trasa jest względna), `asp-page`i identyfikator trasy (`asp-route-id`). Aby uzyskać więcej informacji, zobacz [generowanie adresów URL na stronach](xref:razor-pages/index#url-generation-for-pages) .

Użyj **widoku źródła** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki. Poniżej przedstawiono część wygenerowanego kodu HTML:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicznie generowane linki przekażą identyfikator filmu z ciągiem zapytania (na przykład `?id=1` w `https://localhost:5001/Movies/Details?id=1`).

### <a name="add-route-template"></a>Dodawanie szablonu trasy

Zaktualizuj strony edytowania, szczegółów i usuwania Razor , aby użyć szablonu trasy "{ID: int}". Zmień dyrektywę Page dla każdej z tych stron z `@page` na. `@page "{id:int}"` Uruchom aplikację, a następnie Wyświetl źródło. Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Żądanie do strony z szablonem trasy "{ID: int}", który **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono). Na przykład `http://localhost:5000/Movies/Details` zwróci błąd 404. Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Aby przetestować zachowanie `@page "{id:int?}"`:

* Ustaw dyrektywę Page na stronie */filmy/details. cshtml* na `@page "{id:int?}"`.
* Ustaw punkt przerwania `public async Task<IActionResult> OnGetAsync(int? id)` (w obszarze *strony/filmy/szczegóły. cshtml. cs*).
* Przejdź do adresu `https://localhost:5001/Movies/Details/`.

W przypadku `@page "{id:int}"` dyrektywy punkt przerwania nigdy nie trafi. Aparat routingu zwraca protokół HTTP 404. Przy `@page "{id:int?}"`użyciu `OnGetAsync` metoda zwraca `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Przejrzyj obsługę wyjątków współbieżności

Przejrzyj `OnPostAsync` metodę w pliku *Pages/Films/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a pozostałe wpisy wprowadzane przez klienta do filmu.

Aby przetestować `catch` blok:

* Ustaw punkt przerwania na`catch (DbUpdateConcurrencyException)`
* Wybierz pozycję **Edytuj** dla filmu, wprowadź zmiany, ale nie wprowadzaj opcji **Zapisz**.
* W innym oknie przeglądarki wybierz łącze **Usuń** dla tego samego filmu, a następnie usuń film.
* W poprzednim oknie przeglądarki Opublikuj zmiany w filmie.

Kod produkcyjny może chcieć wykryć konflikty współbieżności. Aby uzyskać więcej informacji, zobacz sekcję [obsługa konfliktów współbieżności](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Przegląd ogłaszania i powiązania

Przejrzyj *stronę/filmy/plik Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Gdy żądanie HTTP GET zostanie wysłane do strony filmy/Edycja (na przykład `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` Metoda pobiera film z bazy danych i zwraca `Page` metodę.
* `Page` Metoda renderuje stronę *stron/filmów/Edit. cshtml* Razor . Plik *Pages/Movies/Edit. cshtml* zawiera dyrektywę model (`@model RazorPagesMovie.Pages.Movies.EditModel`), która sprawia, że model filmu jest dostępny na stronie.
* Zostanie wyświetlony formularz edycji z wartościami z filmu.

Po opublikowaniu strony filmy/Edycja:

* Wartości formularza na stronie są powiązane z `Movie` właściwością. Ten `[BindProperty]` atrybut włącza [powiązanie modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz jest ponownie wyświetlany z przesłanymi wartościami.
* Jeśli nie ma żadnych błędów modelu, film zostanie zapisany.

Metody GET protokołu HTTP na stronach index, Create i DELETE Razor są zgodne z podobnym wzorcem. Metoda POST `OnPostAsync` protokołu HTTP na stronie Tworzenie Razor jest zgodna z podobnym wzorcem `OnPostAsync` do metody na stronie Razor edytowania.

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql)
> [Next: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikacja ze szkieletem jest dobrze uruchomiona, ale prezentacja nie jest idealnym rozwiązaniem. **ReleaseDate** powinna być **datą wydania** (dwa wyrazy).

![Aplikacja filmowa otwarta w przeglądarce Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizowanie wygenerowanego kodu

Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze wyświetlane w następującym kodzie:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych umożliwia Entity Framework Core poprawne mapowanie `Price` na walutę w bazie danych. Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).

[Adnotacje DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) zostały omówione w następnym samouczku. Atrybut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate"). Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.

Przejdź do stron/filmów i umieść kursor na linku **edycji** , aby zobaczyć docelowy adres URL.

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym http://localhost:1234/Movies/Edit/5 adresem URL linku](~/tutorials/razor-pages/da1/edit7.png)

Linki **Edytuj**, **szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Films/index. cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML Razor w plikach. W poprzednim `AnchorTagHelper` kodzie, dynamicznie generuje wartość atrybutu HTML `href` ze Razor strony (trasa jest względna), `asp-page`i identyfikator trasy (`asp-route-id`). Aby uzyskać więcej informacji, zobacz [generowanie adresów URL na stronach](xref:razor-pages/index#url-generation-for-pages) .

Użyj **widoku źródła** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki. Poniżej przedstawiono część wygenerowanego kodu HTML:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicznie generowane linki przekażą identyfikator filmu z ciągiem zapytania (na przykład `?id=1` w `https://localhost:5001/Movies/Details?id=1`).

Zaktualizuj strony edytowania, szczegółów i usuwania Razor , aby użyć szablonu trasy "{ID: int}". Zmień dyrektywę Page dla każdej z tych stron z `@page` na. `@page "{id:int}"` Uruchom aplikację, a następnie Wyświetl źródło. Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Żądanie do strony z szablonem trasy "{ID: int}", który **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono). Na przykład `http://localhost:5000/Movies/Details` zwróci błąd 404. Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Aby przetestować zachowanie `@page "{id:int?}"`:

* Ustaw dyrektywę Page na stronie */filmy/details. cshtml* na `@page "{id:int?}"`.
* Ustaw punkt przerwania `public async Task<IActionResult> OnGetAsync(int? id)` (w obszarze *strony/filmy/szczegóły. cshtml. cs*).
* Przejdź do adresu `https://localhost:5001/Movies/Details/`.

W przypadku `@page "{id:int}"` dyrektywy punkt przerwania nigdy nie trafi. Aparat routingu zwraca protokół HTTP 404. Przy `@page "{id:int?}"`użyciu `OnGetAsync` metoda zwraca `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Przejrzyj obsługę wyjątków współbieżności

Przejrzyj `OnPostAsync` metodę w pliku *Pages/Films/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a pozostałe wpisy wprowadzane przez klienta do filmu.

Aby przetestować `catch` blok:

* Ustaw punkt przerwania na`catch (DbUpdateConcurrencyException)`
* Wybierz pozycję **Edytuj** dla filmu, wprowadź zmiany, ale nie wprowadzaj opcji **Zapisz**.
* W innym oknie przeglądarki wybierz łącze **Usuń** dla tego samego filmu, a następnie usuń film.
* W poprzednim oknie przeglądarki Opublikuj zmiany w filmie.

Kod produkcyjny może chcieć wykryć konflikty współbieżności. Aby uzyskać więcej informacji, zobacz sekcję [obsługa konfliktów współbieżności](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Przegląd ogłaszania i powiązania

Przejrzyj *stronę/filmy/plik Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Gdy żądanie HTTP GET zostanie wysłane do strony filmy/Edycja (na przykład `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` Metoda pobiera film z bazy danych i zwraca `Page` metodę. 
* `Page` Metoda renderuje stronę *stron/filmów/Edit. cshtml* Razor . Plik *Pages/Movies/Edit. cshtml* zawiera dyrektywę model (`@model RazorPagesMovie.Pages.Movies.EditModel`), która sprawia, że model filmu jest dostępny na stronie.
* Zostanie wyświetlony formularz edycji z wartościami z filmu.

Po opublikowaniu strony filmy/Edycja:

* Wartości formularza na stronie są powiązane z `Movie` właściwością. Ten `[BindProperty]` atrybut włącza [powiązanie modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz zostanie wyświetlony z przesłanymi wartościami.
* Jeśli nie ma żadnych błędów modelu, film zostanie zapisany.

Metody GET protokołu HTTP na stronach index, Create i DELETE Razor są zgodne z podobnym wzorcem. Metoda POST `OnPostAsync` protokołu HTTP na stronie Tworzenie Razor jest zgodna z podobnym wzorcem `OnPostAsync` do metody na stronie Razor edytowania.

W następnym samouczku zostanie dodane Wyszukiwanie.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka usługi YouTube](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql)
> [Next: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)

::: moniker-end
