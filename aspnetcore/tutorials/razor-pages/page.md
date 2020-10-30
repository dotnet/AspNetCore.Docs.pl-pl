---
title: 'Część 3, szkieletowe :::no-loc(Razor)::: strony w ASP.NET Core'
author: rick-anderson
description: 'Część 3 serii samouczków na :::no-loc(Razor)::: stronach.'
ms.author: riande
ms.date: 08/17/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/page
ms.openlocfilehash: 7d0085e1d444de30ca124ef544668122ab350c93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060056"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="124bb-103">Część 3, szkieletowe :::no-loc(Razor)::: strony w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="124bb-103">Part 3, scaffolded :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="124bb-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="124bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="124bb-105">Ten samouczek służy do badania :::no-loc(Razor)::: stron utworzonych przez tworzenie szkieletów w [poprzednim samouczku](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="124bb-105">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="124bb-106">Strony tworzenie, usuwanie, szczegóły i edycja</span><span class="sxs-lookup"><span data-stu-id="124bb-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="124bb-107">Zapoznaj się z modelem stron */filmów/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="124bb-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="124bb-108">:::no-loc(Razor)::: Strony pochodzą od `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="124bb-108">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="124bb-109">Zgodnie z Konwencją `PageModel` Klasa pochodna jest wywoływana `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="124bb-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="124bb-110">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) , aby dodać `:::no-loc(Razor):::PagesMovieContext` do strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="124bb-111">Wszystkie strony szkieletowe są zgodne z tym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="124bb-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="124bb-112">Zobacz [kod asynchroniczny](xref:data/ef-rp/intro#asynchronous-code) , aby uzyskać więcej informacji na temat programowania asynchronicznego przy użyciu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="124bb-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="124bb-113">Gdy żądanie zostanie wykonane na stronie, `OnGetAsync` Metoda zwraca listę filmów na :::no-loc(Razor)::: stronie.</span><span class="sxs-lookup"><span data-stu-id="124bb-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-114">`OnGetAsync` lub `OnGet` jest wywoływana w celu zainicjowania stanu strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="124bb-115">W takim przypadku `OnGetAsync` Pobiera listę filmów i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="124bb-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="124bb-116">Gdy `OnGet` zwraca `void` lub `OnGetAsync` zwraca `Task` , nie jest używana instrukcja return.</span><span class="sxs-lookup"><span data-stu-id="124bb-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="124bb-117">Gdy typem zwracanym jest `IActionResult` lub `Task<IActionResult>` , należy podać instrukcję return.</span><span class="sxs-lookup"><span data-stu-id="124bb-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="124bb-118">Na przykład: *Pages/Films/Create. cshtml. cs.* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="124bb-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="124bb-119">Zapoznaj się ze stroną *stron/filmów/index. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="124bb-119">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="124bb-120">:::no-loc(Razor)::: może przechodzić z kodu HTML do języka C# lub do :::no-loc(Razor)::: określonych znaczników.</span><span class="sxs-lookup"><span data-stu-id="124bb-120">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="124bb-121">Gdy `@` po symbolu następuje [ :::no-loc(Razor)::: zastrzeżone słowo kluczowe](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do :::no-loc(Razor)::: specyficznego znacznika, w przeciwnym razie przechodzi do języka C#.</span><span class="sxs-lookup"><span data-stu-id="124bb-121">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="124bb-122">@pageDyrektywa</span><span class="sxs-lookup"><span data-stu-id="124bb-122">The @page directive</span></span>

