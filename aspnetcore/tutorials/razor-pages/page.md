---
title: Część 3, szkieletowe Razor strony w ASP.NET Core
author: rick-anderson
description: Część 3 serii samouczków na Razor stronach.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 6195982f902c17d835d2675c1231eed347d603c2
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652817"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a>Część 3, szkieletowe Razor strony w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Ten samouczek służy do badania Razor stron utworzonych przez tworzenie szkieletów w [poprzednim samouczku](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Strony tworzenie, usuwanie, szczegóły i edycja

Zapoznaj się z modelem stron */filmów/index. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

RazorStrony pochodzą od `PageModel` . Zgodnie z Konwencją `PageModel` Klasa pochodna jest wywoływana `<PageName>Model` . Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) , aby dodać `RazorPagesMovieContext` do strony. Wszystkie strony szkieletowe są zgodne z tym wzorcem. Zobacz [kod asynchroniczny](xref:data/ef-rp/intro#asynchronous-code) , aby uzyskać więcej informacji na temat programowania asynchronicznego przy użyciu Entity Framework.

Gdy żądanie zostanie wykonane na stronie, `OnGetAsync` Metoda zwraca listę filmów na Razor stronie. `OnGetAsync`lub `OnGet` jest wywoływana w celu zainicjowania stanu strony. W takim przypadku `OnGetAsync` Pobiera listę filmów i wyświetla je.

Gdy `OnGet` zwraca `void` lub `OnGetAsync` zwraca `Task` , nie jest używana instrukcja return. Gdy typem zwracanym jest `IActionResult` lub `Task<IActionResult>` , należy podać instrukcję return. Na przykład: *Pages/Films/Create. cshtml. cs.* `OnPostAsync`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Zapoznaj się ze stroną *stron/filmów/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razormoże przechodzić z kodu HTML do języka C# lub do Razor określonych znaczników. Gdy `@` po symbolu następuje [ Razor zastrzeżone słowo kluczowe](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do Razor specyficznego znacznika, w przeciwnym razie przechodzi do języka C#.

### <a name="the-page-directive"></a>@pageDyrektywa

`@page` Razor Dyrektywa powoduje, że plik jest akcją MVC, co oznacza, że może obsługiwać żądania. `@page`musi być pierwszą Razor dyrektywą na stronie. `@page`jest przykładem przejścia do Razor specyficznego znacznika. Aby uzyskać więcej informacji, zobacz [ Razor składnia](xref:mvc/views/razor#razor-syntax) .

Bada wyrażenie lambda użyte w następującym Pomocniku HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

`DisplayNameFor`Pomocnik html sprawdza właściwość, do `Title` której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną. Wyrażenie lambda jest sprawdzane, a nie oceniane. Oznacza to, że nie ma żadnych naruszeń dostępu `model` , gdy, `model.Movie` , lub `model.Movie[0]` jest `null` pusta. Gdy wyrażenie lambda jest oceniane (na przykład z `@Html.DisplayFor(modelItem => item.Title)` ), wartości właściwości modelu są oceniane.

<a name="md"></a>

### <a name="the-model-directive"></a>@modelDyrektywa

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model`Dyrektywa określa typ modelu przekazaną do Razor strony. W poprzednim przykładzie `@model` wiersz powoduje, że `PageModel` Klasa pochodna jest dostępna dla Razor strony. Model jest używany w `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocnikach HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) i na stronie.

### <a name="the-layout-page"></a>Strona układu

Wybierz linki menu (**RazorPagesMovie**, **Home**i **privacy**). Każda Strona wyświetla ten sam układ menu. Układ menu jest implementowany w pliku *Pages/Shared/_Layout. cshtml* . Otwórz plik *Pages/Shared/_Layout. cshtml* .

Szablony [układów](xref:mvc/views/layout) umożliwiają układ kontenera HTML:

* Określone w jednym miejscu.
* Stosowane na wielu stronach w witrynie.

Znajdź `@RenderBody()` wiersz. `RenderBody`jest symbolem zastępczym, w którym wszystkie widoki specyficzne dla strony są wyświetlane, *opakowane* na stronie układ. Na przykład wybierz łącze **prywatność** i widok *strony/prywatność. cshtml* jest renderowany wewnątrz `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData i układ

Rozważ następujące oznakowanie w pliku *Pages/Films/index. cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Poprzedni wyróżniony znacznik jest przykładem Razor przejścia do języka C#. `{`Znaki i `}` należy ująć w blok kodu w języku C#.

`PageModel`Klasa bazowa zawiera `ViewData` Właściwość słownika, która może służyć do przekazywania danych do widoku. Obiekty są dodawane do `ViewData` słownika przy użyciu wzorca klucz/wartość. W poprzednim przykładzie `"Title"` Właściwość jest dodawana do `ViewData` słownika.

`"Title"`Właściwość jest używana w pliku *Pages/Shared/_Layout. cshtml* . Poniższy znacznik pokazuje pierwsze kilka wierszy pliku *_Layout. cshtml* .

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Wiersz `@*Markup removed for brevity.*@` jest Razor komentarzem. W przeciwieństwie do komentarzy HTML ( `<!-- -->` ), Razor Komentarze nie są wysyłane do klienta.

### <a name="update-the-layout"></a>Aktualizowanie układu

Zmień `<title>` element w pliku *Pages/Shared/_Layout. cshtml* , aby wyświetlał **film** zamiast **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Znajdź następujący element zakotwiczony w pliku *Pages/Shared/_Layout. cshtml* .

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Zastąp poprzedni element następującym znacznikiem:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Poprzedni element zakotwiczenia jest [pomocnikiem tagów](xref:mvc/views/tag-helpers/intro). W tym przypadku jest to [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). `asp-page="/Movies/Index"`Atrybut i wartość pomocnika tagów tworzą link do `/Movies/Index` Razor strony. `asp-area`Wartość atrybutu jest pusta, dlatego obszar nie jest używany w łączu. Aby uzyskać więcej informacji, zobacz [obszary](xref:mvc/controllers/areas) .

Zapisz zmiany i przetestuj aplikację, klikając łącze **RpMovie** . Jeśli występują problemy, zobacz plik [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) w usłudze GitHub.

Przetestuj inne linki (**Narzędzia główne**, **RpMovie**, **Utwórz**, **Edytuj**i **Usuń**). Każda Strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Po utworzeniu zakładki na stronie tytuł jest używany dla zakładki.

> [!NOTE]
> W polu nie można wprowadzać przecinków dziesiętnych `Price` . Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i formatów dat innych niż angielski, należy wykonać kroki w celu globalizacji aplikacji. Aby uzyskać instrukcje dotyczące dodawania przecinków dziesiętnych, zobacz ten problem w usłudze [GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .

`Layout`Właściwość jest ustawiana w pliku *Pages/_ViewStart. cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Poprzedzające znaczniki ustawiają plik układu na *Pages/Shared/_Layout. cshtml* dla wszystkich Razor plików w folderze *Pages* . Aby uzyskać więcej informacji, zobacz [Układ](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Model tworzenia strony

Obejrzyj model stron */filmów/Utwórz. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet`Metoda inicjuje wszystkie Stany potrzebne dla strony. Strona tworzenia nie ma żadnego stanu do zainicjowania, więc `Page` jest zwracana. W dalszej części tego samouczka `OnGet` przedstawiono przykład inicjowania stanu. `Page`Metoda tworzy `PageResult` obiekt, który renderuje stronę *Create. cshtml* .

`Movie`Właściwość używa atrybutu, `[BindProperty]` Aby wybrać [powiązanie modelu](xref:mvc/models/model-binding). Gdy formularz tworzenia zapisuje wartości formularza, środowisko uruchomieniowe ASP.NET Core tworzy powiązanie opublikowanych wartości z `Movie` modelem.

`OnPostAsync`Metoda jest uruchamiana, gdy strona zapisuje dane formularza:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Jeśli występują jakieś błędy modelu, formularz jest ponownie wyświetlany wraz z wszelkimi opublikowanymi danymi formularza. Większość błędów modelu można przechwycić po stronie klienta przed opublikowaniem formularza. Przykładem błędu modelu jest opublikowanie wartości pola daty, którego nie można przekonwertować na datę. Weryfikowanie po stronie klienta i sprawdzanie poprawności modelu zostały omówione w dalszej części tego samouczka.

Jeśli nie ma żadnych błędów modelu, dane zostaną zapisane i przeglądarka zostanie przekierowana na stronę indeksu.

### <a name="the-create-razor-page"></a>Strona tworzenie Razor

Przejrzyj strony */filmy/Utwórz* Razor plik stronicowania. cshtml:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Program Visual Studio wyświetla następujące znaczniki w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Widok VS17 na stronie Tworzenie. cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Następujące pomocnicy tagów są pokazane w powyższym znaczniku:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Program Visual Studio wyświetla następujące znaczniki w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

`<form method="post">`Element jest [pomocnikiem tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocnik tagu formularza automatycznie zawiera [token antysfałszowany](xref:security/anti-request-forgery).

Aparat szkieletu tworzy Razor znaczniki dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` i `<span asp-validation-for` ) wyświetla błędy walidacji. Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.

[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) generuje podpis etykiety i `for` atrybut `Title` właściwości.

[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.

Aby uzyskać więcej informacji na temat pomocników tagów `<form method="post">` , takich jak, zobacz [pomocnicy tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Zasoby dodatkowe

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model) 
>  [Dalej: baza danych](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Ten samouczek służy do badania Razor stron utworzonych przez tworzenie szkieletów w [poprzednim samouczku](xref:tutorials/razor-pages/model).

[Wyświetl lub Pobierz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) przykład.

## <a name="the-create-delete-details-and-edit-pages"></a>Strony tworzenie, usuwanie, szczegóły i edycja

Zapoznaj się z modelem stron */filmów/index. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

RazorStrony pochodzą od `PageModel` . Zgodnie z Konwencją `PageModel` Klasa pochodna jest wywoływana `<PageName>Model` . Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) , aby dodać `RazorPagesMovieContext` do strony. Wszystkie strony szkieletowe są zgodne z tym wzorcem. Zobacz [kod asynchroniczny](xref:data/ef-rp/intro#asynchronous-code) , aby uzyskać więcej informacji na temat programowania asynchronicznego przy użyciu Entity Framework.

Gdy żądanie zostanie wykonane na stronie, `OnGetAsync` Metoda zwraca listę filmów na Razor stronie. `OnGetAsync`lub `OnGet` jest wywoływana na Razor stronie w celu zainicjowania stanu dla strony. W takim przypadku `OnGetAsync` Pobiera listę filmów i wyświetla je.

Gdy `OnGet` zwraca `void` lub `OnGetAsync` zwraca `Task` , nie jest używana żadna metoda Return. Gdy typem zwracanym jest `IActionResult` lub `Task<IActionResult>` , należy podać instrukcję return. Na przykład: *Pages/Films/Create. cshtml. cs.* `OnPostAsync`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Zapoznaj się ze stroną *stron/filmów/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razormoże przechodzić z kodu HTML do języka C# lub do Razor określonych znaczników. Gdy `@` po symbolu następuje [ Razor zastrzeżone słowo kluczowe](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do Razor specyficznego znacznika, w przeciwnym razie przechodzi do języka C#.

`@page` Razor Dyrektywa powoduje, że plik jest akcją MVC, co oznacza, że może obsługiwać żądania. `@page`musi być pierwszą Razor dyrektywą na stronie. `@page`jest przykładem przejścia do Razor specyficznego znacznika. Aby uzyskać więcej informacji, zobacz [ Razor składnia](xref:mvc/views/razor#razor-syntax) .

Bada wyrażenie lambda użyte w następującym Pomocniku HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

`DisplayNameFor`Pomocnik html sprawdza właściwość, do `Title` której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną. Wyrażenie lambda jest sprawdzane, a nie oceniane. Oznacza to, że nie ma żadnych naruszeń dostępu `model` , gdy, `model.Movie` , lub `model.Movie[0]` są `null` puste. Gdy wyrażenie lambda jest oceniane (na przykład z `@Html.DisplayFor(modelItem => item.Title)` ), wartości właściwości modelu są oceniane.

<a name="md"></a>

### <a name="the-model-directive"></a>@modelDyrektywa

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model`Dyrektywa określa typ modelu przekazaną do Razor strony. W poprzednim przykładzie `@model` wiersz powoduje, że `PageModel` Klasa pochodna jest dostępna dla Razor strony. Model jest używany w `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocnikach HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) i na stronie.

### <a name="the-layout-page"></a>Strona układu

Wybierz linki menu (**RazorPagesMovie**, **Home**i **privacy**). Każda Strona wyświetla ten sam układ menu. Układ menu jest implementowany w pliku *Pages/Shared/_Layout. cshtml* . Otwórz plik *Pages/Shared/_Layout. cshtml* .

Szablony [układów](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie. Znajdź `@RenderBody()` wiersz. `RenderBody`jest symbolem zastępczym, w którym wszystkie utworzone widoki związane ze stroną są wyświetlane, *opakowane* na stronie układ. Na przykład w przypadku wybrania linku **prywatność** widok **strony/prywatność. cshtml** jest renderowany wewnątrz `RenderBody` metody.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData i układ

Rozważmy następujący kod z pliku *Pages/Films/index. cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Poprzedni wyróżniony kod jest przykładem Razor przejścia do języka C#. `{`Znaki i `}` należy ująć w blok kodu w języku C#.

`PageModel`Klasa bazowa ma `ViewData` Właściwość dictionary, która może służyć do dodawania danych, które mają zostać przekazane do widoku. Obiekty można dodawać do `ViewData` słownika przy użyciu wzorca klucz/wartość. W poprzednim przykładzie właściwość "title" została dodana do `ViewData` słownika.

Właściwość "title" jest używana w pliku *Pages/Shared/_Layout. cshtml* . Poniższy znacznik pokazuje pierwsze kilka wierszy pliku *_Layout. cshtml* .

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Wiersz `@*Markup removed for brevity.*@` jest Razor komentarzem, które nie pojawia się w pliku układu. W przeciwieństwie do komentarzy HTML ( `<!-- -->` ), Razor Komentarze nie są wysyłane do klienta.

### <a name="update-the-layout"></a>Aktualizowanie układu

Zmień `<title>` element w pliku *Pages/Shared/_Layout. cshtml* , aby wyświetlał **film** zamiast **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Znajdź następujący element zakotwiczony w pliku *Pages/Shared/_Layout. cshtml* .

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Zastąp poprzedni element następującym znacznikiem.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Poprzedni element zakotwiczenia jest [pomocnikiem tagów](xref:mvc/views/tag-helpers/intro). W tym przypadku jest to [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). `asp-page="/Movies/Index"`Atrybut i wartość pomocnika tagów tworzą link do `/Movies/Index` Razor strony. `asp-area`Wartość atrybutu jest pusta, dlatego obszar nie jest używany w łączu. Aby uzyskać więcej informacji, zobacz [obszary](xref:mvc/controllers/areas) .

Zapisz zmiany i przetestuj aplikację, klikając łącze **RpMovie** . Jeśli występują problemy, zobacz plik [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) w usłudze GitHub.

Przetestuj inne linki (**Narzędzia główne**, **RpMovie**, **Utwórz**, **Edytuj**i **Usuń**). Każda Strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Po utworzeniu zakładki na stronie tytuł jest używany dla zakładki.

> [!NOTE]
> W polu nie można wprowadzać przecinków dziesiętnych `Price` . Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i formatów dat innych niż angielski, należy wykonać kroki w celu globalizacji aplikacji. Ten [problem w usłudze GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) zawiera instrukcje dotyczące dodawania przecinków dziesiętnych.

`Layout`Właściwość jest ustawiana w pliku *Pages/_ViewStart. cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Poprzedzające znaczniki ustawiają plik układu na *Pages/Shared/_Layout. cshtml* dla wszystkich Razor plików w folderze *Pages* . Aby uzyskać więcej informacji, zobacz [Układ](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Model tworzenia strony

Obejrzyj model stron */filmów/Utwórz. cshtml. cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet`Metoda inicjuje wszystkie Stany potrzebne dla strony. Strona tworzenia nie ma żadnego stanu do zainicjowania, więc `Page` jest zwracana. W dalszej części tego samouczka zobaczysz `OnGet` stan zainicjowania metody. `Page`Metoda tworzy `PageResult` obiekt, który renderuje stronę *Create. cshtml* .

`Movie`Właściwość używa atrybutu, `[BindProperty]` Aby wybrać [powiązanie modelu](xref:mvc/models/model-binding). Gdy formularz tworzenia zapisuje wartości formularza, środowisko uruchomieniowe ASP.NET Core tworzy powiązanie opublikowanych wartości z `Movie` modelem.

`OnPostAsync`Metoda jest uruchamiana, gdy strona zapisuje dane formularza:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Jeśli występują jakieś błędy modelu, formularz jest ponownie wyświetlany wraz z wszelkimi opublikowanymi danymi formularza. Większość błędów modelu można przechwycić po stronie klienta przed opublikowaniem formularza. Przykładem błędu modelu jest opublikowanie wartości pola daty, którego nie można przekonwertować na datę. Weryfikowanie po stronie klienta i sprawdzanie poprawności modelu zostały omówione w dalszej części tego samouczka.

Jeśli nie ma żadnych błędów modelu, dane zostaną zapisane i przeglądarka zostanie przekierowana na stronę indeksu.

### <a name="the-create-razor-page"></a>Strona tworzenie Razor

Przejrzyj strony */filmy/Utwórz* Razor plik stronicowania. cshtml:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Program Visual Studio Wyświetla `<form method="post">` tag w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:

![Widok VS17 na stronie Tworzenie. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uzyskać więcej informacji na temat pomocników tagów `<form method="post">` , takich jak, zobacz [pomocnicy tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Visual Studio dla komputerów Mac wyświetla `<form method="post">` tag w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów.

---

`<form method="post">`Element jest [pomocnikiem tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocnik tagu formularza automatycznie zawiera [token antysfałszowany](xref:security/anti-request-forgery).

Aparat szkieletu tworzy Razor znaczniki dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` i `<span asp-validation-for` ) wyświetla błędy walidacji. Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.

[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) generuje podpis etykiety i `for` atrybut `Title` właściwości.

[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka usługi YouTube](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model) 
>  [Dalej: baza danych](xref:tutorials/razor-pages/sql)

::: moniker-end
