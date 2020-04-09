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
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>Aktualizowanie wygenerowanych stron w aplikacji ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Aplikacja filmowa ma dobry początek, ale prezentacja nie jest idealna. **ReleaseDate** powinna być **data wydania** (dwa słowa).

![Aplikacja filmowa otwarta w Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizowanie wygenerowanego kodu

Otwórz plik *Models/Movie.cs* i dodaj wyróżnione linie pokazane w następującym kodzie:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych umożliwia Entity Framework `Price` Core poprawnie mapować do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) jest omówione w następnym samouczku. [Atrybut Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate"). [Atrybut DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.

Przejdź do stron/filmów i umieść wskaźnik myszy na linku **Edytuj,** aby wyświetlić docelowy adres URL.

![Okno przeglądarki z kursorem myszy na http://localhost:1234/Movies/Edit/5 łącze Edytuj i wyświetlonym adresem URL łącza](~/tutorials/razor-pages/da1/edit7.png)

Łącza **Edytuj,** **Szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Movies/Index.cshtml.*

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor. W poprzednim kodzie `AnchorTagHelper` dynamicznie generuje wartość `href` atrybutu HTML ze strony Razor (trasa `asp-page`jest względna),`asp-route-id`identyfikatora trasy i trasy ( ). Więcej informacji [można znaleźć w obszarze Generowanie adresów URL dla stron.](xref:razor-pages/index#url-generation-for-pages)

Użyj **view source** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki. Poniżej przedstawiono część wygenerowanego kodu HTML:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Łącza generowane dynamicznie przekazują identyfikator filmu z ciągiem zapytania `?id=1` `https://localhost:5001/Movies/Details?id=1`(na przykład w ).

### <a name="add-route-template"></a>Dodaj szablon trasy

Zaktualizuj strony Edytuj, Szczegóły i Usuń razor, aby użyć szablonu trasy "{id:int}". Zmień dyrektywę strony dla każdej `@page` `@page "{id:int}"`z tych stron z . Uruchom aplikację, a następnie wyświetl źródło. Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Żądanie na stronie z szablonem trasy "{id:int}", które **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono). Na przykład `http://localhost:5000/Movies/Details` zwróci błąd 404. Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Aby przetestować `@page "{id:int?}"`zachowanie:

* Ustaw dyrektywę strony w *pages/movies/details.cshtml* na `@page "{id:int?}"`.
* Ustaw punkt przerwania (w `public async Task<IActionResult> OnGetAsync(int? id)` *pages/movies/details.cshtml.cs*).
* Przejdź do adresu `https://localhost:5001/Movies/Details/`.

Dzięki `@page "{id:int}"` dyrektywie punkt przerwania nigdy nie zostanie trafiony. Aparat routingu zwraca http 404. Za `@page "{id:int?}"`pomocą `OnGetAsync` , `NotFound` zwraca metodę (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Przejrzyj obsługę wyjątków współbieżności

Przejrzyj `OnPostAsync` metodę w pliku *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a inny klient księguje zmiany w filmie.

Aby przetestować `catch` blok:

* Ustawianie punktu przerwania`catch (DbUpdateConcurrencyException)`
* Wybierz **pozycję Edytuj** dla filmu, wprowadź zmiany, ale nie **wprowadzaj przycisku Zapisz**.
* W innym oknie przeglądarki zaznacz **łącze Usuń** dla tego samego filmu, a następnie usuń film.
* W poprzednim oknie przeglądarki opublikuj zmiany w filmie.

Kod produkcji może chcieć wykryć konflikty współbieżności. Zobacz [Obsługa konfliktów współbieżności,](xref:data/ef-rp/concurrency) aby uzyskać więcej informacji.

### <a name="posting-and-binding-review"></a>Księgowanie i przegląd wiążący

Sprawdź plik *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Gdy na stronie Filmy/Edycja jest składane żądanie `http://localhost:5000/Movies/Edit/2`HTTP GET (na przykład):

* Metoda `OnGetAsync` pobiera film z bazy danych i `Page` zwraca metodę.
* Metoda `Page` renderuje *stronę Pages/Movies/Edit.cshtml* Razor. Plik *Pages/Movies/Edit.cshtml* zawiera dyrektywę`@model RazorPagesMovie.Pages.Movies.EditModel`modelu ( ), która udostępnia model filmu na stronie.
* Formularz Edycja jest wyświetlany z wartościami z filmu.

Po opublikowaniu strony Filmy/Edycja:

* Wartości formularza na stronie są `Movie` powiązane z właściwością. Atrybut `[BindProperty]` umożliwia [powiązanie modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz jest ponownie wyeksponowany z przesłanych wartości.
* Jeśli nie ma błędów modelu, film zostanie zapisany.

Metody HTTP GET na stronach Indeks, Tworzenie i Usuwanie Maszynki Do golenia mają podobny wzorzec. Metoda HTTP `OnPostAsync` POST na stronie Utwórz maszynkę `OnPostAsync` do golenia jest zgodna z podobnym wzorcem do metody na stronie Edytuj razor.

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql)
> [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikacja filmowa ma dobry początek, ale prezentacja nie jest idealna. **ReleaseDate** powinna być **data wydania** (dwa słowa).

![Aplikacja filmowa otwarta w Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizowanie wygenerowanego kodu

Otwórz plik *Models/Movie.cs* i dodaj wyróżnione linie pokazane w następującym kodzie:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych umożliwia Entity Framework `Price` Core poprawnie mapować do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) jest omówione w następnym samouczku. [Atrybut Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate"). [Atrybut DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.

Przejdź do stron/filmów i umieść wskaźnik myszy na linku **Edytuj,** aby wyświetlić docelowy adres URL.

![Okno przeglądarki z kursorem myszy na http://localhost:1234/Movies/Edit/5 łącze Edytuj i wyświetlonym adresem URL łącza](~/tutorials/razor-pages/da1/edit7.png)

Łącza **Edytuj,** **Szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Movies/Index.cshtml.*

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor. W poprzednim kodzie `AnchorTagHelper` dynamicznie generuje wartość `href` atrybutu HTML ze strony Razor (trasa `asp-page`jest względna),`asp-route-id`identyfikatora trasy i trasy ( ). Więcej informacji [można znaleźć w obszarze Generowanie adresów URL dla stron.](xref:razor-pages/index#url-generation-for-pages)

Użyj **view source** z ulubionej przeglądarki, aby sprawdzić wygenerowane znaczniki. Poniżej przedstawiono część wygenerowanego kodu HTML:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Łącza generowane dynamicznie przekazują identyfikator filmu z ciągiem zapytania `?id=1` `https://localhost:5001/Movies/Details?id=1`(na przykład w ).

Zaktualizuj strony Edytuj, Szczegóły i Usuń razor, aby użyć szablonu trasy "{id:int}". Zmień dyrektywę strony dla każdej `@page` `@page "{id:int}"`z tych stron z . Uruchom aplikację, a następnie wyświetl źródło. Wygenerowany kod HTML dodaje identyfikator do części ścieżki adresu URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Żądanie na stronie z szablonem trasy "{id:int}", które **nie** zawiera liczby całkowitej, zwróci błąd HTTP 404 (nie znaleziono). Na przykład `http://localhost:5000/Movies/Details` zwróci błąd 404. Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Aby przetestować `@page "{id:int?}"`zachowanie:

* Ustaw dyrektywę strony w *pages/movies/details.cshtml* na `@page "{id:int?}"`.
* Ustaw punkt przerwania (w `public async Task<IActionResult> OnGetAsync(int? id)` *pages/movies/details.cshtml.cs*).
* Przejdź do adresu `https://localhost:5001/Movies/Details/`.

Dzięki `@page "{id:int}"` dyrektywie punkt przerwania nigdy nie zostanie trafiony. Aparat routingu zwraca http 404. Za `@page "{id:int?}"`pomocą `OnGetAsync` , `NotFound` zwraca metodę (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Przejrzyj obsługę wyjątków współbieżności

Przejrzyj `OnPostAsync` metodę w pliku *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Poprzedni kod wykrywa wyjątki współbieżności, gdy jeden klient usuwa film, a inny klient księguje zmiany w filmie.

Aby przetestować `catch` blok:

* Ustawianie punktu przerwania`catch (DbUpdateConcurrencyException)`
* Wybierz **pozycję Edytuj** dla filmu, wprowadź zmiany, ale nie **wprowadzaj przycisku Zapisz**.
* W innym oknie przeglądarki zaznacz **łącze Usuń** dla tego samego filmu, a następnie usuń film.
* W poprzednim oknie przeglądarki opublikuj zmiany w filmie.

Kod produkcji może chcieć wykryć konflikty współbieżności. Zobacz [Obsługa konfliktów współbieżności,](xref:data/ef-rp/concurrency) aby uzyskać więcej informacji.

### <a name="posting-and-binding-review"></a>Księgowanie i przegląd wiążący

Sprawdź plik *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Gdy na stronie Filmy/Edycja jest składane żądanie `http://localhost:5000/Movies/Edit/2`HTTP GET (na przykład):

* Metoda `OnGetAsync` pobiera film z bazy danych i `Page` zwraca metodę. 
* Metoda `Page` renderuje *stronę Pages/Movies/Edit.cshtml* Razor. Plik *Pages/Movies/Edit.cshtml* zawiera dyrektywę`@model RazorPagesMovie.Pages.Movies.EditModel`modelu ( ), która udostępnia model filmu na stronie.
* Formularz Edycja jest wyświetlany z wartościami z filmu.

Po opublikowaniu strony Filmy/Edycja:

* Wartości formularza na stronie są `Movie` powiązane z właściwością. Atrybut `[BindProperty]` umożliwia [powiązanie modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Jeśli występują błędy w stanie modelu (na przykład `ReleaseDate` nie można przekonwertować na datę), formularz jest wyświetlany z przesłane wartości.
* Jeśli nie ma błędów modelu, film zostanie zapisany.

Metody HTTP GET na stronach Indeks, Tworzenie i Usuwanie Maszynki Do golenia mają podobny wzorzec. Metoda HTTP `OnPostAsync` POST na stronie Utwórz maszynkę `OnPostAsync` do golenia jest zgodna z podobnym wzorcem do metody na stronie Edytuj razor.

Wyszukiwanie jest dodawane w następnym samouczku.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Poprzedni: Praca z bazą danych](xref:tutorials/razor-pages/sql)
> [Dalej: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)

::: moniker-end
