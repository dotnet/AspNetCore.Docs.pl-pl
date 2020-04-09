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
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="721cb-103">Rusztowania stron maszynki do golenia w rdzeniu ASP.NET</span><span class="sxs-lookup"><span data-stu-id="721cb-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="721cb-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="721cb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="721cb-105">Ten samouczek analizuje strony Razor utworzone przez rusztowania w [poprzednim samouczku](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="721cb-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="721cb-106">Strony Tworzenie, usuwanie, szczegóły i edytowanie</span><span class="sxs-lookup"><span data-stu-id="721cb-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="721cb-107">Sprawdź model strony *Pages/Movies/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="721cb-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="721cb-108">Strony brzytwy `PageModel`pochodzą od .</span><span class="sxs-lookup"><span data-stu-id="721cb-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="721cb-109">Zgodnie z `PageModel`konwencją klasa pochodna jest wywoływana `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="721cb-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="721cb-110">Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) aby dodać do `RazorPagesMovieContext` strony.</span><span class="sxs-lookup"><span data-stu-id="721cb-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="721cb-111">Wszystkie strony rusztowania postępuj zgodnie z tym wzorem.</span><span class="sxs-lookup"><span data-stu-id="721cb-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="721cb-112">Zobacz [kod asynchroniczne, aby](xref:data/ef-rp/intro#asynchronous-code) uzyskać więcej informacji na temat programowania asynchroniowego z Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="721cb-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="721cb-113">Po nałożeniu żądania strony `OnGetAsync` metoda zwraca listę filmów do strony Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="721cb-114">`OnGetAsync`lub `OnGet` jest wywoływana w celu zainicjowania stanu strony.</span><span class="sxs-lookup"><span data-stu-id="721cb-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="721cb-115">W takim `OnGetAsync` przypadku pobiera listę filmów i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="721cb-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="721cb-116">Gdy `OnGet` `void` zwraca `OnGetAsync` `Task`lub zwraca, nie jest używana instrukcja zwrotu.</span><span class="sxs-lookup"><span data-stu-id="721cb-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="721cb-117">Gdy typ zwracany jest `IActionResult` lub `Task<IActionResult>`, należy podać instrukcję zwrotu.</span><span class="sxs-lookup"><span data-stu-id="721cb-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="721cb-118">Na przykład metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="721cb-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="721cb-119">Sprawdź *stronę Razor Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="721cb-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="721cb-120">Razor może przejść z HTML do C# lub do znaczników specyficznych dla Razora.</span><span class="sxs-lookup"><span data-stu-id="721cb-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="721cb-121">Gdy `@` po symbolu następuje [słowo kluczowe razor reserved](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do znaczników specyficznych dla razora, w przeciwnym razie przechodzi do języka C#.</span><span class="sxs-lookup"><span data-stu-id="721cb-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="721cb-122">Dyrektywa @page</span><span class="sxs-lookup"><span data-stu-id="721cb-122">The @page directive</span></span>

<span data-ttu-id="721cb-123">Dyrektywa `@page` Razor sprawia, że plik akcji MVC, co oznacza, że może obsługiwać żądania.</span><span class="sxs-lookup"><span data-stu-id="721cb-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="721cb-124">`@page`musi być pierwszą dyrektywą Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="721cb-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="721cb-125">`@page`jest przykładem przejścia do znaczników specyficznych dla razora.</span><span class="sxs-lookup"><span data-stu-id="721cb-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="721cb-126">Aby uzyskać więcej informacji, zobacz [składnia maszynki do golenia.](xref:mvc/views/razor#razor-syntax)</span><span class="sxs-lookup"><span data-stu-id="721cb-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="721cb-127">Sprawdź wyrażenie lambda używane w następującym pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="721cb-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="721cb-128">Pomocnik `DisplayNameFor` HTML sprawdza właściwość, `Title` do którego odwołuje się wyrażenie lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="721cb-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="721cb-129">Wyrażenie lambda jest kontrolowane, a nie oceniane.</span><span class="sxs-lookup"><span data-stu-id="721cb-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="721cb-130">Oznacza to, że nie `model` `model.Movie`ma `model.Movie[0]` naruszenia `null` dostępu, gdy , lub jest lub puste.</span><span class="sxs-lookup"><span data-stu-id="721cb-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="721cb-131">Gdy wyrażenie lambda jest oceniane (na przykład z), `@Html.DisplayFor(modelItem => item.Title)`wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="721cb-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="721cb-132">Dyrektywa @model</span><span class="sxs-lookup"><span data-stu-id="721cb-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="721cb-133">Dyrektywa `@model` określa typ modelu przekazywane do Razor Page.</span><span class="sxs-lookup"><span data-stu-id="721cb-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="721cb-134">W poprzednim przykładzie `@model` wiersz udostępnia `PageModel`klasę pochodną do strony Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="721cb-135">Model jest używany `@Html.DisplayNameFor` w `@Html.DisplayFor` pomocnikach [HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stronie.</span><span class="sxs-lookup"><span data-stu-id="721cb-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="721cb-136">Strona układu</span><span class="sxs-lookup"><span data-stu-id="721cb-136">The layout page</span></span>

<span data-ttu-id="721cb-137">Wybierz linki menu **(RazorPagesMovie**, **Home**i **Prywatność).**</span><span class="sxs-lookup"><span data-stu-id="721cb-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="721cb-138">Każda strona zawiera ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="721cb-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="721cb-139">Układ menu jest zaimplementowany w pliku *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="721cb-140">Otwórz plik *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="721cb-141">[Szablony układu](xref:mvc/views/layout) umożliwiają układ kontenera HTML:</span><span class="sxs-lookup"><span data-stu-id="721cb-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="721cb-142">Określone w jednym miejscu.</span><span class="sxs-lookup"><span data-stu-id="721cb-142">Specified in one place.</span></span>
* <span data-ttu-id="721cb-143">Stosowane na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="721cb-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="721cb-144">Znajdź `@RenderBody()` linię.</span><span class="sxs-lookup"><span data-stu-id="721cb-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="721cb-145">`RenderBody`jest symbolem zastępczym, w którym wyświetlane są wszystkie widoki specyficzne dla strony, *zawinięte* w stronę układu.</span><span class="sxs-lookup"><span data-stu-id="721cb-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="721cb-146">Na przykład wybierz łącze **Prywatność,** a widok *Pages/Privacy.cshtml* jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="721cb-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="721cb-147">ViewData i układ</span><span class="sxs-lookup"><span data-stu-id="721cb-147">ViewData and layout</span></span>

<span data-ttu-id="721cb-148">Należy wziąć pod uwagę następujące znaczniki z pliku *Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="721cb-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="721cb-149">Poprzedni wyróżniony znaczników jest przykładem Razor przejście do języka C#.</span><span class="sxs-lookup"><span data-stu-id="721cb-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="721cb-150">Znaki `{` `}` i ująć blok kodu C#.</span><span class="sxs-lookup"><span data-stu-id="721cb-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="721cb-151">Klasa `PageModel` podstawowa `ViewData` zawiera właściwość słownika, która może służyć do przekazywania danych do View.</span><span class="sxs-lookup"><span data-stu-id="721cb-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="721cb-152">Obiekty są dodawane do słownika `ViewData` przy użyciu wzorca klucza/wartości.</span><span class="sxs-lookup"><span data-stu-id="721cb-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="721cb-153">W poprzednim przykładzie `"Title"` właściwość jest `ViewData` dodawany do słownika.</span><span class="sxs-lookup"><span data-stu-id="721cb-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="721cb-154">Właściwość `"Title"` jest używana w pliku *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="721cb-155">Poniższy znacznik pokazuje kilka pierwszych wierszy pliku *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="721cb-156">Linia `@*Markup removed for brevity.*@` jest komentarz Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="721cb-157">W przeciwieństwie`<!-- -->`do komentarzy HTML ( ), komentarze Razor nie są wysyłane do klienta.</span><span class="sxs-lookup"><span data-stu-id="721cb-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="721cb-158">Aktualizowanie układu</span><span class="sxs-lookup"><span data-stu-id="721cb-158">Update the layout</span></span>

<span data-ttu-id="721cb-159">Zmień `<title>` element w pliku *Pages/Shared/_Layout.cshtml,* aby wyświetlić **film,** a nie **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="721cb-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="721cb-160">Znajdź następujący element zakotwiczenia w pliku *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="721cb-161">Zastąp poprzedni element następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="721cb-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="721cb-162">Poprzednim elementem zakotwiczenia jest [Pomocnik znaczników](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="721cb-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="721cb-163">W tym przypadku jest to [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="721cb-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="721cb-164">Atrybut `asp-page="/Movies/Index"` i wartość Pomocnika znaczników tworzy `/Movies/Index` łącze do strony Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="721cb-165">Wartość `asp-area` atrybutu jest pusta, więc obszar nie jest używany w łączu.</span><span class="sxs-lookup"><span data-stu-id="721cb-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="721cb-166">Aby uzyskać więcej informacji, zobacz [Obszary.](xref:mvc/controllers/areas)</span><span class="sxs-lookup"><span data-stu-id="721cb-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="721cb-167">Zapisz zmiany i przetestuj aplikację, klikając link **RpMovie.**</span><span class="sxs-lookup"><span data-stu-id="721cb-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="721cb-168">Zobacz [plik _Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) w GitHub, jeśli masz jakiekolwiek problemy.</span><span class="sxs-lookup"><span data-stu-id="721cb-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="721cb-169">Testowanie innych łączy (**Strona główna**, **RpMovie**, **Tworzenie**, **Edycja**i **Usuń**).</span><span class="sxs-lookup"><span data-stu-id="721cb-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="721cb-170">Każda strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Podczas doszkiowania strony tytuł jest używany dla zakładki.</span><span class="sxs-lookup"><span data-stu-id="721cb-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="721cb-171">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="721cb-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="721cb-172">Aby obsługiwać [sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i formatów daty spoza języka AMERYKAŃSKIEgo, należy podjąć kroki w celu zglobalizowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="721cb-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="721cb-173">Zobacz ten [problem GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) instrukcje dotyczące dodawania przecinka dziesiętnego.</span><span class="sxs-lookup"><span data-stu-id="721cb-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="721cb-174">Właściwość `Layout` jest ustawiona w pliku *Pages/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="721cb-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="721cb-175">Poprzedni znacznik ustawia plik układu na *Pages/Shared/_Layout.cshtml* dla wszystkich plików Razor w folderze *Pages.*</span><span class="sxs-lookup"><span data-stu-id="721cb-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="721cb-176">Aby uzyskać więcej informacji, zobacz [Układ.](xref:razor-pages/index#layout)</span><span class="sxs-lookup"><span data-stu-id="721cb-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="721cb-177">Model strony Tworzenie</span><span class="sxs-lookup"><span data-stu-id="721cb-177">The Create page model</span></span>

<span data-ttu-id="721cb-178">Sprawdź model strony *Pages/Movies/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="721cb-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="721cb-179">Metoda `OnGet` inicjuje dowolny stan potrzebny dla strony.</span><span class="sxs-lookup"><span data-stu-id="721cb-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="721cb-180">Strona Utwórz nie ma żadnego stanu `Page` do zainicjowania, więc jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="721cb-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="721cb-181">W dalszej części samouczka pokazano przykład inicjowania `OnGet` stanu.</span><span class="sxs-lookup"><span data-stu-id="721cb-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="721cb-182">Metoda `Page` tworzy `PageResult` obiekt, który renderuje stronę *Create.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="721cb-183">Właściwość `Movie` używa `[BindProperty]` atrybutu, aby wyrazić zgodę na [powiązanie modelu.](xref:mvc/models/model-binding)</span><span class="sxs-lookup"><span data-stu-id="721cb-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="721cb-184">Gdy formularz Utwórz księguje wartości formularza, środowisko uruchomieniowe ASP.NET Core `Movie` wiąże zaksięgowane wartości z modelem.</span><span class="sxs-lookup"><span data-stu-id="721cb-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="721cb-185">Metoda `OnPostAsync` jest uruchamiana, gdy dane formularza wpisów strony:</span><span class="sxs-lookup"><span data-stu-id="721cb-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="721cb-186">Jeśli występują błędy modelu, formularz jest ponownie wyeksponowany wraz z wszelkimi opublikowanymi danymi formularza.</span><span class="sxs-lookup"><span data-stu-id="721cb-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="721cb-187">Większość błędów modelu można złapać po stronie klienta przed zaksięgowania formularza.</span><span class="sxs-lookup"><span data-stu-id="721cb-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="721cb-188">Przykładem błędu modelu jest księgowanie wartości pola daty, które nie może zostać przekonwertowane na datę.</span><span class="sxs-lookup"><span data-stu-id="721cb-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="721cb-189">Sprawdzanie poprawności po stronie klienta i sprawdzanie poprawności modelu są omówione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="721cb-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="721cb-190">Jeśli nie ma żadnych błędów modelu, dane są zapisywane, a przeglądarka jest przekierowywane do strony Indeks.</span><span class="sxs-lookup"><span data-stu-id="721cb-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="721cb-191">Strona Tworzenie maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="721cb-191">The Create Razor Page</span></span>

<span data-ttu-id="721cb-192">Sprawdź plik *strony/filmy/utwórz.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="721cb-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="721cb-193">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="721cb-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="721cb-194">Program Visual Studio wyświetla następujące znaczniki w charakterystycznej pogrubionej czcionce używanej dla pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="721cb-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Widok vs17 strony Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="721cb-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="721cb-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="721cb-197">W poprzednim znaczniku pokazano następujące pomocników znaczników:</span><span class="sxs-lookup"><span data-stu-id="721cb-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="721cb-198">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="721cb-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="721cb-199">Program Visual Studio wyświetla następujące znaczniki w charakterystycznej pogrubionej czcionce używanej dla pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="721cb-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="721cb-200">Element `<form method="post">` jest [pomocnikiem znaczników formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="721cb-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="721cb-201">Pomocnik znacznika formularza automatycznie zawiera [token antyforgery.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="721cb-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="721cb-202">Aparat rusztowań tworzy znaczniki Razor dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="721cb-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="721cb-203">[Pomocnicy znaczników](xref:mvc/views/working-with-forms#the-validation-tag-helpers) `<div asp-validation-summary` sprawdzania `<span asp-validation-for`poprawności (i) wyświetlają błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="721cb-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="721cb-204">Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.</span><span class="sxs-lookup"><span data-stu-id="721cb-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="721cb-205">[Pomocnik tagu](xref:mvc/views/working-with-forms#the-label-tag-helper) etykiety`<label asp-for="Movie.Title" class="control-label"></label>`( ) generuje `for` podpis etykiety `Title` i atrybut właściwości.</span><span class="sxs-lookup"><span data-stu-id="721cb-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="721cb-206">[Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="721cb-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="721cb-207">Aby uzyskać więcej informacji na `<form method="post">`temat pomocników tagów, takich jak , zobacz [Pomocników tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="721cb-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="721cb-208">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="721cb-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="721cb-209">[Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model)
> [Dalej: Baza danych](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="721cb-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="721cb-210">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="721cb-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="721cb-211">Ten samouczek analizuje strony Razor utworzone przez rusztowania w [poprzednim samouczku](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="721cb-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="721cb-212">[Wyświetl lub pobierz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) próbkę.</span><span class="sxs-lookup"><span data-stu-id="721cb-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="721cb-213">Strony Tworzenie, usuwanie, szczegóły i edytowanie</span><span class="sxs-lookup"><span data-stu-id="721cb-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="721cb-214">Sprawdź model strony *Pages/Movies/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="721cb-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="721cb-215">Strony brzytwy `PageModel`pochodzą od .</span><span class="sxs-lookup"><span data-stu-id="721cb-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="721cb-216">Zgodnie z `PageModel`konwencją klasa pochodna jest wywoływana `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="721cb-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="721cb-217">Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) aby dodać do `RazorPagesMovieContext` strony.</span><span class="sxs-lookup"><span data-stu-id="721cb-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="721cb-218">Wszystkie strony rusztowania postępuj zgodnie z tym wzorem.</span><span class="sxs-lookup"><span data-stu-id="721cb-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="721cb-219">Zobacz [kod asynchroniczne, aby](xref:data/ef-rp/intro#asynchronous-code) uzyskać więcej informacji na temat programowania asynchroniowego z Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="721cb-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="721cb-220">Po nałożeniu żądania strony `OnGetAsync` metoda zwraca listę filmów do strony Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="721cb-221">`OnGetAsync`lub `OnGet` jest wywoływana na stronie Razor, aby zainicjować stan strony.</span><span class="sxs-lookup"><span data-stu-id="721cb-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="721cb-222">W takim `OnGetAsync` przypadku pobiera listę filmów i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="721cb-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="721cb-223">W `OnGet` `void` przypadku `OnGetAsync` `Task`zwracania lub zwracania nie jest używana metoda zwrotu.</span><span class="sxs-lookup"><span data-stu-id="721cb-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="721cb-224">Gdy typ zwracany jest `IActionResult` lub `Task<IActionResult>`, należy podać instrukcję zwrotu.</span><span class="sxs-lookup"><span data-stu-id="721cb-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="721cb-225">Na przykład metoda *Pages/Movies/Create.cshtml.cs:* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="721cb-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="721cb-226">Sprawdź *stronę Razor Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="721cb-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="721cb-227">Razor może przejść z HTML do C# lub do znaczników specyficznych dla Razora.</span><span class="sxs-lookup"><span data-stu-id="721cb-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="721cb-228">Gdy `@` po symbolu następuje [słowo kluczowe razor reserved](xref:mvc/views/razor#razor-reserved-keywords), przechodzi do znaczników specyficznych dla razora, w przeciwnym razie przechodzi do języka C#.</span><span class="sxs-lookup"><span data-stu-id="721cb-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="721cb-229">Dyrektywa `@page` Razor sprawia, że plik do akcji MVC, co oznacza, że może obsługiwać żądania.</span><span class="sxs-lookup"><span data-stu-id="721cb-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="721cb-230">`@page`musi być pierwszą dyrektywą Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="721cb-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="721cb-231">`@page`jest przykładem przejścia do znaczników specyficznych dla razora.</span><span class="sxs-lookup"><span data-stu-id="721cb-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="721cb-232">Aby uzyskać więcej informacji, zobacz [składnia maszynki do golenia.](xref:mvc/views/razor#razor-syntax)</span><span class="sxs-lookup"><span data-stu-id="721cb-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="721cb-233">Sprawdź wyrażenie lambda używane w następującym pomocniku HTML:</span><span class="sxs-lookup"><span data-stu-id="721cb-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="721cb-234">Pomocnik `DisplayNameFor` HTML sprawdza właściwość, `Title` do którego odwołuje się wyrażenie lambda, aby określić nazwę wyświetlaną.</span><span class="sxs-lookup"><span data-stu-id="721cb-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="721cb-235">Wyrażenie lambda jest kontrolowane, a nie oceniane.</span><span class="sxs-lookup"><span data-stu-id="721cb-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="721cb-236">Oznacza to, że nie `model` `model.Movie`ma `model.Movie[0]` naruszenia `null` dostępu, gdy , lub są lub puste.</span><span class="sxs-lookup"><span data-stu-id="721cb-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="721cb-237">Gdy wyrażenie lambda jest oceniane (na przykład z), `@Html.DisplayFor(modelItem => item.Title)`wartości właściwości modelu są oceniane.</span><span class="sxs-lookup"><span data-stu-id="721cb-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="721cb-238">Dyrektywa @model</span><span class="sxs-lookup"><span data-stu-id="721cb-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="721cb-239">Dyrektywa `@model` określa typ modelu przekazywane do Razor Page.</span><span class="sxs-lookup"><span data-stu-id="721cb-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="721cb-240">W poprzednim przykładzie `@model` wiersz udostępnia `PageModel`klasę pochodną do strony Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="721cb-241">Model jest używany `@Html.DisplayNameFor` w `@Html.DisplayFor` pomocnikach [HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) na stronie.</span><span class="sxs-lookup"><span data-stu-id="721cb-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="721cb-242">Strona układu</span><span class="sxs-lookup"><span data-stu-id="721cb-242">The layout page</span></span>

<span data-ttu-id="721cb-243">Wybierz linki menu **(RazorPagesMovie**, **Home**i **Prywatność).**</span><span class="sxs-lookup"><span data-stu-id="721cb-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="721cb-244">Każda strona zawiera ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="721cb-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="721cb-245">Układ menu jest zaimplementowany w pliku *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="721cb-246">Otwórz plik *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="721cb-247">[Szablony układu](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="721cb-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="721cb-248">Znajdź `@RenderBody()` linię.</span><span class="sxs-lookup"><span data-stu-id="721cb-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="721cb-249">`RenderBody`to symbol zastępczy, w którym wszystkie utworzone widoki specyficzne dla strony są wyświetlane, *zawinięte* na stronie układu.</span><span class="sxs-lookup"><span data-stu-id="721cb-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="721cb-250">Jeśli na przykład wybierzesz łącze **Prywatność,** widok **Pages/Privacy.cshtml** zostanie renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="721cb-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="721cb-251">ViewData i układ</span><span class="sxs-lookup"><span data-stu-id="721cb-251">ViewData and layout</span></span>

<span data-ttu-id="721cb-252">Rozważmy następujący kod z pliku *Pages/Movies/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="721cb-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="721cb-253">Powyższy wyróżniony kod jest przykładem razor przejście do języka C#.</span><span class="sxs-lookup"><span data-stu-id="721cb-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="721cb-254">Znaki `{` `}` i ująć blok kodu C#.</span><span class="sxs-lookup"><span data-stu-id="721cb-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="721cb-255">Klasa `PageModel` podstawowa `ViewData` ma właściwość słownika, która może służyć do dodawania danych, które mają być przekazywać do widoku.</span><span class="sxs-lookup"><span data-stu-id="721cb-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="721cb-256">Obiekty można dodać `ViewData` do słownika przy użyciu wzorca klucza/wartości.</span><span class="sxs-lookup"><span data-stu-id="721cb-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="721cb-257">W poprzednim przykładzie właściwość "Title" jest `ViewData` dodawany do słownika.</span><span class="sxs-lookup"><span data-stu-id="721cb-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="721cb-258">Właściwość "Tytuł" jest używana w pliku *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="721cb-259">Poniższy znacznik pokazuje kilka pierwszych wierszy pliku *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="721cb-260">Linia `@*Markup removed for brevity.*@` jest komentarzem Razor, który nie pojawia się w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="721cb-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="721cb-261">W przeciwieństwie`<!-- -->`do komentarzy HTML ( ), komentarze Razor nie są wysyłane do klienta.</span><span class="sxs-lookup"><span data-stu-id="721cb-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="721cb-262">Aktualizowanie układu</span><span class="sxs-lookup"><span data-stu-id="721cb-262">Update the layout</span></span>

<span data-ttu-id="721cb-263">Zmień `<title>` element w pliku *Pages/Shared/_Layout.cshtml,* aby wyświetlić **film,** a nie **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="721cb-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="721cb-264">Znajdź następujący element zakotwiczenia w pliku *Pages/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="721cb-265">Zastąp poprzedni element następującym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="721cb-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="721cb-266">Poprzednim elementem zakotwiczenia jest [Pomocnik znaczników](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="721cb-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="721cb-267">W tym przypadku jest to [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="721cb-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="721cb-268">Atrybut `asp-page="/Movies/Index"` i wartość Pomocnika znaczników tworzy `/Movies/Index` łącze do strony Razor.</span><span class="sxs-lookup"><span data-stu-id="721cb-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="721cb-269">Wartość `asp-area` atrybutu jest pusta, więc obszar nie jest używany w łączu.</span><span class="sxs-lookup"><span data-stu-id="721cb-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="721cb-270">Aby uzyskać więcej informacji, zobacz [Obszary.](xref:mvc/controllers/areas)</span><span class="sxs-lookup"><span data-stu-id="721cb-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="721cb-271">Zapisz zmiany i przetestuj aplikację, klikając link **RpMovie.**</span><span class="sxs-lookup"><span data-stu-id="721cb-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="721cb-272">Zobacz [plik _Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) w GitHub, jeśli masz jakiekolwiek problemy.</span><span class="sxs-lookup"><span data-stu-id="721cb-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="721cb-273">Testowanie innych łączy (**Strona główna**, **RpMovie**, **Tworzenie**, **Edycja**i **Usuń**).</span><span class="sxs-lookup"><span data-stu-id="721cb-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="721cb-274">Każda strona ustawia tytuł, który można zobaczyć na karcie przeglądarki. Podczas doszkiowania strony tytuł jest używany dla zakładki.</span><span class="sxs-lookup"><span data-stu-id="721cb-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="721cb-275">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="721cb-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="721cb-276">Aby obsługiwać [sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i formatów daty spoza języka AMERYKAŃSKIEgo, należy podjąć kroki w celu zglobalizowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="721cb-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="721cb-277">Ten [problem GitHub 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) instrukcje dotyczące dodawania przecinka dziesiętnego.</span><span class="sxs-lookup"><span data-stu-id="721cb-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="721cb-278">Właściwość `Layout` jest ustawiona w pliku *Pages/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="721cb-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="721cb-279">Poprzedni znacznik ustawia plik układu na *Pages/Shared/_Layout.cshtml* dla wszystkich plików Razor w folderze *Pages.*</span><span class="sxs-lookup"><span data-stu-id="721cb-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="721cb-280">Aby uzyskać więcej informacji, zobacz [Układ.](xref:razor-pages/index#layout)</span><span class="sxs-lookup"><span data-stu-id="721cb-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="721cb-281">Model strony Tworzenie</span><span class="sxs-lookup"><span data-stu-id="721cb-281">The Create page model</span></span>

<span data-ttu-id="721cb-282">Sprawdź model strony *Pages/Movies/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="721cb-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="721cb-283">Metoda `OnGet` inicjuje dowolny stan potrzebny dla strony.</span><span class="sxs-lookup"><span data-stu-id="721cb-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="721cb-284">Strona Utwórz nie ma żadnego stanu `Page` do zainicjowania, więc jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="721cb-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="721cb-285">W dalszej części `OnGet` samouczka zobaczysz, że metoda inicjuje stan.</span><span class="sxs-lookup"><span data-stu-id="721cb-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="721cb-286">Metoda `Page` tworzy `PageResult` obiekt, który renderuje stronę *Create.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="721cb-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="721cb-287">Właściwość `Movie` używa `[BindProperty]` atrybutu, aby wyrazić zgodę na [powiązanie modelu.](xref:mvc/models/model-binding)</span><span class="sxs-lookup"><span data-stu-id="721cb-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="721cb-288">Gdy formularz Utwórz księguje wartości formularza, środowisko uruchomieniowe ASP.NET Core `Movie` wiąże zaksięgowane wartości z modelem.</span><span class="sxs-lookup"><span data-stu-id="721cb-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="721cb-289">Metoda `OnPostAsync` jest uruchamiana, gdy dane formularza wpisów strony:</span><span class="sxs-lookup"><span data-stu-id="721cb-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="721cb-290">Jeśli występują błędy modelu, formularz jest ponownie wyeksponowany wraz z wszelkimi opublikowanymi danymi formularza.</span><span class="sxs-lookup"><span data-stu-id="721cb-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="721cb-291">Większość błędów modelu można złapać po stronie klienta przed zaksięgowania formularza.</span><span class="sxs-lookup"><span data-stu-id="721cb-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="721cb-292">Przykładem błędu modelu jest księgowanie wartości pola daty, które nie może zostać przekonwertowane na datę.</span><span class="sxs-lookup"><span data-stu-id="721cb-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="721cb-293">Sprawdzanie poprawności po stronie klienta i sprawdzanie poprawności modelu są omówione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="721cb-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="721cb-294">Jeśli nie ma żadnych błędów modelu, dane są zapisywane, a przeglądarka jest przekierowywane do strony Indeks.</span><span class="sxs-lookup"><span data-stu-id="721cb-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="721cb-295">Strona Tworzenie maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="721cb-295">The Create Razor Page</span></span>

<span data-ttu-id="721cb-296">Sprawdź plik *strony/filmy/utwórz.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="721cb-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="721cb-297">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="721cb-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="721cb-298">Program Visual `<form method="post">` Studio wyświetla znacznik w charakterystycznej pogrubionej czcionce używanej dla pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="721cb-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Widok vs17 strony Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="721cb-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="721cb-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="721cb-301">Aby uzyskać więcej informacji na `<form method="post">`temat pomocników tagów, takich jak , zobacz [Pomocników tagów w ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="721cb-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="721cb-302">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="721cb-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="721cb-303">Program Visual Studio `<form method="post">` dla komputerów Mac wyświetla znacznik w charakterystycznej pogrubionej czcionce używanej dla pomocników znaczników.</span><span class="sxs-lookup"><span data-stu-id="721cb-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="721cb-304">Element `<form method="post">` jest [pomocnikiem znaczników formularza](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="721cb-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="721cb-305">Pomocnik znacznika formularza automatycznie zawiera [token antyforgery.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="721cb-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="721cb-306">Aparat rusztowań tworzy znaczniki Razor dla każdego pola w modelu (z wyjątkiem identyfikatora) podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="721cb-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="721cb-307">[Pomocnicy znaczników](xref:mvc/views/working-with-forms#the-validation-tag-helpers) `<div asp-validation-summary` sprawdzania `<span asp-validation-for`poprawności (i) wyświetlają błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="721cb-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="721cb-308">Sprawdzanie poprawności jest omówione bardziej szczegółowo w dalszej części tej serii.</span><span class="sxs-lookup"><span data-stu-id="721cb-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="721cb-309">[Pomocnik tagu](xref:mvc/views/working-with-forms#the-label-tag-helper) etykiety`<label asp-for="Movie.Title" class="control-label"></label>`( ) generuje `for` podpis etykiety `Title` i atrybut właściwości.</span><span class="sxs-lookup"><span data-stu-id="721cb-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="721cb-310">[Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="721cb-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="721cb-311">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="721cb-311">Additional resources</span></span>

* [<span data-ttu-id="721cb-312">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="721cb-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="721cb-313">[Poprzedni: Dodawanie modelu](xref:tutorials/razor-pages/model)
> [Dalej: Baza danych](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="721cb-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
