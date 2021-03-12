---
title: Częściowe widoki w ASP.NET Core
author: ardalis
description: Odkryj, jak używać widoków częściowych, aby rozbić duże pliki znaczników i zmniejszyć duplikowanie wspólnego znacznika na stronach sieci Web w aplikacjach ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
no-loc:
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
uid: mvc/views/partial
ms.openlocfilehash: 0a8e4a4fdecd657840c6c02424ffffa64d4ab473
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586881"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="45b23-103">Częściowe widoki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45b23-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="45b23-104">[Steve Kowalski](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="45b23-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="45b23-105">Widok częściowy to [Razor](xref:mvc/views/razor) plik znaczników (*. cshtml*) bez [`@page`](xref:mvc/views/razor#page) dyrektywy, która renderuje dane wyjściowe HTML *w* innym wyrenderowanym danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="45b23-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45b23-106">Termin *częściowy widok* jest używany podczas tworzenia aplikacji MVC, gdzie pliki znaczników są nazywane *widokami*, lub Razor aplikacją Pages, gdzie pliki znaczników są nazywane *stronami*.</span><span class="sxs-lookup"><span data-stu-id="45b23-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="45b23-107">Ten temat ogólnie odnosi się do widoków MVC i stron stron Razor jako *plików znaczników*.</span><span class="sxs-lookup"><span data-stu-id="45b23-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="45b23-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/partial/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="45b23-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="45b23-109">Kiedy używać widoków częściowych</span><span class="sxs-lookup"><span data-stu-id="45b23-109">When to use partial views</span></span>

<span data-ttu-id="45b23-110">Widoki częściowe są efektywnym sposobem:</span><span class="sxs-lookup"><span data-stu-id="45b23-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="45b23-111">Podziel duże pliki znaczników na mniejsze składniki.</span><span class="sxs-lookup"><span data-stu-id="45b23-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="45b23-112">W dużych, złożonych plikach znaczników składających się z kilku elementów logicznych istnieje zaleta pracy z każdą częścią, która jest izolowana do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="45b23-113">Kod w pliku znaczników jest zarządzany, ponieważ znacznik zawiera tylko ogólną strukturę strony i odwołania do widoków częściowych.</span><span class="sxs-lookup"><span data-stu-id="45b23-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="45b23-114">Zmniejszenie duplikatu typowej zawartości znaczników w plikach znaczników.</span><span class="sxs-lookup"><span data-stu-id="45b23-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="45b23-115">Gdy te same elementy znaczników są używane w plikach znaczników, częściowy widok usuwa duplikowanie zawartości znacznika do jednego pliku widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="45b23-116">Gdy znacznik zostanie zmieniony w widoku częściowym, aktualizuje renderowane dane wyjściowe plików znaczników, które używają widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="45b23-117">Widoków częściowych nie należy używać do obsługi wspólnych elementów układu.</span><span class="sxs-lookup"><span data-stu-id="45b23-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="45b23-118">Wspólne elementy układu należy określić w plikach [_Layout. cshtml](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="45b23-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="45b23-119">Nie używaj widoku częściowego, w którym wymagana jest funkcja logiki renderowania złożonego lub wykonywania kodu.</span><span class="sxs-lookup"><span data-stu-id="45b23-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="45b23-120">Zamiast widoku częściowego, należy użyć [składnika widoku](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="45b23-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="45b23-121">Zadeklaruj częściowe widoki</span><span class="sxs-lookup"><span data-stu-id="45b23-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="45b23-122">Widok częściowy to plik *. cshtml* , bez [`@page`](xref:mvc/views/razor#page) dyrektywy obsługiwanej w folderze *widoki* (MVC) lub folderze *strony* ( Razor strony).</span><span class="sxs-lookup"><span data-stu-id="45b23-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="45b23-123">W ASP.NET Core MVC kontroler <xref:Microsoft.AspNetCore.Mvc.ViewResult> może zwrócić widok lub widok częściowy.</span><span class="sxs-lookup"><span data-stu-id="45b23-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="45b23-124">Na Razor stronach <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> można zwrócić widok częściowy reprezentowany jako <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> obiekt.</span><span class="sxs-lookup"><span data-stu-id="45b23-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="45b23-125">Odwołania do widoków częściowych i renderowania są opisane w sekcji [odwołanie do częściowego widoku](#reference-a-partial-view) .</span><span class="sxs-lookup"><span data-stu-id="45b23-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="45b23-126">W przeciwieństwie do widoku MVC lub renderowania stron, widok częściowy nie działa *_ViewStart. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45b23-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="45b23-127">Aby uzyskać więcej informacji na temat *_ViewStart. cshtml*, zobacz <xref:mvc/views/layout> .</span><span class="sxs-lookup"><span data-stu-id="45b23-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="45b23-128">Nazwy plików widoku częściowego często zaczynają się od znaku podkreślenia ( `_` ).</span><span class="sxs-lookup"><span data-stu-id="45b23-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="45b23-129">Ta konwencja nazewnictwa nie jest wymagana, ale pomaga wizualnie odróżnić widoki częściowe od widoków i stron.</span><span class="sxs-lookup"><span data-stu-id="45b23-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="45b23-130">Widok częściowy jest plikiem znaczników *. cshtml* , który jest przechowywany w folderze *widoki* .</span><span class="sxs-lookup"><span data-stu-id="45b23-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="45b23-131">Kontroler <xref:Microsoft.AspNetCore.Mvc.ViewResult> może zwrócić widok lub widok częściowy.</span><span class="sxs-lookup"><span data-stu-id="45b23-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="45b23-132">Odwołania do widoków częściowych i renderowania są opisane w sekcji [odwołanie do częściowego widoku](#reference-a-partial-view) .</span><span class="sxs-lookup"><span data-stu-id="45b23-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="45b23-133">W przeciwieństwie do renderowania widoku MVC widok częściowy nie działa *_ViewStart. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45b23-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="45b23-134">Aby uzyskać więcej informacji na temat *_ViewStart. cshtml*, zobacz <xref:mvc/views/layout> .</span><span class="sxs-lookup"><span data-stu-id="45b23-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="45b23-135">Nazwy plików widoku częściowego często zaczynają się od znaku podkreślenia ( `_` ).</span><span class="sxs-lookup"><span data-stu-id="45b23-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="45b23-136">Ta konwencja nazewnictwa nie jest wymagana, ale pomaga wizualnie odróżnić widoki częściowe od widoków.</span><span class="sxs-lookup"><span data-stu-id="45b23-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="45b23-137">Odwołuje się do widoku częściowego</span><span class="sxs-lookup"><span data-stu-id="45b23-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a><span data-ttu-id="45b23-138">Używanie widoku częściowego na Razor stronach PageModel</span><span class="sxs-lookup"><span data-stu-id="45b23-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="45b23-139">W ASP.NET Core 2,0 lub 2,1, następująca metoda obsługi renderuje widok częściowy *\_ AuthorPartialRP. cshtml* do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="45b23-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="45b23-140">W ASP.NET Core 2,2 lub nowszej metoda obsługi może Alternatywnie wywołać <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> metodę w celu utworzenia `PartialViewResult` obiektu:</span><span class="sxs-lookup"><span data-stu-id="45b23-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="45b23-141">Używanie widoku częściowego w pliku znaczników</span><span class="sxs-lookup"><span data-stu-id="45b23-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45b23-142">W pliku znaczników istnieje kilka sposobów odwoływania się do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="45b23-143">Zalecamy, aby aplikacje korzystały z jednej z następujących metod renderowania asynchronicznego:</span><span class="sxs-lookup"><span data-stu-id="45b23-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="45b23-144">Pomocnik tagu częściowego</span><span class="sxs-lookup"><span data-stu-id="45b23-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="45b23-145">Asynchroniczny pomocnik HTML</span><span class="sxs-lookup"><span data-stu-id="45b23-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="45b23-146">W pliku znaczników istnieją dwa sposoby odwoływania się do widoku częściowego:</span><span class="sxs-lookup"><span data-stu-id="45b23-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="45b23-147">Asynchroniczny pomocnik HTML</span><span class="sxs-lookup"><span data-stu-id="45b23-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="45b23-148">Synchroniczny pomocnik HTML</span><span class="sxs-lookup"><span data-stu-id="45b23-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="45b23-149">Zalecamy, aby aplikacje korzystały z [asynchronicznego pomocnika HTML](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="45b23-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="45b23-150">Pomocnik tagu częściowego</span><span class="sxs-lookup"><span data-stu-id="45b23-150">Partial Tag Helper</span></span>

<span data-ttu-id="45b23-151">[Pomocnik tagu częściowego](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) wymaga ASP.NET Core 2,1 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="45b23-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="45b23-152">Pomocnik tagów częściowej renderuje zawartość asynchronicznie i używa składni podobnej do kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="45b23-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="45b23-153">Gdy rozszerzenie pliku jest obecne, pomocnik tagów odwołuje się do widoku częściowego, który musi znajdować się w tym samym folderze co plik znaczników wywołujący widok częściowy:</span><span class="sxs-lookup"><span data-stu-id="45b23-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="45b23-154">Poniższy przykład odwołuje się do widoku częściowego z poziomu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45b23-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="45b23-155">Ścieżki, które zaczynają się od ukośnika ( `~/` ) lub ukośnika ( `/` ), można znaleźć w katalogu głównym aplikacji:</span><span class="sxs-lookup"><span data-stu-id="45b23-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="45b23-156">**Razor Page**</span><span class="sxs-lookup"><span data-stu-id="45b23-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="45b23-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="45b23-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="45b23-158">Poniższy przykład odwołuje się do widoku częściowego ze ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="45b23-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="45b23-159">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="45b23-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="45b23-160">Asynchroniczny pomocnik HTML</span><span class="sxs-lookup"><span data-stu-id="45b23-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="45b23-161">W przypadku korzystania z pomocnika HTML najlepszym rozwiązaniem jest użycie <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> .</span><span class="sxs-lookup"><span data-stu-id="45b23-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="45b23-162">`PartialAsync` zwraca <xref:Microsoft.AspNetCore.Html.IHtmlContent> Typ opakowany w <xref:System.Threading.Tasks.Task%601> .</span><span class="sxs-lookup"><span data-stu-id="45b23-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="45b23-163">Metoda jest przywoływana przez odtworzenie prefiksu oczekującego wywołania przy użyciu `@` znaku:</span><span class="sxs-lookup"><span data-stu-id="45b23-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="45b23-164">Gdy rozszerzenie pliku jest obecne, pomocnik HTML odwołuje się do widoku częściowego, który musi znajdować się w tym samym folderze co plik znaczników wywołujący widok częściowy:</span><span class="sxs-lookup"><span data-stu-id="45b23-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="45b23-165">Poniższy przykład odwołuje się do widoku częściowego z poziomu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45b23-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="45b23-166">Ścieżki, które zaczynają się od ukośnika ( `~/` ) lub ukośnika ( `/` ), można znaleźć w katalogu głównym aplikacji:</span><span class="sxs-lookup"><span data-stu-id="45b23-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45b23-167">**Razor Page**</span><span class="sxs-lookup"><span data-stu-id="45b23-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="45b23-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="45b23-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="45b23-169">Poniższy przykład odwołuje się do widoku częściowego ze ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="45b23-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="45b23-170">Alternatywnie możesz renderować widok częściowy za pomocą <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> .</span><span class="sxs-lookup"><span data-stu-id="45b23-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="45b23-171">Ta metoda nie zwraca elementu <xref:Microsoft.AspNetCore.Html.IHtmlContent> .</span><span class="sxs-lookup"><span data-stu-id="45b23-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="45b23-172">Przesyła strumieniowo renderowane dane wyjściowe bezpośrednio do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45b23-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="45b23-173">Ponieważ metoda nie zwraca wyniku, musi być wywołana w Razor bloku kodu:</span><span class="sxs-lookup"><span data-stu-id="45b23-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="45b23-174">Ponieważ `RenderPartialAsync` strumienie są renderowane, zapewnia lepszą wydajność w niektórych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="45b23-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="45b23-175">W sytuacjach krytycznych dla wydajności należy wykonać testy porównawcze strony przy użyciu obu podejścia i użyć podejścia, które generuje szybszy czas odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45b23-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="45b23-176">Synchroniczny pomocnik HTML</span><span class="sxs-lookup"><span data-stu-id="45b23-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="45b23-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> i <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> są synchronicznymi odpowiednikami `PartialAsync` i `RenderPartialAsync` , odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="45b23-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="45b23-178">Nie zaleca się synchronicznych odpowiedników, ponieważ występują scenariusze, w których są one zakleszczeniami.</span><span class="sxs-lookup"><span data-stu-id="45b23-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="45b23-179">Metody synchroniczne są przeznaczone do usunięcia w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="45b23-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="45b23-180">Jeśli musisz wykonać kod, użyj [składnika widoku](xref:mvc/views/view-components) zamiast widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45b23-181">Wywołanie `Partial` lub `RenderPartial` wynik w ostrzeżeniu programu Visual Studio Analyzer.</span><span class="sxs-lookup"><span data-stu-id="45b23-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="45b23-182">Na przykład, obecność `Partial` daje następujący komunikat ostrzegawczy:</span><span class="sxs-lookup"><span data-stu-id="45b23-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="45b23-183">Użycie IHtmlHelper. częściowe może spowodować zakleszczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45b23-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="45b23-184">Rozważ użycie &lt; &gt; pomocnika tagów częściowych lub IHtmlHelper. PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="45b23-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="45b23-185">Zamień wywołania na `@Html.Partial` with `@await Html.PartialAsync` lub [pomocnika tagów częściowych](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="45b23-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="45b23-186">Aby uzyskać więcej informacji na temat migracji pomocnika częściowego znacznika, zobacz [Migrowanie z pomocnika HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="45b23-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="45b23-187">Odnajdywanie widoku częściowego</span><span class="sxs-lookup"><span data-stu-id="45b23-187">Partial view discovery</span></span>

<span data-ttu-id="45b23-188">Gdy do widoku częściowego odwołuje się nazwa bez rozszerzenia pliku, następujące lokalizacje są przeszukiwane w podanej kolejności:</span><span class="sxs-lookup"><span data-stu-id="45b23-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45b23-189">**Razor Page**</span><span class="sxs-lookup"><span data-stu-id="45b23-189">**Razor Pages**</span></span>

1. <span data-ttu-id="45b23-190">Aktualnie wykonywany folder strony</span><span class="sxs-lookup"><span data-stu-id="45b23-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="45b23-191">Wykres katalogu powyżej folderu strony</span><span class="sxs-lookup"><span data-stu-id="45b23-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="45b23-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="45b23-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="45b23-193">Następujące konwencje dotyczą odnajdywania widoku częściowego:</span><span class="sxs-lookup"><span data-stu-id="45b23-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="45b23-194">Różne widoki częściowe o tej samej nazwie pliku są dozwolone, gdy częściowe widoki znajdują się w różnych folderach.</span><span class="sxs-lookup"><span data-stu-id="45b23-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="45b23-195">W przypadku odwoływania się do widoku częściowego według nazwy bez rozszerzenia pliku, gdy widok częściowy znajduje się zarówno w folderze wywołującym, jak i w folderze *udostępnionym* , widok częściowy w folderze obiektu wywołującego dostarcza widok częściowy.</span><span class="sxs-lookup"><span data-stu-id="45b23-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="45b23-196">Jeśli widok częściowy nie znajduje się w folderze wywołującym, w folderze *udostępnionym* zostanie udostępniony widok częściowy.</span><span class="sxs-lookup"><span data-stu-id="45b23-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="45b23-197">Częściowe widoki w folderze *udostępnionym* są nazywane *widokami części udostępnionych* lub *domyślnymi widokami częściowymi*.</span><span class="sxs-lookup"><span data-stu-id="45b23-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="45b23-198">Częściowe widoki mogą być *łańcucha* częściowy &mdash; Widok może wywoływać inny widok częściowy, jeśli odwołanie cykliczne nie jest tworzone przez wywołania.</span><span class="sxs-lookup"><span data-stu-id="45b23-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="45b23-199">Ścieżki względne są zawsze względne w stosunku do bieżącego pliku, nie do głównego lub nadrzędnego pliku.</span><span class="sxs-lookup"><span data-stu-id="45b23-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="45b23-200">Element [Razor](xref:mvc/views/razor) `section` zdefiniowany w widoku częściowym jest niewidoczny dla nadrzędnych plików znaczników.</span><span class="sxs-lookup"><span data-stu-id="45b23-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="45b23-201">`section`Jest widoczny tylko dla widoku częściowego, w którym jest zdefiniowany.</span><span class="sxs-lookup"><span data-stu-id="45b23-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="45b23-202">Dostęp do danych z widoków częściowych</span><span class="sxs-lookup"><span data-stu-id="45b23-202">Access data from partial views</span></span>

<span data-ttu-id="45b23-203">Po utworzeniu wystąpienia widoku częściowego otrzymuje on *kopię* słownika elementu nadrzędnego `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="45b23-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="45b23-204">Aktualizacje wprowadzone do danych w widoku częściowym nie są utrwalane w widoku nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="45b23-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="45b23-205">`ViewData` zmiany w częściowym widoku są tracone po powrocie widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="45b23-206">Poniższy przykład ilustruje, jak przekazać wystąpienie elementu [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) do widoku częściowego:</span><span class="sxs-lookup"><span data-stu-id="45b23-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="45b23-207">Można przekazać model do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="45b23-208">Model może być obiektem niestandardowym.</span><span class="sxs-lookup"><span data-stu-id="45b23-208">The model can be a custom object.</span></span> <span data-ttu-id="45b23-209">Można przekazać model z `PartialAsync` (renderuje blok zawartości do obiektu wywołującego) lub `RenderPartialAsync` (strumieniowo zawartość do danych wyjściowych):</span><span class="sxs-lookup"><span data-stu-id="45b23-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45b23-210">**Razor Page**</span><span class="sxs-lookup"><span data-stu-id="45b23-210">**Razor Pages**</span></span>

<span data-ttu-id="45b23-211">Następujące znaczniki w przykładowej aplikacji pochodzą ze strony *stron/ArticlesRP/ReadRP. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="45b23-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="45b23-212">Strona zawiera dwa widoki częściowe.</span><span class="sxs-lookup"><span data-stu-id="45b23-212">The page contains two partial views.</span></span> <span data-ttu-id="45b23-213">Drugi widok częściowy przechodzi w modelu i `ViewData` do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="45b23-214">`ViewDataDictionary`Przeciążenie konstruktora służy do przekazywania nowego `ViewData` słownika podczas zachowywania istniejącego `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="45b23-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="45b23-215">*Pages/Shared/_AuthorPartialRP. cshtml* to pierwszy widok częściowy, do którego odwołuje się plik znacznika *ReadRP. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="45b23-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="45b23-216">*Pages/ArticlesRP/_ArticleSectionRP. cshtml* to drugi widok częściowy, do którego odwołuje się plik znaczników *ReadRP. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="45b23-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="45b23-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="45b23-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="45b23-218">Poniższy znacznik w aplikacji przykładowej pokazuje widok *widoki/artykuły/Read. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="45b23-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="45b23-219">Widok zawiera dwa widoki częściowe.</span><span class="sxs-lookup"><span data-stu-id="45b23-219">The view contains two partial views.</span></span> <span data-ttu-id="45b23-220">Drugi widok częściowy przechodzi w modelu i `ViewData` do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="45b23-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="45b23-221">`ViewDataDictionary`Przeciążenie konstruktora służy do przekazywania nowego `ViewData` słownika podczas zachowywania istniejącego `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="45b23-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="45b23-222">*Widoki/Shared/_AuthorPartial. cshtml* to pierwszy widok częściowy, do którego odwołuje się plik znaczników *odczytu. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="45b23-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="45b23-223">*Widoki/artykuły/_ArticleSection. cshtml* to drugi widok częściowy, do którego odwołuje się plik znaczników *odczytu. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="45b23-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="45b23-224">W czasie wykonywania częściowe są renderowane do renderowanego wyjściowego pliku znaczników, który sam jest renderowany w udostępnionym *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45b23-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="45b23-225">Pierwszy widok częściowy renderuje nazwę autora artykułu i datę publikacji:</span><span class="sxs-lookup"><span data-stu-id="45b23-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="45b23-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="45b23-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="45b23-227">Ten widok częściowy z &lt; udostępnionej ścieżki pliku widoku częściowego &gt; .</span><span class="sxs-lookup"><span data-stu-id="45b23-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="45b23-228">11/19/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="45b23-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="45b23-229">Drugi widok częściowy renderuje sekcje artykułu:</span><span class="sxs-lookup"><span data-stu-id="45b23-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="45b23-230">Sekcja jeden indeks: 0</span><span class="sxs-lookup"><span data-stu-id="45b23-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="45b23-231">Cztery wyniki i siedem lat temu...</span><span class="sxs-lookup"><span data-stu-id="45b23-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="45b23-232">Sekcja dwa indeks: 1</span><span class="sxs-lookup"><span data-stu-id="45b23-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="45b23-233">Teraz pracujemy nad znakomitym wojny cywilnej, testowaniem...</span><span class="sxs-lookup"><span data-stu-id="45b23-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="45b23-234">Sekcja trzy indeks: 2</span><span class="sxs-lookup"><span data-stu-id="45b23-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="45b23-235">Jednak w większym sensie nie można przeznaczyć...</span><span class="sxs-lookup"><span data-stu-id="45b23-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="45b23-236">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="45b23-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="45b23-237">Razor odwołanie do składni dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45b23-237">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* [<span data-ttu-id="45b23-238">Razor odwołanie do składni dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45b23-238">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
