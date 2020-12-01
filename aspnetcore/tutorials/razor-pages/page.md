---
title: Część 3, strony szkieletowe Razor
author: rick-anderson
description: Część 3 serii samouczków na Razor stronach.
ms.author: riande
ms.date: 09/25/2020
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 4a5369b9e40de89ac9a1895466e7bdd7afb9d32e
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420035"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="a6451-103">Część 3, szkieletowe Razor strony w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6451-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a6451-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6451-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6451-105">Ten samouczek służy do badania Razor stron utworzonych przez tworzenie szkieletów w [poprzednim samouczku](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="a6451-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a6451-106">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a6451-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="a6451-107">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a6451-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="a6451-108">Strony tworzenie, usuwanie, szczegóły i edycja</span><span class="sxs-lookup"><span data-stu-id="a6451-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="a6451-109">Sprawdzanie *stron/filmów/ Index cshtml.cs* strony:</span><span class="sxs-lookup"><span data-stu-id="a6451-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="a6451-110">Razor Strony pochodzą od `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="a6451-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="a6451-111">Zgodnie z Konwencją `PageModel` Klasa pochodna ma nazwę `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="a6451-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="a6451-112">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) , aby dodać `RazorPagesMovieContext` do strony:</span><span class="sxs-lookup"><span data-stu-id="a6451-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="a6451-113">Zobacz [kod asynchroniczny](xref:data/ef-rp/intro#asynchronous-code) , aby uzyskać więcej informacji na temat programowania asynchronicznego przy użyciu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a6451-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="a6451-114">Gdy żądanie zostanie wykonane na stronie, `OnGetAsync` Metoda zwraca listę filmów na Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="a6451-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="a6451-115">Na Razor stronie `OnGetAsync` lub `OnGet` jest wywoływana w celu zainicjowania stanu strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="a6451-116">W takim przypadku `OnGetAsync` Pobiera listę filmów i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="a6451-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="a6451-117">Gdy `OnGet` zwraca `void` lub `OnGetAsync` zwraca `Task` , nie jest używana instrukcja return.</span><span class="sxs-lookup"><span data-stu-id="a6451-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="a6451-118">Na przykład strona prywatność:</span><span class="sxs-lookup"><span data-stu-id="a6451-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="a6451-119">Gdy typem zwracanym jest `IActionResult` lub `Task<IActionResult>` , należy podać instrukcję return.</span><span class="sxs-lookup"><span data-stu-id="a6451-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="a6451-120">Na przykład: *Pages/Films/Create. cshtml. cs.* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="a6451-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="a6451-121">Przejrzyj strony */filmy/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="a6451-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="a6451-122">Razor może przechodzić z kodu HTML do języka C# lub do Razor określonych znaczników.</span><span class="sxs-lookup"><span data-stu-id="a6451-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="a6451-123">Gdy `@` po symbolu następuje [ Razor zastrzeżone słowo kluczowe](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do Razor specyficznego znacznika, w przeciwnym razie przechodzi do języka C#.</span><span class="sxs-lookup"><span data-stu-id="a6451-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="a6451-124">@pageDyrektywa</span><span class="sxs-lookup"><span data-stu-id="a6451-124">The @page directive</span></span>

<span data-ttu-id="a6451-125">`@page` Razor Dyrektywa powoduje, że plik jest akcją MVC, co oznacza, że może obsługiwać żądania.</span><span class="sxs-lookup"><span data-stu-id="a6451-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="a6451-126">`@page` musi być pierwszą Razor dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="a6451-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a6451-127">`@page` i `@model` są przykładami przejścia do Razor specyficznego znacznika.</span><span class="sxs-lookup"><span data-stu-id="a6451-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="a6451-128">Aby uzyskać więcej informacji, zobacz [ Razor składnia](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="a6451-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="a6451-129">@modelDyrektywa</span><span class="sxs-lookup"><span data-stu-id="a6451-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="a6451-130">`@model`Dyrektywa określa typ modelu przekazaną do Razor strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="a6451-131">W poprzednim przykładzie `@model` wiersz powoduje, że `PageModel` Klasa pochodna jest dostępna dla Razor strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="a6451-132">Model jest używany w `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocnikach HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) i na stronie.</span><span class="sxs-lookup"><span data-stu-id="a6451-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="a6451-133">Bada wyrażenie lambda użyte w następującym Pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="a6451-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="a6451-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>Pomocnik html sprawdza właściwość, do `Title` której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="a6451-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="a6451-135">Wyrażenie lambda jest sprawdzane, a nie oceniane.</span><span class="sxs-lookup"><span data-stu-id="a6451-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="a6451-136">Oznacza to, że nie ma żadnych naruszeń dostępu `model` , gdy, `model.Movie` , lub `model.Movie[0]` jest `null` pusta.</span><span class="sxs-lookup"><span data-stu-id="a6451-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="a6451-137">Gdy wyrażenie lambda zostanie obliczone, na przykład w przypadku `@Html.DisplayFor(modelItem => item.Title)` wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="a6451-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="a6451-138">Strona układu</span><span class="sxs-lookup"><span data-stu-id="a6451-138">The layout page</span></span>

<span data-ttu-id="a6451-139">Wybierz menu linki **Razor PagesMovie**, **Home** i **privacy**.</span><span class="sxs-lookup"><span data-stu-id="a6451-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="a6451-140">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="a6451-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="a6451-141">Układ menu jest implementowany w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a6451-142">Otwórz i Przeanalizuj plik *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a6451-143">Szablony [układów](xref:mvc/views/layout) umożliwiają układ kontenera HTML:</span><span class="sxs-lookup"><span data-stu-id="a6451-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="a6451-144">Określone w jednym miejscu.</span><span class="sxs-lookup"><span data-stu-id="a6451-144">Specified in one place.</span></span>
* <span data-ttu-id="a6451-145">Stosowane na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="a6451-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="a6451-146">Znajdź `@RenderBody()` wiersz.</span><span class="sxs-lookup"><span data-stu-id="a6451-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a6451-147">`RenderBody` jest symbolem zastępczym, w którym wszystkie widoki specyficzne dla strony są wyświetlane, *opakowane* na stronie układ.</span><span class="sxs-lookup"><span data-stu-id="a6451-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a6451-148">Na przykład wybierz łącze **prywatność** i widok *strony/prywatność. cshtml* jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="a6451-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="a6451-149">ViewData i układ</span><span class="sxs-lookup"><span data-stu-id="a6451-149">ViewData and layout</span></span>

<span data-ttu-id="a6451-150">Rozważ następujące znaczniki ze *stron/filmów/pliku Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a6451-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="a6451-151">Poprzedni wyróżniony znacznik jest przykładem Razor przejścia do języka C#.</span><span class="sxs-lookup"><span data-stu-id="a6451-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="a6451-152">`{`Znaki i `}` należy ująć w blok kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="a6451-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="a6451-153">`PageModel`Klasa bazowa zawiera `ViewData` Właściwość słownika, która może służyć do przekazywania danych do widoku.</span><span class="sxs-lookup"><span data-stu-id="a6451-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="a6451-154">Obiekty są dodawane do `ViewData` słownika przy użyciu znaku \***wartość** _ wzorca.</span><span class="sxs-lookup"><span data-stu-id="a6451-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="a6451-155">W poprzednim przykładzie `Title` Właściwość jest dodawana do `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="a6451-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="a6451-156">`Title`Właściwość jest używana w pliku _Pages/shared/_Layout. cshtml \*.</span><span class="sxs-lookup"><span data-stu-id="a6451-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="a6451-157">Poniższy znacznik pokazuje pierwsze kilka wierszy pliku *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="a6451-158">Wiersz `@*Markup removed for brevity.*@` jest Razor komentarzem.</span><span class="sxs-lookup"><span data-stu-id="a6451-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="a6451-159">W przeciwieństwie do komentarzy HTML `<!-- -->` Razor Komentarze nie są wysyłane do klienta.</span><span class="sxs-lookup"><span data-stu-id="a6451-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="a6451-160">Zobacz [powiadomienia MDN Web docs: wprowadzenie do języka HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) , aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a6451-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="a6451-161">Aktualizowanie układu</span><span class="sxs-lookup"><span data-stu-id="a6451-161">Update the layout</span></span>

1. <span data-ttu-id="a6451-162">Zmień `<title>` element w pliku *Pages/Shared/_Layout. cshtml* , aby wyświetlał **film** zamiast **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a6451-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="a6451-163">Znajdź następujący element zakotwiczony w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="a6451-164">Zastąp poprzedni element następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="a6451-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="a6451-165">Poprzedni element zakotwiczenia jest [pomocnikiem tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a6451-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="a6451-166">W tym przypadku jest to [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a6451-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="a6451-167">`asp-page="/Movies/Index"`Atrybut i wartość pomocnika tagów tworzą link do `/Movies/Index` Razor strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="a6451-168">`asp-area`Wartość atrybutu jest pusta, dlatego obszar nie jest używany w łączu.</span><span class="sxs-lookup"><span data-stu-id="a6451-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="a6451-169">Aby uzyskać więcej informacji, zobacz [obszary](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="a6451-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="a6451-170">Zapisz zmiany i przetestuj aplikację, wybierając łącze **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="a6451-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="a6451-171">Jeśli występują problemy, zobacz plik [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="a6451-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="a6451-172">Przetestuj linki **Home**, **RpMovie**, **Create**, **Edit** i **delete** .</span><span class="sxs-lookup"><span data-stu-id="a6451-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="a6451-173">Każda Strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Po utworzeniu zakładki na stronie tytuł jest używany dla zakładki.</span><span class="sxs-lookup"><span data-stu-id="a6451-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="a6451-174">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="a6451-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a6451-175">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i nieUS-Englishych formatów daty, należy wykonać kroki w celu globalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6451-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="a6451-176">Aby uzyskać instrukcje dotyczące dodawania przecinków dziesiętnych, zobacz ten problem w usłudze [GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="a6451-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="a6451-177">`Layout`Właściwość jest ustawiana w pliku *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a6451-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a6451-178">Poprzedzające znaczniki ustawiają plik układu na *Pages/Shared/_Layout. cshtml* dla wszystkich Razor plików w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="a6451-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="a6451-179">Aby uzyskać więcej informacji, zobacz [Układ](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="a6451-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="a6451-180">Model tworzenia strony</span><span class="sxs-lookup"><span data-stu-id="a6451-180">The Create page model</span></span>

<span data-ttu-id="a6451-181">Obejrzyj model stron */filmów/Utwórz. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a6451-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="a6451-182">`OnGet`Metoda inicjuje wszystkie Stany potrzebne dla strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="a6451-183">Strona tworzenia nie ma żadnego stanu do zainicjowania, więc `Page` jest zwracana.</span><span class="sxs-lookup"><span data-stu-id="a6451-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="a6451-184">W dalszej części tego samouczka `OnGet` przedstawiono przykład inicjowania stanu.</span><span class="sxs-lookup"><span data-stu-id="a6451-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="a6451-185">`Page`Metoda tworzy `PageResult` obiekt, który renderuje stronę *Create. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="a6451-186">`Movie`Właściwość używa atrybutu [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) do przystąpienia do [powiązania modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="a6451-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a6451-187">Gdy formularz tworzenia zapisuje wartości formularza, środowisko uruchomieniowe ASP.NET Core tworzy powiązanie opublikowanych wartości z `Movie` modelem.</span><span class="sxs-lookup"><span data-stu-id="a6451-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="a6451-188">`OnPostAsync`Metoda jest uruchamiana, gdy strona zapisuje dane formularza:</span><span class="sxs-lookup"><span data-stu-id="a6451-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="a6451-189">Jeśli występują jakieś błędy modelu, formularz jest ponownie wyświetlany wraz z wszelkimi opublikowanymi danymi formularza.</span><span class="sxs-lookup"><span data-stu-id="a6451-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="a6451-190">Większość błędów modelu można przechwycić po stronie klienta przed opublikowaniem formularza.</span><span class="sxs-lookup"><span data-stu-id="a6451-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="a6451-191">Przykładem błędu modelu jest opublikowanie wartości pola daty, którego nie można przekonwertować na datę.</span><span class="sxs-lookup"><span data-stu-id="a6451-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="a6451-192">Weryfikowanie po stronie klienta i sprawdzanie poprawności modelu zostały omówione w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="a6451-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="a6451-193">Jeśli nie ma błędów modelu:</span><span class="sxs-lookup"><span data-stu-id="a6451-193">If there are no model errors:</span></span>

* <span data-ttu-id="a6451-194">Dane są zapisywane.</span><span class="sxs-lookup"><span data-stu-id="a6451-194">The data is saved.</span></span>
* <span data-ttu-id="a6451-195">Przeglądarka zostanie przekierowana na Index stronę.</span><span class="sxs-lookup"><span data-stu-id="a6451-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="a6451-196">Strona tworzenie Razor</span><span class="sxs-lookup"><span data-stu-id="a6451-196">The Create Razor Page</span></span>

<span data-ttu-id="a6451-197">Przejrzyj strony */filmy/Utwórz* Razor plik stronicowania. cshtml:</span><span class="sxs-lookup"><span data-stu-id="a6451-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="a6451-198">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6451-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6451-199">Program Visual Studio wyświetla następujące znaczniki w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="a6451-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Widok VS17 na stronie Tworzenie. cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a6451-201">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a6451-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a6451-202">Następujące pomocnicy tagów są pokazane w powyższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="a6451-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="a6451-203">`<form method="post">`Element jest [pomocnikiem tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a6451-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="a6451-204">Pomocnik tagu formularza automatycznie zawiera [token antysfałszowany](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="a6451-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a6451-205">Aparat szkieletu tworzy Razor znaczniki dla każdego pola w modelu, z wyjątkiem identyfikatora, podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="a6451-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="a6451-206">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` i `<span asp-validation-for` ) wyświetla błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="a6451-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="a6451-207">Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.</span><span class="sxs-lookup"><span data-stu-id="a6451-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="a6451-208">[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) generuje podpis etykiety i `[for]` atrybut `Title` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a6451-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="a6451-209">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="a6451-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="a6451-210">Aby uzyskać więcej informacji na temat pomocników tagów `<form method="post">` , takich jak, zobacz [pomocnicy tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a6451-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6451-211">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a6451-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a6451-212">[Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model) 
>  [Dalej: Pracuj z bazą danych](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="a6451-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="a6451-213">Strony tworzenie, usuwanie, szczegóły i edycja</span><span class="sxs-lookup"><span data-stu-id="a6451-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="a6451-214">Sprawdzanie *stron/filmów/ Index cshtml.cs* strony:</span><span class="sxs-lookup"><span data-stu-id="a6451-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="a6451-215">Razor Strony pochodzą od `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="a6451-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="a6451-216">Zgodnie z Konwencją `PageModel` Klasa pochodna ma nazwę `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="a6451-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="a6451-217">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) , aby dodać `RazorPagesMovieContext` do strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="a6451-218">Strony szkieletowe postępują zgodnie z tym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="a6451-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="a6451-219">Zobacz [kod asynchroniczny](xref:data/ef-rp/intro#asynchronous-code) , aby uzyskać więcej informacji na temat programowania asynchronicznego przy użyciu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a6451-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="a6451-220">Gdy żądanie zostanie wykonane na stronie, `OnGetAsync` Metoda zwraca listę filmów na Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="a6451-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="a6451-221">`OnGetAsync` lub `OnGet` jest wywoływana na Razor stronie w celu zainicjowania stanu dla strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="a6451-222">W takim przypadku `OnGetAsync` Pobiera listę filmów i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="a6451-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="a6451-223">Gdy `OnGet` zwraca `void` lub `OnGetAsync` zwraca `Task` , nie jest używana żadna metoda Return.</span><span class="sxs-lookup"><span data-stu-id="a6451-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="a6451-224">Gdy typem zwracanym jest `IActionResult` lub `Task<IActionResult>` , należy podać instrukcję return.</span><span class="sxs-lookup"><span data-stu-id="a6451-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="a6451-225">Na przykład: *Pages/Films/Create. cshtml. cs.* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="a6451-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="a6451-226">Przejrzyj strony */filmy/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="a6451-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="a6451-227">Razor może przechodzić z kodu HTML do języka C# lub do Razor określonych znaczników.</span><span class="sxs-lookup"><span data-stu-id="a6451-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="a6451-228">Gdy `@` po symbolu następuje [ Razor zastrzeżone słowo kluczowe](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do Razor specyficznego znacznika, w przeciwnym razie przechodzi do języka C#.</span><span class="sxs-lookup"><span data-stu-id="a6451-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="a6451-229">`@page` Razor Dyrektywa powoduje, że plik jest akcją MVC, co oznacza, że może obsługiwać żądania.</span><span class="sxs-lookup"><span data-stu-id="a6451-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="a6451-230">`@page` musi być pierwszą Razor dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="a6451-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a6451-231">`@page` jest przykładem przejścia do Razor specyficznego znacznika.</span><span class="sxs-lookup"><span data-stu-id="a6451-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="a6451-232">Aby uzyskać więcej informacji, zobacz [ Razor składnia](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="a6451-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="a6451-233">@modelDyrektywa</span><span class="sxs-lookup"><span data-stu-id="a6451-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="a6451-234">`@model`Dyrektywa określa typ modelu przekazaną do Razor strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="a6451-235">W poprzednim przykładzie `@model` wiersz powoduje, że `PageModel` Klasa pochodna jest dostępna dla Razor strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="a6451-236">Model jest używany w `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocnikach HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) i na stronie.</span><span class="sxs-lookup"><span data-stu-id="a6451-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="a6451-237">Pomocniki HTML</span><span class="sxs-lookup"><span data-stu-id="a6451-237">HTML Helpers</span></span>

<span data-ttu-id="a6451-238">Bada wyrażenie lambda użyte w następującym Pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="a6451-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="a6451-239"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>Pomocnik html sprawdza właściwość, do `Title` której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="a6451-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="a6451-240">Wyrażenie lambda jest sprawdzane, a nie oceniane.</span><span class="sxs-lookup"><span data-stu-id="a6451-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="a6451-241">Oznacza to, że nie ma żadnych naruszeń dostępu `model` , gdy, `model.Movie` , lub `model.Movie[0]` są `null` puste.</span><span class="sxs-lookup"><span data-stu-id="a6451-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="a6451-242">Gdy wyrażenie lambda zostanie obliczone, na przykład w przypadku `@Html.DisplayFor(modelItem => item.Title)` wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="a6451-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="a6451-243">Strona układu</span><span class="sxs-lookup"><span data-stu-id="a6451-243">The layout page</span></span>

<span data-ttu-id="a6451-244">Wybierz menu linki **Razor PagesMovie**, **Home** i **privacy**.</span><span class="sxs-lookup"><span data-stu-id="a6451-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="a6451-245">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="a6451-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="a6451-246">Układ menu jest implementowany w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a6451-247">Otwórz i Przeanalizuj plik *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a6451-248">Szablony [układów](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML lokacji w jednym miejscu, a następnie zastosowanie jej na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="a6451-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="a6451-249">Znajdź `@RenderBody()` wiersz.</span><span class="sxs-lookup"><span data-stu-id="a6451-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a6451-250">`RenderBody` jest symbolem zastępczym, w którym wszystkie utworzone widoki związane ze stroną są wyświetlane, *opakowane* na stronie układ.</span><span class="sxs-lookup"><span data-stu-id="a6451-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a6451-251">Na przykład w przypadku wybrania linku **prywatność** widok **strony/prywatność. cshtml** jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="a6451-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="a6451-252">ViewData i układ</span><span class="sxs-lookup"><span data-stu-id="a6451-252">ViewData and layout</span></span>

<span data-ttu-id="a6451-253">Rozważmy następujący kod ze *stron/filmów/pliku Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a6451-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="a6451-254">Poprzedni wyróżniony kod jest przykładem Razor przejścia do języka C#.</span><span class="sxs-lookup"><span data-stu-id="a6451-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="a6451-255">`{`Znaki i `}` należy ująć w blok kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="a6451-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="a6451-256">`PageModel`Klasa bazowa ma `ViewData` Właściwość dictionary, która może służyć do dodawania danych, które mają zostać przekazane do widoku.</span><span class="sxs-lookup"><span data-stu-id="a6451-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="a6451-257">Obiekty można dodawać do `ViewData` słownika przy użyciu wzorca klucz/wartość.</span><span class="sxs-lookup"><span data-stu-id="a6451-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="a6451-258">W poprzednim przykładzie `Title` Właściwość jest dodawana do `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="a6451-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="a6451-259">`Title`Właściwość jest używana w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a6451-260">Poniższy znacznik pokazuje pierwsze kilka wierszy pliku *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="a6451-261">Wiersz `@*Markup removed for brevity.*@` jest Razor komentarzem.</span><span class="sxs-lookup"><span data-stu-id="a6451-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="a6451-262">W przeciwieństwie do komentarzy HTML `<!-- -->` Razor Komentarze nie są wysyłane do klienta.</span><span class="sxs-lookup"><span data-stu-id="a6451-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="a6451-263">Zobacz [powiadomienia MDN Web docs: wprowadzenie do języka HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) , aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a6451-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="a6451-264">Aktualizowanie układu</span><span class="sxs-lookup"><span data-stu-id="a6451-264">Update the layout</span></span>

<span data-ttu-id="a6451-265">Zmień `<title>` element w pliku *Pages/Shared/_Layout. cshtml* , aby wyświetlał **film** zamiast **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a6451-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="a6451-266">Znajdź następujący element zakotwiczony w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="a6451-267">Zastąp poprzedni element następującym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="a6451-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="a6451-268">Poprzedni element zakotwiczenia jest [pomocnikiem tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a6451-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="a6451-269">W tym przypadku jest to [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a6451-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="a6451-270">`asp-page="/Movies/Index"`Atrybut i wartość pomocnika tagów tworzą link do `/Movies/Index` Razor strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="a6451-271">`asp-area`Wartość atrybutu jest pusta, dlatego obszar nie jest używany w łączu.</span><span class="sxs-lookup"><span data-stu-id="a6451-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="a6451-272">Aby uzyskać więcej informacji, zobacz [obszary](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="a6451-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="a6451-273">Zapisz zmiany i przetestuj aplikację, klikając łącze **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="a6451-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="a6451-274">Jeśli występują problemy, zobacz plik [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="a6451-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="a6451-275">Przetestuj inne linki (**Narzędzia główne**, **RpMovie**, **Utwórz**, **Edytuj** i **Usuń**).</span><span class="sxs-lookup"><span data-stu-id="a6451-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="a6451-276">Każda Strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Po utworzeniu zakładki na stronie tytuł jest używany dla zakładki.</span><span class="sxs-lookup"><span data-stu-id="a6451-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="a6451-277">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="a6451-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a6451-278">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i nieUS-Englishych formatów daty, należy wykonać kroki w celu globalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6451-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="a6451-279">Ten [problem w usłudze GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) zawiera instrukcje dotyczące dodawania przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="a6451-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="a6451-280">`Layout`Właściwość jest ustawiana w pliku *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a6451-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a6451-281">Poprzedzające znaczniki ustawiają plik układu na *Pages/Shared/_Layout. cshtml* dla wszystkich Razor plików w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="a6451-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="a6451-282">Aby uzyskać więcej informacji, zobacz [Układ](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="a6451-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="a6451-283">Model tworzenia strony</span><span class="sxs-lookup"><span data-stu-id="a6451-283">The Create page model</span></span>

<span data-ttu-id="a6451-284">Obejrzyj model stron */filmów/Utwórz. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a6451-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="a6451-285">`OnGet`Metoda inicjuje wszystkie Stany potrzebne dla strony.</span><span class="sxs-lookup"><span data-stu-id="a6451-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="a6451-286">Strona tworzenia nie ma żadnego stanu do zainicjowania, więc `Page` jest zwracana.</span><span class="sxs-lookup"><span data-stu-id="a6451-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="a6451-287">W dalszej części tego samouczka zobaczysz `OnGet` stan zainicjowania metody.</span><span class="sxs-lookup"><span data-stu-id="a6451-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="a6451-288">`Page`Metoda tworzy `PageResult` obiekt, który renderuje stronę *Create. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6451-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="a6451-289">`Movie`Właściwość używa atrybutu [[BindProperty]] <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> do przystąpienia do [powiązania modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="a6451-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a6451-290">Gdy formularz tworzenia zapisuje wartości formularza, środowisko uruchomieniowe ASP.NET Core tworzy powiązanie opublikowanych wartości z `Movie` modelem.</span><span class="sxs-lookup"><span data-stu-id="a6451-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="a6451-291">`OnPostAsync`Metoda jest uruchamiana, gdy strona zapisuje dane formularza:</span><span class="sxs-lookup"><span data-stu-id="a6451-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="a6451-292">Jeśli występują jakieś błędy modelu, formularz jest ponownie wyświetlany wraz z wszelkimi opublikowanymi danymi formularza.</span><span class="sxs-lookup"><span data-stu-id="a6451-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="a6451-293">Większość błędów modelu można przechwycić po stronie klienta przed opublikowaniem formularza.</span><span class="sxs-lookup"><span data-stu-id="a6451-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="a6451-294">Przykładem błędu modelu jest opublikowanie wartości pola daty, którego nie można przekonwertować na datę.</span><span class="sxs-lookup"><span data-stu-id="a6451-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="a6451-295">Weryfikowanie po stronie klienta i sprawdzanie poprawności modelu zostały omówione w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="a6451-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="a6451-296">Jeśli nie ma żadnych błędów modelu, dane zostaną zapisane i przeglądarka zostanie przekierowana na Index stronę.</span><span class="sxs-lookup"><span data-stu-id="a6451-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="a6451-297">Strona tworzenie Razor</span><span class="sxs-lookup"><span data-stu-id="a6451-297">The Create Razor Page</span></span>

<span data-ttu-id="a6451-298">Przejrzyj strony */filmy/Utwórz* Razor plik stronicowania. cshtml:</span><span class="sxs-lookup"><span data-stu-id="a6451-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="a6451-299">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6451-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6451-300">Program Visual Studio Wyświetla `<form method="post">` tag w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="a6451-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Widok VS17 na stronie Tworzenie. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6451-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6451-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a6451-303">Aby uzyskać więcej informacji na temat pomocników tagów `<form method="post">` , takich jak, zobacz [pomocnicy tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a6451-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6451-304">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a6451-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a6451-305">Visual Studio dla komputerów Mac wyświetla `<form method="post">` tag w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="a6451-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="a6451-306">`<form method="post">`Element jest [pomocnikiem tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a6451-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="a6451-307">Pomocnik tagu formularza automatycznie zawiera [token antysfałszowany](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="a6451-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a6451-308">Aparat szkieletu tworzy Razor znaczniki dla każdego pola w modelu, z wyjątkiem identyfikatora, podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="a6451-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="a6451-309">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` i `<span asp-validation-for` ) wyświetla błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="a6451-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="a6451-310">Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.</span><span class="sxs-lookup"><span data-stu-id="a6451-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="a6451-311">[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) generuje podpis etykiety i `[for]` atrybut `Title` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a6451-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="a6451-312">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="a6451-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6451-313">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a6451-313">Additional resources</span></span>

* [<span data-ttu-id="a6451-314">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="a6451-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="a6451-315">[Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model) 
>  [Dalej: Pracuj z bazą danych](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="a6451-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