<span data-ttu-id="124bb-123">`@page` :::no-loc(Razor)::: Dyrektywa powoduje, że plik jest akcją MVC, co oznacza, że może obsługiwać żądania.</span><span class="sxs-lookup"><span data-stu-id="124bb-123">The `@page` :::no-loc(Razor)::: directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="124bb-124">`@page` musi być pierwszą :::no-loc(Razor)::: dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="124bb-124">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="124bb-125">`@page` jest przykładem przejścia do :::no-loc(Razor)::: specyficznego znacznika.</span><span class="sxs-lookup"><span data-stu-id="124bb-125">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="124bb-126">Aby uzyskać więcej informacji, zobacz [ :::no-loc(Razor)::: składnia](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="124bb-126">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="124bb-127">Bada wyrażenie lambda użyte w następującym Pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="124bb-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="124bb-128">`DisplayNameFor`Pomocnik html sprawdza właściwość, do `Title` której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="124bb-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="124bb-129">Wyrażenie lambda jest sprawdzane, a nie oceniane.</span><span class="sxs-lookup"><span data-stu-id="124bb-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="124bb-130">Oznacza to, że nie ma żadnych naruszeń dostępu `model` , gdy, `model.Movie` , lub `model.Movie[0]` jest `null` pusta.</span><span class="sxs-lookup"><span data-stu-id="124bb-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="124bb-131">Gdy wyrażenie lambda jest oceniane (na przykład z `@Html.DisplayFor(modelItem => item.Title)` ), wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="124bb-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="124bb-132">@modelDyrektywa</span><span class="sxs-lookup"><span data-stu-id="124bb-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="124bb-133">`@model`Dyrektywa określa typ modelu przekazaną do :::no-loc(Razor)::: strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-133">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-134">W poprzednim przykładzie `@model` wiersz powoduje, że `PageModel` Klasa pochodna jest dostępna dla :::no-loc(Razor)::: strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-135">Model jest używany w `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocnikach HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) i na stronie.</span><span class="sxs-lookup"><span data-stu-id="124bb-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="124bb-136">Strona układu</span><span class="sxs-lookup"><span data-stu-id="124bb-136">The layout page</span></span>

<span data-ttu-id="124bb-137">Wybierz linki menu ( **:::no-loc(Razor)::: PagesMovie** , **Home** i **privacy** ).</span><span class="sxs-lookup"><span data-stu-id="124bb-137">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="124bb-138">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="124bb-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="124bb-139">Układ menu jest implementowany w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="124bb-140">Otwórz plik *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="124bb-141">Szablony [układów](xref:mvc/views/layout) umożliwiają układ kontenera HTML:</span><span class="sxs-lookup"><span data-stu-id="124bb-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="124bb-142">Określone w jednym miejscu.</span><span class="sxs-lookup"><span data-stu-id="124bb-142">Specified in one place.</span></span>
* <span data-ttu-id="124bb-143">Stosowane na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="124bb-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="124bb-144">Znajdź `@RenderBody()` wiersz.</span><span class="sxs-lookup"><span data-stu-id="124bb-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="124bb-145">`RenderBody` jest symbolem zastępczym, w którym wszystkie widoki specyficzne dla strony są wyświetlane, *opakowane* na stronie układ.</span><span class="sxs-lookup"><span data-stu-id="124bb-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="124bb-146">Na przykład wybierz łącze **prywatność** i widok *strony/prywatność. cshtml* jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="124bb-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="124bb-147">ViewData i układ</span><span class="sxs-lookup"><span data-stu-id="124bb-147">ViewData and layout</span></span>

<span data-ttu-id="124bb-148">Rozważ następujące oznakowanie w pliku *Pages/Films/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="124bb-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="124bb-149">Poprzedni wyróżniony znacznik jest przykładem :::no-loc(Razor)::: przejścia do języka C#.</span><span class="sxs-lookup"><span data-stu-id="124bb-149">The preceding highlighted markup is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="124bb-150">`{`Znaki i `}` należy ująć w blok kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="124bb-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="124bb-151">`PageModel`Klasa bazowa zawiera `ViewData` Właściwość słownika, która może służyć do przekazywania danych do widoku.</span><span class="sxs-lookup"><span data-stu-id="124bb-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="124bb-152">Obiekty są dodawane do `ViewData` słownika przy użyciu wzorca klucz/wartość.</span><span class="sxs-lookup"><span data-stu-id="124bb-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="124bb-153">W poprzednim przykładzie `"Title"` Właściwość jest dodawana do `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="124bb-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="124bb-154">`"Title"`Właściwość jest używana w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="124bb-155">Poniższy znacznik pokazuje pierwsze kilka wierszy pliku *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="124bb-156">Wiersz `@*Markup removed for brevity.*@` jest :::no-loc(Razor)::: komentarzem.</span><span class="sxs-lookup"><span data-stu-id="124bb-156">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="124bb-157">W przeciwieństwie do komentarzy HTML ( `<!-- -->` ), :::no-loc(Razor)::: Komentarze nie są wysyłane do klienta.</span><span class="sxs-lookup"><span data-stu-id="124bb-157">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="124bb-158">Aktualizowanie układu</span><span class="sxs-lookup"><span data-stu-id="124bb-158">Update the layout</span></span>

<span data-ttu-id="124bb-159">Zmień `<title>` element w pliku *Pages/Shared/_Layout. cshtml* , aby wyświetlał **film** zamiast **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="124bb-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie** .</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="124bb-160">Znajdź następujący element zakotwiczony w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="124bb-161">Zastąp poprzedni element następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="124bb-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="124bb-162">Poprzedni element zakotwiczenia jest [pomocnikiem tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="124bb-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="124bb-163">W tym przypadku jest to [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="124bb-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="124bb-164">`asp-page="/Movies/Index"`Atrybut i wartość pomocnika tagów tworzą link do `/Movies/Index` :::no-loc(Razor)::: strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-165">`asp-area`Wartość atrybutu jest pusta, dlatego obszar nie jest używany w łączu.</span><span class="sxs-lookup"><span data-stu-id="124bb-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="124bb-166">Aby uzyskać więcej informacji, zobacz [obszary](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="124bb-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="124bb-167">Zapisz zmiany i przetestuj aplikację, klikając łącze **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="124bb-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="124bb-168">Jeśli występują problemy, zobacz plik [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="124bb-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="124bb-169">Przetestuj inne linki ( **Narzędzia główne** , **RpMovie** , **Utwórz** , **Edytuj** i **Usuń** ).</span><span class="sxs-lookup"><span data-stu-id="124bb-169">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="124bb-170">Każda Strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Po utworzeniu zakładki na stronie tytuł jest używany dla zakładki.</span><span class="sxs-lookup"><span data-stu-id="124bb-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="124bb-171">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="124bb-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="124bb-172">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i nieUS-Englishych formatów daty, należy wykonać kroki w celu globalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="124bb-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="124bb-173">Aby uzyskać instrukcje dotyczące dodawania przecinków dziesiętnych, zobacz ten problem w usłudze [GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .</span><span class="sxs-lookup"><span data-stu-id="124bb-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="124bb-174">`Layout`Właściwość jest ustawiana w pliku *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="124bb-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="124bb-175">Poprzedzające znaczniki ustawiają plik układu na *Pages/Shared/_Layout. cshtml* dla wszystkich :::no-loc(Razor)::: plików w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="124bb-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="124bb-176">Aby uzyskać więcej informacji, zobacz [Układ](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="124bb-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="124bb-177">Model tworzenia strony</span><span class="sxs-lookup"><span data-stu-id="124bb-177">The Create page model</span></span>

<span data-ttu-id="124bb-178">Obejrzyj model stron */filmów/Utwórz. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="124bb-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="124bb-179">`OnGet`Metoda inicjuje wszystkie Stany potrzebne dla strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="124bb-180">Strona tworzenia nie ma żadnego stanu do zainicjowania, więc `Page` jest zwracana.</span><span class="sxs-lookup"><span data-stu-id="124bb-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="124bb-181">W dalszej części tego samouczka `OnGet` przedstawiono przykład inicjowania stanu.</span><span class="sxs-lookup"><span data-stu-id="124bb-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="124bb-182">`Page`Metoda tworzy `PageResult` obiekt, który renderuje stronę *Create. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="124bb-183">`Movie`Właściwość używa atrybutu, `[BindProperty]` Aby wybrać [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="124bb-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="124bb-184">Gdy formularz tworzenia zapisuje wartości formularza, środowisko uruchomieniowe ASP.NET Core tworzy powiązanie opublikowanych wartości z `Movie` modelem.</span><span class="sxs-lookup"><span data-stu-id="124bb-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="124bb-185">`OnPostAsync`Metoda jest uruchamiana, gdy strona zapisuje dane formularza:</span><span class="sxs-lookup"><span data-stu-id="124bb-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="124bb-186">Jeśli występują jakieś błędy modelu, formularz jest ponownie wyświetlany wraz z wszelkimi opublikowanymi danymi formularza.</span><span class="sxs-lookup"><span data-stu-id="124bb-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="124bb-187">Większość błędów modelu można przechwycić po stronie klienta przed opublikowaniem formularza.</span><span class="sxs-lookup"><span data-stu-id="124bb-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="124bb-188">Przykładem błędu modelu jest opublikowanie wartości pola daty, którego nie można przekonwertować na datę.</span><span class="sxs-lookup"><span data-stu-id="124bb-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="124bb-189">Weryfikowanie po stronie klienta i sprawdzanie poprawności modelu zostały omówione w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="124bb-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="124bb-190">Jeśli nie ma żadnych błędów modelu, dane zostaną zapisane i przeglądarka zostanie przekierowana na stronę indeksu.</span><span class="sxs-lookup"><span data-stu-id="124bb-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="124bb-191">Strona tworzenie :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="124bb-191">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="124bb-192">Przejrzyj strony */filmy/Utwórz* :::no-loc(Razor)::: plik stronicowania. cshtml:</span><span class="sxs-lookup"><span data-stu-id="124bb-192">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="124bb-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="124bb-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="124bb-194">Program Visual Studio wyświetla następujące znaczniki w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="124bb-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Widok VS17 na stronie Tworzenie. cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="124bb-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="124bb-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="124bb-197">Następujące pomocnicy tagów są pokazane w powyższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="124bb-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="124bb-198">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="124bb-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="124bb-199">Program Visual Studio wyświetla następujące znaczniki w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="124bb-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="124bb-200">`<form method="post">`Element jest [pomocnikiem tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="124bb-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="124bb-201">Pomocnik tagu formularza automatycznie zawiera [token antysfałszowany](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="124bb-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="124bb-202">Aparat szkieletu tworzy :::no-loc(Razor)::: znaczniki dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="124bb-202">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="124bb-203">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` i `<span asp-validation-for` ) wyświetla błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="124bb-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="124bb-204">Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.</span><span class="sxs-lookup"><span data-stu-id="124bb-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="124bb-205">[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) generuje podpis etykiety i `for` atrybut `Title` właściwości.</span><span class="sxs-lookup"><span data-stu-id="124bb-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="124bb-206">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="124bb-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="124bb-207">Aby uzyskać więcej informacji na temat pomocników tagów `<form method="post">` , takich jak, zobacz [pomocnicy tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="124bb-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="124bb-208">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="124bb-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="124bb-209">[Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model) 
>  [Dalej: baza danych](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="124bb-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="124bb-210">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="124bb-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="124bb-211">Ten samouczek służy do badania :::no-loc(Razor)::: stron utworzonych przez tworzenie szkieletów w [poprzednim samouczku](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="124bb-211">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="124bb-212">[Wyświetl lub Pobierz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) przykład.</span><span class="sxs-lookup"><span data-stu-id="124bb-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="124bb-213">Strony tworzenie, usuwanie, szczegóły i edycja</span><span class="sxs-lookup"><span data-stu-id="124bb-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="124bb-214">Zapoznaj się z modelem stron */filmów/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="124bb-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="124bb-215">:::no-loc(Razor)::: Strony pochodzą od `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="124bb-215">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="124bb-216">Zgodnie z Konwencją `PageModel` Klasa pochodna jest wywoływana `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="124bb-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="124bb-217">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) , aby dodać `:::no-loc(Razor):::PagesMovieContext` do strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="124bb-218">Wszystkie strony szkieletowe są zgodne z tym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="124bb-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="124bb-219">Zobacz [kod asynchroniczny](xref:data/ef-rp/intro#asynchronous-code) , aby uzyskać więcej informacji na temat programowania asynchronicznego przy użyciu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="124bb-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="124bb-220">Gdy żądanie zostanie wykonane na stronie, `OnGetAsync` Metoda zwraca listę filmów na :::no-loc(Razor)::: stronie.</span><span class="sxs-lookup"><span data-stu-id="124bb-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-221">`OnGetAsync` lub `OnGet` jest wywoływana na :::no-loc(Razor)::: stronie w celu zainicjowania stanu dla strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-221">`OnGetAsync` or `OnGet` is called on a :::no-loc(Razor)::: Page to initialize the state for the page.</span></span> <span data-ttu-id="124bb-222">W takim przypadku `OnGetAsync` Pobiera listę filmów i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="124bb-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="124bb-223">Gdy `OnGet` zwraca `void` lub `OnGetAsync` zwraca `Task` , nie jest używana żadna metoda Return.</span><span class="sxs-lookup"><span data-stu-id="124bb-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="124bb-224">Gdy typem zwracanym jest `IActionResult` lub `Task<IActionResult>` , należy podać instrukcję return.</span><span class="sxs-lookup"><span data-stu-id="124bb-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="124bb-225">Na przykład: *Pages/Films/Create. cshtml. cs.* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="124bb-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="124bb-226">Zapoznaj się ze stroną *stron/filmów/index. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="124bb-226">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="124bb-227">:::no-loc(Razor)::: może przechodzić z kodu HTML do języka C# lub do :::no-loc(Razor)::: określonych znaczników.</span><span class="sxs-lookup"><span data-stu-id="124bb-227">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="124bb-228">Gdy `@` po symbolu następuje [ :::no-loc(Razor)::: zastrzeżone słowo kluczowe](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do :::no-loc(Razor)::: specyficznego znacznika, w przeciwnym razie przechodzi do języka C#.</span><span class="sxs-lookup"><span data-stu-id="124bb-228">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="124bb-229">`@page` :::no-loc(Razor)::: Dyrektywa powoduje, że plik jest akcją MVC, co oznacza, że może obsługiwać żądania.</span><span class="sxs-lookup"><span data-stu-id="124bb-229">The `@page` :::no-loc(Razor)::: directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="124bb-230">`@page` musi być pierwszą :::no-loc(Razor)::: dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="124bb-230">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="124bb-231">`@page` jest przykładem przejścia do :::no-loc(Razor)::: specyficznego znacznika.</span><span class="sxs-lookup"><span data-stu-id="124bb-231">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="124bb-232">Aby uzyskać więcej informacji, zobacz [ :::no-loc(Razor)::: składnia](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="124bb-232">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="124bb-233">Bada wyrażenie lambda użyte w następującym Pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="124bb-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="124bb-234">`DisplayNameFor`Pomocnik html sprawdza właściwość, do `Title` której istnieje odwołanie w wyrażeniu lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="124bb-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="124bb-235">Wyrażenie lambda jest sprawdzane, a nie oceniane.</span><span class="sxs-lookup"><span data-stu-id="124bb-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="124bb-236">Oznacza to, że nie ma żadnych naruszeń dostępu `model` , gdy, `model.Movie` , lub `model.Movie[0]` są `null` puste.</span><span class="sxs-lookup"><span data-stu-id="124bb-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="124bb-237">Gdy wyrażenie lambda jest oceniane (na przykład z `@Html.DisplayFor(modelItem => item.Title)` ), wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="124bb-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="124bb-238">@modelDyrektywa</span><span class="sxs-lookup"><span data-stu-id="124bb-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="124bb-239">`@model`Dyrektywa określa typ modelu przekazaną do :::no-loc(Razor)::: strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-239">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-240">W poprzednim przykładzie `@model` wiersz powoduje, że `PageModel` Klasa pochodna jest dostępna dla :::no-loc(Razor)::: strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-241">Model jest używany w `@Html.DisplayNameFor` `@Html.DisplayFor` [pomocnikach HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) i na stronie.</span><span class="sxs-lookup"><span data-stu-id="124bb-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="124bb-242">Strona układu</span><span class="sxs-lookup"><span data-stu-id="124bb-242">The layout page</span></span>

<span data-ttu-id="124bb-243">Wybierz linki menu ( **:::no-loc(Razor)::: PagesMovie** , **Home** i **privacy** ).</span><span class="sxs-lookup"><span data-stu-id="124bb-243">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="124bb-244">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="124bb-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="124bb-245">Układ menu jest implementowany w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="124bb-246">Otwórz plik *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="124bb-247">Szablony [układów](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="124bb-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="124bb-248">Znajdź `@RenderBody()` wiersz.</span><span class="sxs-lookup"><span data-stu-id="124bb-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="124bb-249">`RenderBody` jest symbolem zastępczym, w którym wszystkie utworzone widoki związane ze stroną są wyświetlane, *opakowane* na stronie układ.</span><span class="sxs-lookup"><span data-stu-id="124bb-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="124bb-250">Na przykład w przypadku wybrania linku **prywatność** widok **strony/prywatność. cshtml** jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="124bb-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="124bb-251">ViewData i układ</span><span class="sxs-lookup"><span data-stu-id="124bb-251">ViewData and layout</span></span>

<span data-ttu-id="124bb-252">Rozważmy następujący kod z pliku *Pages/Films/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="124bb-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="124bb-253">Poprzedni wyróżniony kod jest przykładem :::no-loc(Razor)::: przejścia do języka C#.</span><span class="sxs-lookup"><span data-stu-id="124bb-253">The preceding highlighted code is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="124bb-254">`{`Znaki i `}` należy ująć w blok kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="124bb-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="124bb-255">`PageModel`Klasa bazowa ma `ViewData` Właściwość dictionary, która może służyć do dodawania danych, które mają zostać przekazane do widoku.</span><span class="sxs-lookup"><span data-stu-id="124bb-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="124bb-256">Obiekty można dodawać do `ViewData` słownika przy użyciu wzorca klucz/wartość.</span><span class="sxs-lookup"><span data-stu-id="124bb-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="124bb-257">W poprzednim przykładzie właściwość "title" została dodana do `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="124bb-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="124bb-258">Właściwość "title" jest używana w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="124bb-259">Poniższy znacznik pokazuje pierwsze kilka wierszy pliku *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="124bb-260">Wiersz `@*Markup removed for brevity.*@` jest :::no-loc(Razor)::: komentarzem, które nie pojawia się w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="124bb-260">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="124bb-261">W przeciwieństwie do komentarzy HTML ( `<!-- -->` ), :::no-loc(Razor)::: Komentarze nie są wysyłane do klienta.</span><span class="sxs-lookup"><span data-stu-id="124bb-261">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="124bb-262">Aktualizowanie układu</span><span class="sxs-lookup"><span data-stu-id="124bb-262">Update the layout</span></span>

<span data-ttu-id="124bb-263">Zmień `<title>` element w pliku *Pages/Shared/_Layout. cshtml* , aby wyświetlał **film** zamiast **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="124bb-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie** .</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="124bb-264">Znajdź następujący element zakotwiczony w pliku *Pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="124bb-265">Zastąp poprzedni element następującym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="124bb-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="124bb-266">Poprzedni element zakotwiczenia jest [pomocnikiem tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="124bb-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="124bb-267">W tym przypadku jest to [pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="124bb-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="124bb-268">`asp-page="/Movies/Index"`Atrybut i wartość pomocnika tagów tworzą link do `/Movies/Index` :::no-loc(Razor)::: strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="124bb-269">`asp-area`Wartość atrybutu jest pusta, dlatego obszar nie jest używany w łączu.</span><span class="sxs-lookup"><span data-stu-id="124bb-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="124bb-270">Aby uzyskać więcej informacji, zobacz [obszary](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="124bb-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="124bb-271">Zapisz zmiany i przetestuj aplikację, klikając łącze **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="124bb-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="124bb-272">Jeśli występują problemy, zobacz plik [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="124bb-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="124bb-273">Przetestuj inne linki ( **Narzędzia główne** , **RpMovie** , **Utwórz** , **Edytuj** i **Usuń** ).</span><span class="sxs-lookup"><span data-stu-id="124bb-273">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="124bb-274">Każda Strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Po utworzeniu zakładki na stronie tytuł jest używany dla zakładki.</span><span class="sxs-lookup"><span data-stu-id="124bb-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="124bb-275">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="124bb-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="124bb-276">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i nieUS-Englishych formatów daty, należy wykonać kroki w celu globalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="124bb-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="124bb-277">Ten [problem w usłudze GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) zawiera instrukcje dotyczące dodawania przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="124bb-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="124bb-278">`Layout`Właściwość jest ustawiana w pliku *Pages/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="124bb-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="124bb-279">Poprzedzające znaczniki ustawiają plik układu na *Pages/Shared/_Layout. cshtml* dla wszystkich :::no-loc(Razor)::: plików w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="124bb-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="124bb-280">Aby uzyskać więcej informacji, zobacz [Układ](xref:razor-pages/index#layout) .</span><span class="sxs-lookup"><span data-stu-id="124bb-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="124bb-281">Model tworzenia strony</span><span class="sxs-lookup"><span data-stu-id="124bb-281">The Create page model</span></span>

<span data-ttu-id="124bb-282">Obejrzyj model stron */filmów/Utwórz. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="124bb-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="124bb-283">`OnGet`Metoda inicjuje wszystkie Stany potrzebne dla strony.</span><span class="sxs-lookup"><span data-stu-id="124bb-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="124bb-284">Strona tworzenia nie ma żadnego stanu do zainicjowania, więc `Page` jest zwracana.</span><span class="sxs-lookup"><span data-stu-id="124bb-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="124bb-285">W dalszej części tego samouczka zobaczysz `OnGet` stan zainicjowania metody.</span><span class="sxs-lookup"><span data-stu-id="124bb-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="124bb-286">`Page`Metoda tworzy `PageResult` obiekt, który renderuje stronę *Create. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="124bb-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="124bb-287">`Movie`Właściwość używa atrybutu, `[BindProperty]` Aby wybrać [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="124bb-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="124bb-288">Gdy formularz tworzenia zapisuje wartości formularza, środowisko uruchomieniowe ASP.NET Core tworzy powiązanie opublikowanych wartości z `Movie` modelem.</span><span class="sxs-lookup"><span data-stu-id="124bb-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="124bb-289">`OnPostAsync`Metoda jest uruchamiana, gdy strona zapisuje dane formularza:</span><span class="sxs-lookup"><span data-stu-id="124bb-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="124bb-290">Jeśli występują jakieś błędy modelu, formularz jest ponownie wyświetlany wraz z wszelkimi opublikowanymi danymi formularza.</span><span class="sxs-lookup"><span data-stu-id="124bb-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="124bb-291">Większość błędów modelu można przechwycić po stronie klienta przed opublikowaniem formularza.</span><span class="sxs-lookup"><span data-stu-id="124bb-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="124bb-292">Przykładem błędu modelu jest opublikowanie wartości pola daty, którego nie można przekonwertować na datę.</span><span class="sxs-lookup"><span data-stu-id="124bb-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="124bb-293">Weryfikowanie po stronie klienta i sprawdzanie poprawności modelu zostały omówione w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="124bb-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="124bb-294">Jeśli nie ma żadnych błędów modelu, dane zostaną zapisane i przeglądarka zostanie przekierowana na stronę indeksu.</span><span class="sxs-lookup"><span data-stu-id="124bb-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="124bb-295">Strona tworzenie :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="124bb-295">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="124bb-296">Przejrzyj strony */filmy/Utwórz* :::no-loc(Razor)::: plik stronicowania. cshtml:</span><span class="sxs-lookup"><span data-stu-id="124bb-296">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="124bb-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="124bb-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="124bb-298">Program Visual Studio Wyświetla `<form method="post">` tag w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="124bb-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Widok VS17 na stronie Tworzenie. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="124bb-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="124bb-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="124bb-301">Aby uzyskać więcej informacji na temat pomocników tagów `<form method="post">` , takich jak, zobacz [pomocnicy tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="124bb-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="124bb-302">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="124bb-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="124bb-303">Visual Studio dla komputerów Mac wyświetla `<form method="post">` tag w wyróżnionej pogrubionej czcionce używanej przez pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="124bb-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="124bb-304">`<form method="post">`Element jest [pomocnikiem tagu formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="124bb-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="124bb-305">Pomocnik tagu formularza automatycznie zawiera [token antysfałszowany](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="124bb-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="124bb-306">Aparat szkieletu tworzy :::no-loc(Razor)::: znaczniki dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="124bb-306">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="124bb-307">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` i `<span asp-validation-for` ) wyświetla błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="124bb-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="124bb-308">Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.</span><span class="sxs-lookup"><span data-stu-id="124bb-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="124bb-309">[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) generuje podpis etykiety i `for` atrybut `Title` właściwości.</span><span class="sxs-lookup"><span data-stu-id="124bb-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="124bb-310">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="124bb-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="124bb-311">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="124bb-311">Additional resources</span></span>

* [<span data-ttu-id="124bb-312">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="124bb-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="124bb-313">[Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model) 
>  [Dalej: baza danych](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="124bb-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
