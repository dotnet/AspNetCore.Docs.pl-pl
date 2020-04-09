---
title: Rusztowania stron maszynki do golenia w rdzeniu ASP.NET
author: rick-anderson
description: W tym artykule wyjaśniono strony Razor generowane przez rusztowania.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: cec4295a2c08c89db0975808583f41c7d09bfc88
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662450"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Rusztowania stron maszynki do golenia w rdzeniu ASP.NET

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Ten samouczek analizuje strony Razor utworzone przez rusztowania w [poprzednim samouczku](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Strony Tworzenie, usuwanie, szczegóły i edytowanie

Sprawdź model strony *Pages/Movies/Index.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Strony brzytwy `PageModel`pochodzą od . Zgodnie z `PageModel`konwencją klasa pochodna jest wywoływana `<PageName>Model`. Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) aby dodać do `RazorPagesMovieContext` strony. Wszystkie strony rusztowania postępuj zgodnie z tym wzorem. Zobacz [kod asynchroniczne, aby](xref:data/ef-rp/intro#asynchronous-code) uzyskać więcej informacji na temat programowania asynchroniowego z Entity Framework.

Po nałożeniu żądania strony `OnGetAsync` metoda zwraca listę filmów do strony Razor. `OnGetAsync`lub `OnGet` jest wywoływana w celu zainicjowania stanu strony. W takim `OnGetAsync` przypadku pobiera listę filmów i wyświetla je.

Gdy `OnGet` `void` zwraca `OnGetAsync` `Task`lub zwraca, nie jest używana instrukcja zwrotu. Gdy typ zwracany jest `IActionResult` lub `Task<IActionResult>`, należy podać instrukcję zwrotu. Na przykład metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Sprawdź *stronę Razor Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor może przejść z HTML do C# lub do znaczników specyficznych dla Razora. Gdy `@` po symbolu następuje [słowo kluczowe razor reserved](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do znaczników specyficznych dla razora, w przeciwnym razie przechodzi do języka C#.

### <a name="the-page-directive"></a>Dyrektywa @page

Dyrektywa `@page` Razor sprawia, że plik akcji MVC, co oznacza, że może obsługiwać żądania. `@page`musi być pierwszą dyrektywą Razor na stronie. `@page`jest przykładem przejścia do znaczników specyficznych dla razora. Aby uzyskać więcej informacji, zobacz [składnia maszynki do golenia.](xref:mvc/views/razor#razor-syntax)

Sprawdź wyrażenie lambda używane w następującym pomocniku HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Pomocnik `DisplayNameFor` HTML sprawdza właściwość, `Title` do którego odwołuje się wyrażenie lambda, aby określić nazwę wyświetlaną. Wyrażenie lambda jest kontrolowane, a nie oceniane. Oznacza to, że nie `model` `model.Movie`ma `model.Movie[0]` naruszenia `null` dostępu, gdy , lub jest lub puste. Gdy wyrażenie lambda jest oceniane (na przykład z), `@Html.DisplayFor(modelItem => item.Title)`wartości właściwości modelu są oceniane.

<a name="md"></a>

### <a name="the-model-directive"></a>Dyrektywa @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Dyrektywa `@model` określa typ modelu przekazywane do Razor Page. W poprzednim przykładzie `@model` wiersz udostępnia `PageModel`klasę pochodną do strony Razor. Model jest używany `@Html.DisplayNameFor` w `@Html.DisplayFor` pomocnikach [HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stronie.

### <a name="the-layout-page"></a>Strona układu

Wybierz linki menu **(RazorPagesMovie**, **Home**i **Prywatność).** Każda strona zawiera ten sam układ menu. Układ menu jest zaimplementowany w pliku *Pages/Shared/_Layout.cshtml.* Otwórz plik *Pages/Shared/_Layout.cshtml.*

[Szablony układu](xref:mvc/views/layout) umożliwiają układ kontenera HTML:

* Określone w jednym miejscu.
* Stosowane na wielu stronach w witrynie.

Znajdź `@RenderBody()` linię. `RenderBody`jest symbolem zastępczym, w którym wyświetlane są wszystkie widoki specyficzne dla strony, *zawinięte* w stronę układu. Na przykład wybierz łącze **Prywatność,** a widok *Pages/Privacy.cshtml* jest renderowany wewnątrz `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData i układ

Należy wziąć pod uwagę następujące znaczniki z pliku *Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Poprzedni wyróżniony znaczników jest przykładem Razor przejście do języka C#. Znaki `{` `}` i ująć blok kodu C#.

Klasa `PageModel` podstawowa `ViewData` zawiera właściwość słownika, która może służyć do przekazywania danych do View. Obiekty są dodawane do słownika `ViewData` przy użyciu wzorca klucza/wartości. W poprzednim przykładzie `"Title"` właściwość jest `ViewData` dodawany do słownika.

Właściwość `"Title"` jest używana w pliku *Pages/Shared/_Layout.cshtml.* Poniższy znacznik pokazuje kilka pierwszych wierszy pliku *_Layout.cshtml.*

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Linia `@*Markup removed for brevity.*@` jest komentarz Razor. W przeciwieństwie`<!-- -->`do komentarzy HTML ( ), komentarze Razor nie są wysyłane do klienta.

### <a name="update-the-layout"></a>Aktualizowanie układu

Zmień `<title>` element w pliku *Pages/Shared/_Layout.cshtml,* aby wyświetlić **film,** a nie **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Znajdź następujący element zakotwiczenia w pliku *Pages/Shared/_Layout.cshtml.*

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Zastąp poprzedni element następującym znacznikiem:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Poprzednim elementem zakotwiczenia jest [Pomocnik znaczników](xref:mvc/views/tag-helpers/intro). W tym przypadku jest to [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Atrybut `asp-page="/Movies/Index"` i wartość Pomocnika znaczników tworzy `/Movies/Index` łącze do strony Razor. Wartość `asp-area` atrybutu jest pusta, więc obszar nie jest używany w łączu. Aby uzyskać więcej informacji, zobacz [Obszary.](xref:mvc/controllers/areas)

Zapisz zmiany i przetestuj aplikację, klikając link **RpMovie.** Zobacz [plik _Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) w GitHub, jeśli masz jakiekolwiek problemy.

Testowanie innych łączy (**Strona główna**, **RpMovie**, **Tworzenie**, **Edycja**i **Usuń**). Każda strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Podczas doszkiowania strony tytuł jest używany dla zakładki.

> [!NOTE]
> Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych. Aby obsługiwać [sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i formatów daty spoza języka AMERYKAŃSKIEgo, należy podjąć kroki w celu zglobalizowania aplikacji. Zobacz ten [problem GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) instrukcje dotyczące dodawania przecinka dziesiętnego.

Właściwość `Layout` jest ustawiona w pliku *Pages/_ViewStart.cshtml:*

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Poprzedni znacznik ustawia plik układu na *Pages/Shared/_Layout.cshtml* dla wszystkich plików Razor w folderze *Pages.* Aby uzyskać więcej informacji, zobacz [Układ.](xref:razor-pages/index#layout)

### <a name="the-create-page-model"></a>Model strony Tworzenie

Sprawdź model strony *Pages/Movies/Create.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Metoda `OnGet` inicjuje dowolny stan potrzebny dla strony. Strona Utwórz nie ma żadnego stanu `Page` do zainicjowania, więc jest zwracany. W dalszej części samouczka pokazano przykład inicjowania `OnGet` stanu. Metoda `Page` tworzy `PageResult` obiekt, który renderuje stronę *Create.cshtml.*

Właściwość `Movie` używa `[BindProperty]` atrybutu, aby wyrazić zgodę na [powiązanie modelu.](xref:mvc/models/model-binding) Gdy formularz Utwórz księguje wartości formularza, środowisko uruchomieniowe ASP.NET Core `Movie` wiąże zaksięgowane wartości z modelem.

Metoda `OnPostAsync` jest uruchamiana, gdy dane formularza wpisów strony:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Jeśli występują błędy modelu, formularz jest ponownie wyeksponowany wraz z wszelkimi opublikowanymi danymi formularza. Większość błędów modelu można złapać po stronie klienta przed zaksięgowania formularza. Przykładem błędu modelu jest księgowanie wartości pola daty, które nie może zostać przekonwertowane na datę. Sprawdzanie poprawności po stronie klienta i sprawdzanie poprawności modelu są omówione w dalszej części samouczka.

Jeśli nie ma żadnych błędów modelu, dane są zapisywane, a przeglądarka jest przekierowywane do strony Indeks.

### <a name="the-create-razor-page"></a>Strona Tworzenie maszynki do golenia

Sprawdź plik *strony/filmy/utwórz.cshtml* Razor:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Program Visual Studio wyświetla następujące znaczniki w charakterystycznej pogrubionej czcionce używanej dla pomocników tagów:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Widok vs17 strony Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

W poprzednim znaczniku pokazano następujące pomocników znaczników:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Program Visual Studio wyświetla następujące znaczniki w charakterystycznej pogrubionej czcionce używanej dla pomocników tagów:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

Element `<form method="post">` jest [pomocnikiem znaczników formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocnik znacznika formularza automatycznie zawiera [token antyforgery.](xref:security/anti-request-forgery)

Aparat rusztowań tworzy znaczniki Razor dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocnicy znaczników](xref:mvc/views/working-with-forms#the-validation-tag-helpers) `<div asp-validation-summary` sprawdzania `<span asp-validation-for`poprawności (i) wyświetlają błędy sprawdzania poprawności. Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.

[Pomocnik tagu](xref:mvc/views/working-with-forms#the-label-tag-helper) etykiety`<label asp-for="Movie.Title" class="control-label"></label>`( ) generuje `for` podpis etykiety `Title` i atrybut właściwości.

[Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta.

Aby uzyskać więcej informacji na `<form method="post">`temat pomocników tagów, takich jak , zobacz [Pomocników tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model)
> [Dalej: Baza danych](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Ten samouczek analizuje strony Razor utworzone przez rusztowania w [poprzednim samouczku](xref:tutorials/razor-pages/model).

[Wyświetl lub pobierz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) próbkę.

## <a name="the-create-delete-details-and-edit-pages"></a>Strony Tworzenie, usuwanie, szczegóły i edytowanie

Sprawdź model strony *Pages/Movies/Index.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Strony brzytwy `PageModel`pochodzą od . Zgodnie z `PageModel`konwencją klasa pochodna jest wywoływana `<PageName>Model`. Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) aby dodać do `RazorPagesMovieContext` strony. Wszystkie strony rusztowania postępuj zgodnie z tym wzorem. Zobacz [kod asynchroniczne, aby](xref:data/ef-rp/intro#asynchronous-code) uzyskać więcej informacji na temat programowania asynchroniowego z Entity Framework.

Po nałożeniu żądania strony `OnGetAsync` metoda zwraca listę filmów do strony Razor. `OnGetAsync`lub `OnGet` jest wywoływana na stronie Razor, aby zainicjować stan strony. W takim `OnGetAsync` przypadku pobiera listę filmów i wyświetla je.

W `OnGet` `void` przypadku `OnGetAsync` `Task`zwracania lub zwracania nie jest używana metoda zwrotu. Gdy typ zwracany jest `IActionResult` lub `Task<IActionResult>`, należy podać instrukcję zwrotu. Na przykład metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Sprawdź *stronę Razor Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor może przejść z HTML do C# lub do znaczników specyficznych dla Razora. Gdy `@` po symbolu następuje [słowo kluczowe razor reserved](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do znaczników specyficznych dla razora, w przeciwnym razie przechodzi do języka C#.

Dyrektywa `@page` Razor sprawia, że plik do akcji MVC, co oznacza, że może obsługiwać żądania. `@page`musi być pierwszą dyrektywą Razor na stronie. `@page`jest przykładem przejścia do znaczników specyficznych dla razora. Aby uzyskać więcej informacji, zobacz [składnia maszynki do golenia.](xref:mvc/views/razor#razor-syntax)

Sprawdź wyrażenie lambda używane w następującym pomocniku HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Pomocnik `DisplayNameFor` HTML sprawdza właściwość, `Title` do którego odwołuje się wyrażenie lambda, aby określić nazwę wyświetlaną. Wyrażenie lambda jest kontrolowane, a nie oceniane. Oznacza to, że nie `model` `model.Movie`ma `model.Movie[0]` naruszenia `null` dostępu, gdy , lub są lub puste. Gdy wyrażenie lambda jest oceniane (na przykład z), `@Html.DisplayFor(modelItem => item.Title)`wartości właściwości modelu są oceniane.

<a name="md"></a>

### <a name="the-model-directive"></a>Dyrektywa @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Dyrektywa `@model` określa typ modelu przekazywane do Razor Page. W poprzednim przykładzie `@model` wiersz udostępnia `PageModel`klasę pochodną do strony Razor. Model jest używany `@Html.DisplayNameFor` w `@Html.DisplayFor` pomocnikach [HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stronie.

### <a name="the-layout-page"></a>Strona układu

Wybierz linki menu **(RazorPagesMovie**, **Home**i **Prywatność).** Każda strona zawiera ten sam układ menu. Układ menu jest zaimplementowany w pliku *Pages/Shared/_Layout.cshtml.* Otwórz plik *Pages/Shared/_Layout.cshtml.*

[Szablony układu](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie. Znajdź `@RenderBody()` linię. `RenderBody`to symbol zastępczy, w którym wszystkie utworzone widoki specyficzne dla strony są wyświetlane, *zawinięte* na stronie układu. Jeśli na przykład wybierzesz łącze **Prywatność,** widok **Pages/Privacy.cshtml** zostanie renderowany wewnątrz `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData i układ

Rozważmy następujący kod z pliku *Pages/Movies/Index.cshtml:*

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Powyższy wyróżniony kod jest przykładem razor przejście do języka C#. Znaki `{` `}` i ująć blok kodu C#.

Klasa `PageModel` podstawowa `ViewData` ma właściwość słownika, która może służyć do dodawania danych, które mają być przekazywać do widoku. Obiekty można dodać `ViewData` do słownika przy użyciu wzorca klucza/wartości. W poprzednim przykładzie właściwość "Title" jest `ViewData` dodawany do słownika.

Właściwość "Tytuł" jest używana w pliku *Pages/Shared/_Layout.cshtml.* Poniższy znacznik pokazuje kilka pierwszych wierszy pliku *_Layout.cshtml.*

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Linia `@*Markup removed for brevity.*@` jest komentarzem Razor, który nie pojawia się w pliku układu. W przeciwieństwie`<!-- -->`do komentarzy HTML ( ), komentarze Razor nie są wysyłane do klienta.

### <a name="update-the-layout"></a>Aktualizowanie układu

Zmień `<title>` element w pliku *Pages/Shared/_Layout.cshtml,* aby wyświetlić **film,** a nie **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Znajdź następujący element zakotwiczenia w pliku *Pages/Shared/_Layout.cshtml.*

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Zastąp poprzedni element następującym znacznikiem.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Poprzednim elementem zakotwiczenia jest [Pomocnik znaczników](xref:mvc/views/tag-helpers/intro). W tym przypadku jest to [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Atrybut `asp-page="/Movies/Index"` i wartość Pomocnika znaczników tworzy `/Movies/Index` łącze do strony Razor. Wartość `asp-area` atrybutu jest pusta, więc obszar nie jest używany w łączu. Aby uzyskać więcej informacji, zobacz [Obszary.](xref:mvc/controllers/areas)

Zapisz zmiany i przetestuj aplikację, klikając link **RpMovie.** Zobacz [plik _Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) w GitHub, jeśli masz jakiekolwiek problemy.

Testowanie innych łączy (**Strona główna**, **RpMovie**, **Tworzenie**, **Edycja**i **Usuń**). Każda strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Podczas doszkiowania strony tytuł jest używany dla zakładki.

> [!NOTE]
> Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych. Aby obsługiwać [sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i formatów daty spoza języka AMERYKAŃSKIEgo, należy podjąć kroki w celu zglobalizowania aplikacji. Ten [problem GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) instrukcje dotyczące dodawania przecinka dziesiętnego.

Właściwość `Layout` jest ustawiona w pliku *Pages/_ViewStart.cshtml:*

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Poprzedni znacznik ustawia plik układu na *Pages/Shared/_Layout.cshtml* dla wszystkich plików Razor w folderze *Pages.* Aby uzyskać więcej informacji, zobacz [Układ.](xref:razor-pages/index#layout)

### <a name="the-create-page-model"></a>Model strony Tworzenie

Sprawdź model strony *Pages/Movies/Create.cshtml.cs:*

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Metoda `OnGet` inicjuje dowolny stan potrzebny dla strony. Strona Utwórz nie ma żadnego stanu `Page` do zainicjowania, więc jest zwracany. W dalszej części `OnGet` samouczka zobaczysz, że metoda inicjuje stan. Metoda `Page` tworzy `PageResult` obiekt, który renderuje stronę *Create.cshtml.*

Właściwość `Movie` używa `[BindProperty]` atrybutu, aby wyrazić zgodę na [powiązanie modelu.](xref:mvc/models/model-binding) Gdy formularz Utwórz księguje wartości formularza, środowisko uruchomieniowe ASP.NET Core `Movie` wiąże zaksięgowane wartości z modelem.

Metoda `OnPostAsync` jest uruchamiana, gdy dane formularza wpisów strony:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Jeśli występują błędy modelu, formularz jest ponownie wyeksponowany wraz z wszelkimi opublikowanymi danymi formularza. Większość błędów modelu można złapać po stronie klienta przed zaksięgowania formularza. Przykładem błędu modelu jest księgowanie wartości pola daty, które nie może zostać przekonwertowane na datę. Sprawdzanie poprawności po stronie klienta i sprawdzanie poprawności modelu są omówione w dalszej części samouczka.

Jeśli nie ma żadnych błędów modelu, dane są zapisywane, a przeglądarka jest przekierowywane do strony Indeks.

### <a name="the-create-razor-page"></a>Strona Tworzenie maszynki do golenia

Sprawdź plik *strony/filmy/utwórz.cshtml* Razor:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Program Visual `<form method="post">` Studio wyświetla znacznik w charakterystycznej pogrubionej czcionce używanej dla pomocników tagów:

![Widok vs17 strony Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uzyskać więcej informacji na `<form method="post">`temat pomocników tagów, takich jak , zobacz [Pomocników tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Program Visual Studio `<form method="post">` dla komputerów Mac wyświetla znacznik w charakterystycznej pogrubionej czcionce używanej dla pomocników znaczników.

---

Element `<form method="post">` jest [pomocnikiem znaczników formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocnik znacznika formularza automatycznie zawiera [token antyforgery.](xref:security/anti-request-forgery)

Aparat rusztowań tworzy znaczniki Razor dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocnicy znaczników](xref:mvc/views/working-with-forms#the-validation-tag-helpers) `<div asp-validation-summary` sprawdzania `<span asp-validation-for`poprawności (i) wyświetlają błędy sprawdzania poprawności. Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.

[Pomocnik tagu](xref:mvc/views/working-with-forms#the-label-tag-helper) etykiety`<label asp-for="Movie.Title" class="control-label"></label>`( ) generuje `for` podpis etykiety `Title` i atrybut właściwości.

[Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model)
> [Dalej: Baza danych](xref:tutorials/razor-pages/sql)

::: moniker-end
