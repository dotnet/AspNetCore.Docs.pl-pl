---
title: Wprowadzenie do Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się Razor , jak strony w ASP.NET Core sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424562"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="880ab-103">Wprowadzenie do Razor Pages w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="880ab-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="880ab-104">[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="880ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="880ab-105">Razor Pages może sprawiać, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="880ab-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="880ab-106">Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="880ab-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="880ab-107">Ten dokument zawiera wprowadzenie do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="880ab-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="880ab-108">Nie jest to samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="880ab-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="880ab-109">Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="880ab-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="880ab-110">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="880ab-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="880ab-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="880ab-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="880ab-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="880ab-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="880ab-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="880ab-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="880ab-114">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="880ab-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="880ab-115">Tworzenie projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="880ab-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="880ab-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="880ab-117">Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia projektu Razor Pages, zobacz Wprowadzenie do [Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="880ab-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="880ab-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="880ab-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="880ab-119">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="880ab-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="880ab-120">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="880ab-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="880ab-121">Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia projektu Razor Pages, zobacz Wprowadzenie do [Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="880ab-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="880ab-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-122">Razor Pages</span></span>

<span data-ttu-id="880ab-123">Razor Pages jest włączona w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="880ab-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="880ab-124">Weź pod uwagę podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="880ab-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="880ab-125">Poprzedni kod wygląda podobnie jak [plik widoku Razor](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="880ab-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="880ab-126">Co sprawia, że jest to [`@page`](xref:mvc/views/razor#page) dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="880ab-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="880ab-127">`@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="880ab-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="880ab-128">`@page`musi być pierwszą dyrektywą Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="880ab-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="880ab-129">`@page`wpływa na zachowanie innych konstrukcji [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="880ab-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="880ab-130">Nazwy plików Razor Pages mają sufiks *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="880ab-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="880ab-131">Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="880ab-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="880ab-132">Plik *Pages/index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="880ab-133">Model strony *stron/index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="880ab-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="880ab-134">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak plik stronicowania Razor z dołączonym rozszerzeniem *. cs* .</span><span class="sxs-lookup"><span data-stu-id="880ab-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="880ab-135">Na przykład Poprzednia strona Razor to *Pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="880ab-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="880ab-136">Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="880ab-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="880ab-137">Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="880ab-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="880ab-138">W poniższej tabeli przedstawiono ścieżkę strony Razor i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="880ab-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="880ab-139">Nazwa i ścieżka pliku</span><span class="sxs-lookup"><span data-stu-id="880ab-139">File name and path</span></span>               | <span data-ttu-id="880ab-140">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="880ab-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="880ab-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="880ab-142">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="880ab-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="880ab-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="880ab-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="880ab-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="880ab-146">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="880ab-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="880ab-147">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="880ab-147">Notes:</span></span>

* <span data-ttu-id="880ab-148">Środowisko wykonawcze domyślnie wyszukuje pliki Razor Pages w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="880ab-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="880ab-149">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="880ab-150">Napisz podstawowy formularz</span><span class="sxs-lookup"><span data-stu-id="880ab-150">Write a basic form</span></span>

<span data-ttu-id="880ab-151">Razor Pages zaprojektowano tak, aby wspólne wzorce używane z przeglądarkami sieci Web były łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="880ab-152">[Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w klasie strony Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="880ab-153">Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="880ab-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="880ab-154">Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .</span><span class="sxs-lookup"><span data-stu-id="880ab-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="880ab-155">Model danych:</span><span class="sxs-lookup"><span data-stu-id="880ab-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="880ab-156">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="880ab-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="880ab-157">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="880ab-158">Model strony *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="880ab-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="880ab-159">Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="880ab-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="880ab-160">`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="880ab-161">Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="880ab-162">Ta separacja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="880ab-162">This separation allows:</span></span>

* <span data-ttu-id="880ab-163">Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="880ab-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="880ab-164">Testowanie jednostkowe</span><span class="sxs-lookup"><span data-stu-id="880ab-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="880ab-165">Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz).</span><span class="sxs-lookup"><span data-stu-id="880ab-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="880ab-166">Można dodać metody obsługi dla dowolnego zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="880ab-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="880ab-167">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="880ab-167">The most common handlers are:</span></span>

* <span data-ttu-id="880ab-168">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="880ab-169">W powyższym kodzie `OnGet` Metoda wyświetla stronę Razor *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="880ab-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="880ab-170">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="880ab-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="880ab-171">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="880ab-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="880ab-172">Poprzedni kod jest typowy dla Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="880ab-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="880ab-173">Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="880ab-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="880ab-174">`OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="880ab-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="880ab-175">Większość podstawowych elementów MVC, takich jak [powiązania modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation)i akcje, działa tak samo z kontrolerami i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="880ab-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="880ab-176">Poprzednia `OnPostAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="880ab-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="880ab-177">Podstawowy przepływ `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="880ab-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="880ab-178">Sprawdź, czy występują błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-178">Check for validation errors.</span></span>

* <span data-ttu-id="880ab-179">Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.</span><span class="sxs-lookup"><span data-stu-id="880ab-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="880ab-180">W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="880ab-181">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="880ab-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="880ab-182">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="880ab-183">Renderowany kod HTML ze *stron/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="880ab-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="880ab-184">W poprzednim kodzie, ogłaszanie formularza:</span><span class="sxs-lookup"><span data-stu-id="880ab-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="880ab-185">Z prawidłowymi danymi:</span><span class="sxs-lookup"><span data-stu-id="880ab-185">With valid data:</span></span>

  * <span data-ttu-id="880ab-186">`OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="880ab-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="880ab-187">Metoda `RedirectToPage` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="880ab-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="880ab-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="880ab-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="880ab-189">Jest wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="880ab-189">Is an action result.</span></span>
    * <span data-ttu-id="880ab-190">Jest podobny do `RedirectToAction` lub `RedirectToRoute` (używany w kontrolerach i widokach).</span><span class="sxs-lookup"><span data-stu-id="880ab-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="880ab-191">Jest dostosowywany dla stron.</span><span class="sxs-lookup"><span data-stu-id="880ab-191">Is customized for pages.</span></span> <span data-ttu-id="880ab-192">W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="880ab-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="880ab-193">`RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="880ab-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="880ab-194">Z błędami walidacji, które są przesyłane do serwera:</span><span class="sxs-lookup"><span data-stu-id="880ab-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="880ab-195">`OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="880ab-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="880ab-196">Metoda `Page` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="880ab-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="880ab-197">Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` .</span><span class="sxs-lookup"><span data-stu-id="880ab-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="880ab-198">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="880ab-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="880ab-199">Metoda obsługi, która zwraca `void` renderowanie strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="880ab-200">W poprzednim przykładzie, księgowanie formularza bez wartości powoduje, że [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zwraca wartość false.</span><span class="sxs-lookup"><span data-stu-id="880ab-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="880ab-201">W tym przykładzie na kliencie nie są wyświetlane błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="880ab-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="880ab-202">Przekazywanie błędów sprawdzania poprawności jest omówione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="880ab-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="880ab-203">Z błędami walidacji wykrytymi przez weryfikację po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="880ab-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="880ab-204">Dane **nie** są ogłaszane na serwerze.</span><span class="sxs-lookup"><span data-stu-id="880ab-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="880ab-205">Sprawdzanie poprawności po stronie klienta zostało wyjaśnione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="880ab-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="880ab-206">`Customer`Właściwość używa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atrybutu, aby zrezygnować z powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="880ab-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="880ab-207">`[BindProperty]`**nie** powinien być używany dla modeli zawierających właściwości, które nie powinny być zmieniane przez klienta.</span><span class="sxs-lookup"><span data-stu-id="880ab-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="880ab-208">Aby uzyskać więcej informacji, zobacz temat [przefinalizowanie](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="880ab-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="880ab-209">Razor Pages domyślnie Powiąż właściwości tylko z `GET` niezleceniami.</span><span class="sxs-lookup"><span data-stu-id="880ab-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="880ab-210">Powiązanie z właściwościami eliminuje konieczność pisania kodu w celu przekonwertowania danych HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="880ab-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="880ab-211">Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="880ab-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="880ab-212">Przeglądanie pliku widoku *stron/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="880ab-213">W poprzednim kodzie [pomocnik tagów wejściowych](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` wiąże `<input>` element HTML z `Customer.Name` wyrażeniem modelu.</span><span class="sxs-lookup"><span data-stu-id="880ab-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="880ab-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)udostępnia pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="880ab-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="880ab-215">Strona główna</span><span class="sxs-lookup"><span data-stu-id="880ab-215">The home page</span></span>

<span data-ttu-id="880ab-216">*Index. cshtml* to Strona główna:</span><span class="sxs-lookup"><span data-stu-id="880ab-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="880ab-217">Skojarzona `PageModel` Klasa (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="880ab-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="880ab-218">Plik *index. cshtml* zawiera następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="880ab-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="880ab-219">`<a /a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji.</span><span class="sxs-lookup"><span data-stu-id="880ab-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="880ab-220">Link zawiera dane trasy z IDENTYFIKATORem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="880ab-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="880ab-221">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="880ab-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="880ab-222">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="880ab-223">Plik *index. cshtml* zawiera znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="880ab-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="880ab-224">Renderowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="880ab-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="880ab-225">Gdy przycisk Usuń jest renderowany w języku HTML, jego [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) zawiera parametry dla:</span><span class="sxs-lookup"><span data-stu-id="880ab-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="880ab-226">Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.</span><span class="sxs-lookup"><span data-stu-id="880ab-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="880ab-227">`handler`, Określony przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="880ab-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="880ab-228">Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza.</span><span class="sxs-lookup"><span data-stu-id="880ab-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="880ab-229">Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="880ab-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="880ab-230">Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="880ab-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="880ab-231">Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="880ab-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="880ab-232">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="880ab-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="880ab-233">Pobiera `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="880ab-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="880ab-234">Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="880ab-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="880ab-235">Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty, a baza danych zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="880ab-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="880ab-236">Wywołania <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> do przekierowania na stronę indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="880ab-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="880ab-237">Plik Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="880ab-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="880ab-238">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="880ab-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="880ab-239">Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="880ab-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="880ab-240">Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="880ab-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="880ab-241">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="880ab-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="880ab-242">Plik *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="880ab-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="880ab-243">Walidacja</span><span class="sxs-lookup"><span data-stu-id="880ab-243">Validation</span></span>

<span data-ttu-id="880ab-244">Reguły walidacji:</span><span class="sxs-lookup"><span data-stu-id="880ab-244">Validation rules:</span></span>

* <span data-ttu-id="880ab-245">Są deklaratywnie określone w klasie modelu.</span><span class="sxs-lookup"><span data-stu-id="880ab-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="880ab-246">Są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="880ab-247"><xref:System.ComponentModel.DataAnnotations>Przestrzeń nazw zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="880ab-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="880ab-248">Adnotacje DataAnnotation zawierają również atrybuty formatowania, takie jak [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) Pomoc dotycząca formatowania i nie zapewniają weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="880ab-249">Rozważmy `Customer` model:</span><span class="sxs-lookup"><span data-stu-id="880ab-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="880ab-250">Za pomocą następującego pliku widoku *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="880ab-251">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="880ab-251">The preceding code:</span></span>

* <span data-ttu-id="880ab-252">Obejmuje skrypty sprawdzania poprawności jQuery i jQuery.</span><span class="sxs-lookup"><span data-stu-id="880ab-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="880ab-253">Używa `<div />` `<span />` [pomocników tagów](xref:mvc/views/tag-helpers/intro) i do włączania:</span><span class="sxs-lookup"><span data-stu-id="880ab-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="880ab-254">Sprawdzanie poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="880ab-254">Client-side validation.</span></span>
  * <span data-ttu-id="880ab-255">Renderowanie błędów walidacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-255">Validation error rendering.</span></span>

* <span data-ttu-id="880ab-256">Generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="880ab-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="880ab-257">Opublikowanie formularza tworzenia bez wartości nazwa powoduje wyświetlenie komunikatu o błędzie "Nazwa pola jest wymagana."</span><span class="sxs-lookup"><span data-stu-id="880ab-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="880ab-258">w formularzu.</span><span class="sxs-lookup"><span data-stu-id="880ab-258">on the form.</span></span> <span data-ttu-id="880ab-259">Jeśli na kliencie jest włączona obsługa języka JavaScript, przeglądarka wyświetli komunikat o błędzie bez publikowania na serwerze.</span><span class="sxs-lookup"><span data-stu-id="880ab-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="880ab-260">Ten `[StringLength(10)]` atrybut jest generowany `data-val-length-max="10"` na RENDEROWANYM kodzie HTML.</span><span class="sxs-lookup"><span data-stu-id="880ab-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="880ab-261">`data-val-length-max`zapobiega wprowadzaniu przez przeglądarki więcej niż określoną maksymalną długość.</span><span class="sxs-lookup"><span data-stu-id="880ab-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="880ab-262">Jeśli jest używane narzędzie, takie jak [programu Fiddler](https://www.telerik.com/fiddler) , do edytowania i powtarzania wpisu:</span><span class="sxs-lookup"><span data-stu-id="880ab-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="880ab-263">O nazwie dłuższej niż 10.</span><span class="sxs-lookup"><span data-stu-id="880ab-263">With the name longer than 10.</span></span>
* <span data-ttu-id="880ab-264">Komunikat o błędzie "Nazwa pola musi być ciągiem o maksymalnej długości 10".</span><span class="sxs-lookup"><span data-stu-id="880ab-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="880ab-265">jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="880ab-265">is returned.</span></span>

<span data-ttu-id="880ab-266">Rozważmy następujący `Movie` model:</span><span class="sxs-lookup"><span data-stu-id="880ab-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="880ab-267">Atrybuty walidacji określają zachowanie do wymuszania na właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="880ab-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="880ab-268">`Required`Atrybuty i `MinimumLength` wskazują, że właściwość musi mieć wartość, ale nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="880ab-269">Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="880ab-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="880ab-270">W poprzednim kodzie "gatunek":</span><span class="sxs-lookup"><span data-stu-id="880ab-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="880ab-271">Należy używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="880ab-271">Must only use letters.</span></span>
  * <span data-ttu-id="880ab-272">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="880ab-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="880ab-273">Odstępy, cyfry i znaki specjalne są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="880ab-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="880ab-274">`RegularExpression`"Ocena":</span><span class="sxs-lookup"><span data-stu-id="880ab-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="880ab-275">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="880ab-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="880ab-276">Zezwala na znaki specjalne i cyfry w kolejnych odstępach.</span><span class="sxs-lookup"><span data-stu-id="880ab-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="880ab-277">"PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".</span><span class="sxs-lookup"><span data-stu-id="880ab-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="880ab-278">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="880ab-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="880ab-279">`StringLength`Atrybut ustawia maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.</span><span class="sxs-lookup"><span data-stu-id="880ab-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="880ab-280">Typy wartości (takie jak `decimal` ,,, `int` `float` `DateTime` ) są z założenia wymagane i nie wymagają `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="880ab-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="880ab-281">Na stronie Tworzenie dla `Movie` modelu są wyświetlane błędy z nieprawidłowymi wartościami:</span><span class="sxs-lookup"><span data-stu-id="880ab-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="880ab-283">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="880ab-283">For more information, see:</span></span>

* [<span data-ttu-id="880ab-284">Dodawanie walidacji do aplikacji filmowej</span><span class="sxs-lookup"><span data-stu-id="880ab-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="880ab-285">[Walidacja modelu w ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="880ab-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="880ab-286">Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="880ab-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="880ab-287">`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="880ab-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="880ab-288">W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="880ab-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="880ab-289">Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:</span><span class="sxs-lookup"><span data-stu-id="880ab-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="880ab-290">Razor Pages powracać do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="880ab-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="880ab-291">XSRF/CSRF i Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="880ab-292">Razor Pages są chronione przez [weryfikację przed fałszerstwem](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="880ab-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="880ab-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="880ab-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="880ab-294">Używanie układów, częściowych, szablonów i pomocników tagów z Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="880ab-295">Strony współpracują ze wszystkimi funkcjami aparatu widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="880ab-296">Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*i *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku konwencjonalnych widoków Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="880ab-297">Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="880ab-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="880ab-298">Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="880ab-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="880ab-299">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="880ab-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="880ab-300">Steruje układem każdej strony (chyba że strona nie jest częścią układu).</span><span class="sxs-lookup"><span data-stu-id="880ab-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="880ab-301">Importuje struktury HTML, takie jak JavaScript i stylesheets.</span><span class="sxs-lookup"><span data-stu-id="880ab-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="880ab-302">Zawartość strony Razor jest renderowana, gdzie `@RenderBody()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="880ab-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="880ab-303">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="880ab-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="880ab-304">Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="880ab-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="880ab-305">Układ znajduje się w *stronie/w folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="880ab-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="880ab-306">Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="880ab-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="880ab-307">Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej strony Razor w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="880ab-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="880ab-308">Plik układu powinien przejść do *stron/folderu udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="880ab-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="880ab-309">Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="880ab-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="880ab-310">*Widoki/udostępnione* są wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="880ab-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="880ab-311">Razor Pages są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.</span><span class="sxs-lookup"><span data-stu-id="880ab-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="880ab-312">Widok wyszukiwania na stronie Razor zawiera folder *strony* .</span><span class="sxs-lookup"><span data-stu-id="880ab-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="880ab-313">Układy, szablony i częściowe używane z kontrolerami MVC i konwencjonalnymi widokami Razor *same działają*.</span><span class="sxs-lookup"><span data-stu-id="880ab-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="880ab-314">Dodaj plik *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="880ab-315">`@namespace`wyjaśniono w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="880ab-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="880ab-316">Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="880ab-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="880ab-317">`@namespace`Dyrektywa ustawiona na stronie:</span><span class="sxs-lookup"><span data-stu-id="880ab-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="880ab-318">`@namespace`Dyrektywa ustawia przestrzeń nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="880ab-319">`@model`Dyrektywa nie musi zawierać przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="880ab-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="880ab-320">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="880ab-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="880ab-321">Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="880ab-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="880ab-322">Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="880ab-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="880ab-323">Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="880ab-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="880ab-324">Wygenerowana przestrzeń nazw dla strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.</span><span class="sxs-lookup"><span data-stu-id="880ab-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="880ab-325">`@namespace`*działa również z konwencjonalnymi widokami Razor.*</span><span class="sxs-lookup"><span data-stu-id="880ab-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="880ab-326">Rozważ użycie pliku widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="880ab-327">Zaktualizowane *strony/Create. cshtml* plik widoku z *_ViewImports. cshtml* i poprzedni plik układu:</span><span class="sxs-lookup"><span data-stu-id="880ab-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="880ab-328">W poprzednim kodzie, *_ViewImports. cshtml* zaimportował przestrzeń nazw i pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="880ab-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="880ab-329">Plik układu zaimportował pliki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="880ab-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="880ab-330">[Razor Pages początkowy projekt](#rpvs17) zawiera elementy *pages/_ValidationScriptsPartial. cshtml*, które przechwytuje weryfikację po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="880ab-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="880ab-331">Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="880ab-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="880ab-332">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="880ab-332">URL generation for Pages</span></span>

<span data-ttu-id="880ab-333">`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="880ab-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="880ab-334">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="880ab-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="880ab-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="880ab-335">*/Pages*</span></span>

  * <span data-ttu-id="880ab-336">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="880ab-337">*Privacy. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="880ab-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="880ab-338">*/Customers*</span></span>

    * <span data-ttu-id="880ab-339">*Create. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="880ab-340">*Edytuj. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="880ab-341">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-341">*Index.cshtml*</span></span>

<span data-ttu-id="880ab-342">Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierowywać do *stron/Customers/index. cshtml* po powodzeniu.</span><span class="sxs-lookup"><span data-stu-id="880ab-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="880ab-343">Ciąg `./Index` jest względną nazwą strony używaną w celu uzyskania dostępu do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="880ab-344">Służy do generowania adresów URL na stronie *strony/klienci/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="880ab-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="880ab-345">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="880ab-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="880ab-346">Bezwzględna nazwa strony `/Index` służy do generowania adresów URL na stronie *stron/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="880ab-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="880ab-347">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="880ab-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="880ab-348">Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="880ab-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="880ab-349">Powyższe przykłady generowania adresów URL oferują udoskonalone Opcje i możliwości funkcjonalne w porównaniu z zakodowanym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="880ab-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="880ab-350">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="880ab-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="880ab-351">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="880ab-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="880ab-352">W poniższej tabeli przedstawiono, która strona indeksu została wybrana przy użyciu różnych `RedirectToPage` parametrów na stronie */Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="880ab-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="880ab-353">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="880ab-353">RedirectToPage(x)</span></span>| <span data-ttu-id="880ab-354">Strona</span><span class="sxs-lookup"><span data-stu-id="880ab-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="880ab-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="880ab-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="880ab-356">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-356">*Pages/Index*</span></span> |
| <span data-ttu-id="880ab-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="880ab-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="880ab-358">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="880ab-359">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="880ab-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="880ab-360">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-360">*Pages/Index*</span></span> |
| <span data-ttu-id="880ab-361">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="880ab-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="880ab-362">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="880ab-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="880ab-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="880ab-364">`RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="880ab-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="880ab-365">Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą.</span><span class="sxs-lookup"><span data-stu-id="880ab-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="880ab-366">Gdy nazwy względne są używane do łączenia między stronami w folderze:</span><span class="sxs-lookup"><span data-stu-id="880ab-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="880ab-367">Zmiana nazwy folderu nie powoduje zerwania linków względnych.</span><span class="sxs-lookup"><span data-stu-id="880ab-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="880ab-368">Linki nie są przerwane, ponieważ nie zawierają nazwy folderu.</span><span class="sxs-lookup"><span data-stu-id="880ab-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="880ab-369">Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="880ab-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="880ab-370">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas> i <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="880ab-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="880ab-371">ViewData — atrybut</span><span class="sxs-lookup"><span data-stu-id="880ab-371">ViewData attribute</span></span>

<span data-ttu-id="880ab-372">Dane można przekazywać do strony za pomocą <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="880ab-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="880ab-373">Właściwości z `[ViewData]` atrybutem mają przechowywane i ładowane wartości z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="880ab-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="880ab-374">W poniższym przykładzie `AboutModel` stosuje `[ViewData]` atrybut do `Title` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="880ab-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="880ab-375">Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="880ab-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="880ab-376">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="880ab-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="880ab-377">TempData</span><span class="sxs-lookup"><span data-stu-id="880ab-377">TempData</span></span>

<span data-ttu-id="880ab-378">ASP.NET Core uwidacznia <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="880ab-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="880ab-379">Ta właściwość przechowuje dane, dopóki nie zostanie odczytana.</span><span class="sxs-lookup"><span data-stu-id="880ab-379">This property stores data until it's read.</span></span> <span data-ttu-id="880ab-380"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody i <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="880ab-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="880ab-381">`TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="880ab-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="880ab-382">Poniższy kod ustawia wartość `Message` użycia `TempData` :</span><span class="sxs-lookup"><span data-stu-id="880ab-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="880ab-383">W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="880ab-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="880ab-384">Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="880ab-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="880ab-385">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="880ab-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="880ab-386">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="880ab-386">Multiple handlers per page</span></span>

<span data-ttu-id="880ab-387">Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="880ab-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="880ab-388">Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="880ab-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="880ab-389">Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="880ab-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="880ab-390">`asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę.</span><span class="sxs-lookup"><span data-stu-id="880ab-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="880ab-391">`asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="880ab-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="880ab-392">Model strony:</span><span class="sxs-lookup"><span data-stu-id="880ab-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="880ab-393">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="880ab-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="880ab-394">Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="880ab-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="880ab-395">W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="880ab-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="880ab-396">Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="880ab-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="880ab-397">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="880ab-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="880ab-398">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="880ab-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="880ab-399">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="880ab-399">Custom routes</span></span>

<span data-ttu-id="880ab-400">Użyj `@page` dyrektywy, aby:</span><span class="sxs-lookup"><span data-stu-id="880ab-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="880ab-401">Określ trasę niestandardową dla strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-401">Specify a custom route to a page.</span></span> <span data-ttu-id="880ab-402">Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="880ab-403">Dołącz segmenty do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-403">Append segments to a page's default route.</span></span> <span data-ttu-id="880ab-404">Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="880ab-405">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="880ab-406">Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="880ab-407">Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="880ab-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="880ab-408">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="880ab-409">Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="880ab-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="880ab-410">Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="880ab-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="880ab-411">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="880ab-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="880ab-412">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="880ab-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="880ab-413">`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="880ab-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="880ab-414">Zaawansowana konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="880ab-414">Advanced configuration and settings</span></span>

<span data-ttu-id="880ab-415">Konfiguracja i ustawienia w poniższych sekcjach nie są wymagane przez większość aplikacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="880ab-416">Aby skonfigurować opcje zaawansowane, użyj metody rozszerzenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> :</span><span class="sxs-lookup"><span data-stu-id="880ab-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="880ab-417">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="880ab-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="880ab-418">Aby uzyskać więcej informacji na temat Konwencji, zobacz [Razor Pages Konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="880ab-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="880ab-419">Aby wstępnie skompilować widoki, zobacz [kompilacja widoku Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="880ab-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="880ab-420">Określ, że Razor Pages znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="880ab-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="880ab-421">Domyślnie Razor Pages są umieszczane w katalogu */Pages* .</span><span class="sxs-lookup"><span data-stu-id="880ab-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="880ab-422">Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> , aby określić, że twoje Razor Pages znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> aplikacji:</span><span class="sxs-lookup"><span data-stu-id="880ab-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="880ab-423">Określ, że Razor Pages znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="880ab-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="880ab-424">Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> , aby określić, że Razor Pages znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="880ab-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="880ab-425">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="880ab-425">Additional resources</span></span>

* <span data-ttu-id="880ab-426">Zobacz Rozpoczynanie [pracy z usługą Razor Pages](xref:tutorials/razor-pages/razor-pages-start), która kompiluje się w tym wprowadzeniu</span><span class="sxs-lookup"><span data-stu-id="880ab-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="880ab-427">Pobieranie lub wyświetlanie przykładowego kodu</span><span class="sxs-lookup"><span data-stu-id="880ab-427">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="880ab-428">[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="880ab-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="880ab-429">Razor Pages jest nowym aspektem ASP.NET Core MVC, który sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i bardziej produktywne.</span><span class="sxs-lookup"><span data-stu-id="880ab-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="880ab-430">Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="880ab-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="880ab-431">Ten dokument zawiera wprowadzenie do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="880ab-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="880ab-432">Nie jest to samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="880ab-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="880ab-433">Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="880ab-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="880ab-434">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="880ab-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="880ab-435">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="880ab-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="880ab-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="880ab-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="880ab-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="880ab-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="880ab-438">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="880ab-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="880ab-439">Tworzenie projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="880ab-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="880ab-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="880ab-441">Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia projektu Razor Pages, zobacz Wprowadzenie do [Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="880ab-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="880ab-442">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="880ab-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="880ab-443">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="880ab-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="880ab-444">Otwórz wygenerowany plik *csproj* z Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="880ab-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="880ab-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="880ab-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="880ab-446">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="880ab-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="880ab-447">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-447">Razor Pages</span></span>

<span data-ttu-id="880ab-448">Razor Pages jest włączona w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="880ab-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="880ab-449">Weź pod uwagę podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="880ab-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="880ab-450">Poprzedni kod wygląda podobnie jak [plik widoku Razor](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="880ab-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="880ab-451">Co sprawia, że jest to `@page` dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="880ab-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="880ab-452">`@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="880ab-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="880ab-453">`@page`musi być pierwszą dyrektywą Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="880ab-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="880ab-454">`@page`wpływa na zachowanie innych konstrukcji Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="880ab-455">Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="880ab-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="880ab-456">Plik *Pages/index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="880ab-457">Model strony *stron/index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="880ab-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="880ab-458">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak plik stronicowania Razor z dołączonym rozszerzeniem *. cs* .</span><span class="sxs-lookup"><span data-stu-id="880ab-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="880ab-459">Na przykład Poprzednia strona Razor to *Pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="880ab-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="880ab-460">Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="880ab-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="880ab-461">Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="880ab-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="880ab-462">W poniższej tabeli przedstawiono ścieżkę strony Razor i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="880ab-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="880ab-463">Nazwa i ścieżka pliku</span><span class="sxs-lookup"><span data-stu-id="880ab-463">File name and path</span></span>               | <span data-ttu-id="880ab-464">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="880ab-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="880ab-465">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="880ab-466">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="880ab-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="880ab-467">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="880ab-468">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="880ab-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="880ab-470">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="880ab-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="880ab-471">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="880ab-471">Notes:</span></span>

* <span data-ttu-id="880ab-472">Środowisko wykonawcze domyślnie wyszukuje pliki Razor Pages w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="880ab-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="880ab-473">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="880ab-474">Napisz podstawowy formularz</span><span class="sxs-lookup"><span data-stu-id="880ab-474">Write a basic form</span></span>

<span data-ttu-id="880ab-475">Razor Pages zaprojektowano tak, aby wspólne wzorce używane z przeglądarkami sieci Web były łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="880ab-476">[Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w klasie strony Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="880ab-477">Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="880ab-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="880ab-478">Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="880ab-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="880ab-479">Model danych:</span><span class="sxs-lookup"><span data-stu-id="880ab-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="880ab-480">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="880ab-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="880ab-481">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="880ab-482">Model strony *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="880ab-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="880ab-483">Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="880ab-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="880ab-484">`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="880ab-485">Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="880ab-486">Ta separacja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="880ab-486">This separation allows:</span></span>

* <span data-ttu-id="880ab-487">Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="880ab-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="880ab-488">[Testowanie jednostkowe](xref:test/razor-pages-tests) stron.</span><span class="sxs-lookup"><span data-stu-id="880ab-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="880ab-489">Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz).</span><span class="sxs-lookup"><span data-stu-id="880ab-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="880ab-490">Metody procedury obsługi można dodać dla dowolnego czasownika HTTP.</span><span class="sxs-lookup"><span data-stu-id="880ab-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="880ab-491">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="880ab-491">The most common handlers are:</span></span>

* <span data-ttu-id="880ab-492">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="880ab-493">Przykład [OnGet](#OnGet) .</span><span class="sxs-lookup"><span data-stu-id="880ab-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="880ab-494">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="880ab-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="880ab-495">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="880ab-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="880ab-496">Poprzedni kod jest typowy dla Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="880ab-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="880ab-497">Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="880ab-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="880ab-498">`OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="880ab-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="880ab-499">Większość elementów podstawowych MVC, takich jak [powiązanie modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation), [Walidacja](xref:mvc/models/validation)i wyniki akcji, jest udostępniana.</span><span class="sxs-lookup"><span data-stu-id="880ab-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="880ab-500">Poprzednia `OnPostAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="880ab-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="880ab-501">Podstawowy przepływ `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="880ab-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="880ab-502">Sprawdź, czy występują błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-502">Check for validation errors.</span></span>

* <span data-ttu-id="880ab-503">Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.</span><span class="sxs-lookup"><span data-stu-id="880ab-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="880ab-504">W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji.</span><span class="sxs-lookup"><span data-stu-id="880ab-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="880ab-505">Walidacja po stronie klienta jest taka sama jak w przypadku tradycyjnych ASP.NET Core aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="880ab-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="880ab-506">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="880ab-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="880ab-507">Po pomyślnym wprowadzeniu danych `OnPostAsync` Metoda obsługi wywołuje `RedirectToPage` metodę pomocnika zwracającą wystąpienie elementu `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="880ab-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="880ab-508">`RedirectToPage`to nowy wynik akcji, podobny do `RedirectToAction` lub `RedirectToRoute` , ale dostosowany do stron.</span><span class="sxs-lookup"><span data-stu-id="880ab-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="880ab-509">W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="880ab-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="880ab-510">`RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="880ab-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="880ab-511">Gdy przesłany formularz ma błędy walidacji (które są przekazywane do serwera), `OnPostAsync` Metoda obsługi wywołuje `Page` metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="880ab-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="880ab-512">Metoda `Page` zwraca wystąpienie klasy `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="880ab-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="880ab-513">Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` .</span><span class="sxs-lookup"><span data-stu-id="880ab-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="880ab-514">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="880ab-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="880ab-515">Metoda obsługi, która zwraca `void` renderowanie strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="880ab-516">`Customer`Właściwość używa `[BindProperty]` atrybutu, aby zrezygnować z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="880ab-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="880ab-517">Razor Pages domyślnie Powiąż właściwości tylko z `GET` niezleceniami.</span><span class="sxs-lookup"><span data-stu-id="880ab-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="880ab-518">Utworzenie powiązania z właściwościami może zmniejszyć ilość kodu, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="880ab-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="880ab-519">Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="880ab-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="880ab-520">Strona główna (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="880ab-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="880ab-521">Skojarzona `PageModel` Klasa (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="880ab-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="880ab-522">Plik *index. cshtml* zawiera następujące znaczniki, aby utworzyć łącze do edycji dla każdego kontaktu:</span><span class="sxs-lookup"><span data-stu-id="880ab-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="880ab-523">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji.</span><span class="sxs-lookup"><span data-stu-id="880ab-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="880ab-524">Link zawiera dane trasy z IDENTYFIKATORem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="880ab-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="880ab-525">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="880ab-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="880ab-526">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="880ab-527">Pomocnicy tagów są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="880ab-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="880ab-528">Plik *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="880ab-529">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="880ab-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="880ab-530">Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="880ab-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="880ab-531">Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="880ab-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="880ab-532">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="880ab-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="880ab-533">Plik *stron/Edytuj. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="880ab-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="880ab-534">Plik *index. cshtml* zawiera również znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="880ab-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="880ab-535">Gdy przycisk Usuń jest renderowany w języku HTML, jego `formaction` Parametry obejmują:</span><span class="sxs-lookup"><span data-stu-id="880ab-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="880ab-536">Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.</span><span class="sxs-lookup"><span data-stu-id="880ab-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="880ab-537">`handler`Określony przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="880ab-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="880ab-538">Oto przykład renderowanego przycisku usuwania z IDENTYFIKATORem kontaktu klienta `1` :</span><span class="sxs-lookup"><span data-stu-id="880ab-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="880ab-539">Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza.</span><span class="sxs-lookup"><span data-stu-id="880ab-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="880ab-540">Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="880ab-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="880ab-541">Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="880ab-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="880ab-542">Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="880ab-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="880ab-543">Poniższy kod ilustruje `OnPostDeleteAsync` procedurę obsługi:</span><span class="sxs-lookup"><span data-stu-id="880ab-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="880ab-544">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="880ab-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="880ab-545">Akceptuje `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="880ab-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="880ab-546">Jeśli na stronie *index. cshtml* znajduje się ograniczenie routingu `"{id:int?}"` , `id` będą pochodzić z danych tras.</span><span class="sxs-lookup"><span data-stu-id="880ab-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="880ab-547">Dane trasy dla programu `id` są określone w identyfikatorze URI, takich jak `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="880ab-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="880ab-548">Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="880ab-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="880ab-549">Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty z listy kontaktów klientów.</span><span class="sxs-lookup"><span data-stu-id="880ab-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="880ab-550">Baza danych jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="880ab-550">The database is updated.</span></span>
* <span data-ttu-id="880ab-551">Wywołania `RedirectToPage` do przekierowania na stronę indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="880ab-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="880ab-552">Oznacz właściwości strony jako wymagane</span><span class="sxs-lookup"><span data-stu-id="880ab-552">Mark page properties as required</span></span>

<span data-ttu-id="880ab-553">Właściwości na stronie `PageModel` można oznaczyć przy użyciu [wymaganego](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atrybutu:</span><span class="sxs-lookup"><span data-stu-id="880ab-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="880ab-554">Aby uzyskać więcej informacji, zobacz [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="880ab-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="880ab-555">Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="880ab-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="880ab-556">`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="880ab-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="880ab-557">W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="880ab-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="880ab-558">Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:</span><span class="sxs-lookup"><span data-stu-id="880ab-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="880ab-559">W ASP.NET Core 2,1 lub nowszej Razor Pages powracać do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="880ab-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="880ab-560">To zachowanie jest włączane przez wywołanie [SetCompatibilityVersion](xref:mvc/compatibility-version) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="880ab-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="880ab-561">Szablony domyślne generują `SetCompatibilityVersion` wywołanie w ASP.NET Core 2,1 i 2,2.</span><span class="sxs-lookup"><span data-stu-id="880ab-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="880ab-562">`SetCompatibilityVersion`efektywnie ustawia opcję Razor Pages `AllowMappingHeadRequestsToGetHandler` na `true` .</span><span class="sxs-lookup"><span data-stu-id="880ab-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="880ab-563">Zamiast korzystać z wszystkich zachowań w programie `SetCompatibilityVersion` , można jawnie zrezygnować z *określonych* zachowań.</span><span class="sxs-lookup"><span data-stu-id="880ab-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="880ab-564">Poniższy kod pozwala na umożliwienie `HEAD` mapowania żądań do `OnGet` programu obsługi:</span><span class="sxs-lookup"><span data-stu-id="880ab-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="880ab-565">XSRF/CSRF i Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="880ab-566">Nie trzeba pisać kodu do [weryfikacji przed fałszerstwem](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="880ab-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="880ab-567">Generowanie i sprawdzanie poprawności tokenów antysfałszowanych są automatycznie dołączane do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="880ab-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="880ab-568">Używanie układów, częściowych, szablonów i pomocników tagów z Razor Pages</span><span class="sxs-lookup"><span data-stu-id="880ab-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="880ab-569">Strony współpracują ze wszystkimi funkcjami aparatu widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="880ab-570">Układy, części, szablony, pomocników tagów, *_ViewStart. cshtml*, *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku konwencjonalnych widoków Razor.</span><span class="sxs-lookup"><span data-stu-id="880ab-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="880ab-571">Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="880ab-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="880ab-572">Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="880ab-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="880ab-573">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="880ab-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="880ab-574">Steruje układem każdej strony (chyba że strona nie jest częścią układu).</span><span class="sxs-lookup"><span data-stu-id="880ab-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="880ab-575">Importuje struktury HTML, takie jak JavaScript i stylesheets.</span><span class="sxs-lookup"><span data-stu-id="880ab-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="880ab-576">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="880ab-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="880ab-577">Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="880ab-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="880ab-578">Układ znajduje się w *stronie/w folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="880ab-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="880ab-579">Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="880ab-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="880ab-580">Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej strony Razor w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="880ab-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="880ab-581">Plik układu powinien przejść do *stron/folderu udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="880ab-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="880ab-582">Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="880ab-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="880ab-583">*Widoki/udostępnione* są wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="880ab-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="880ab-584">Razor Pages są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.</span><span class="sxs-lookup"><span data-stu-id="880ab-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="880ab-585">Widok wyszukiwania na stronie Razor zawiera folder *strony* .</span><span class="sxs-lookup"><span data-stu-id="880ab-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="880ab-586">Układy, szablony i częściowe, które są używane z kontrolerami MVC i konwencjonalnymi widokami Razor, *działają tylko*.</span><span class="sxs-lookup"><span data-stu-id="880ab-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="880ab-587">Dodaj plik *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="880ab-588">`@namespace`wyjaśniono w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="880ab-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="880ab-589">Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="880ab-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="880ab-590">Gdy `@namespace` dyrektywa jest używana jawnie na stronie:</span><span class="sxs-lookup"><span data-stu-id="880ab-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="880ab-591">Dyrektywa ustawia przestrzeń nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="880ab-592">`@model`Dyrektywa nie musi zawierać przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="880ab-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="880ab-593">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="880ab-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="880ab-594">Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="880ab-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="880ab-595">Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="880ab-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="880ab-596">Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="880ab-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="880ab-597">Wygenerowana przestrzeń nazw dla strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.</span><span class="sxs-lookup"><span data-stu-id="880ab-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="880ab-598">`@namespace`*działa również z konwencjonalnymi widokami Razor.*</span><span class="sxs-lookup"><span data-stu-id="880ab-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="880ab-599">Oryginalne *strony/Utwórz plik widoku. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="880ab-600">Zaktualizowane *strony/Utwórz plik widoku. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="880ab-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="880ab-601">[Razor Pages początkowy projekt](#rpvs17) zawiera elementy *pages/_ValidationScriptsPartial. cshtml*, które przechwytuje weryfikację po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="880ab-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="880ab-602">Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="880ab-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="880ab-603">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="880ab-603">URL generation for Pages</span></span>

<span data-ttu-id="880ab-604">`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="880ab-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="880ab-605">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="880ab-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="880ab-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="880ab-606">*/Pages*</span></span>

  * <span data-ttu-id="880ab-607">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="880ab-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="880ab-608">*/Customers*</span></span>

    * <span data-ttu-id="880ab-609">*Create. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="880ab-610">*Edytuj. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="880ab-611">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="880ab-611">*Index.cshtml*</span></span>

<span data-ttu-id="880ab-612">Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierują do *stron/index. cshtml* po powodzeniu.</span><span class="sxs-lookup"><span data-stu-id="880ab-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="880ab-613">Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="880ab-614">Ten ciąg `/Index` może służyć do generowania identyfikatorów URI na stronie *stron/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="880ab-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="880ab-615">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="880ab-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="880ab-616">Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="880ab-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="880ab-617">Powyższe przykłady generowania adresów URL oferują ulepszone opcje i możliwości funkcjonalne w porównaniu z zakodowana adresem URL.</span><span class="sxs-lookup"><span data-stu-id="880ab-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="880ab-618">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="880ab-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="880ab-619">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="880ab-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="880ab-620">W poniższej tabeli przedstawiono, która strona indeksu została wybrana z różnymi `RedirectToPage` parametrami *stron/Customers/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="880ab-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="880ab-621">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="880ab-621">RedirectToPage(x)</span></span>| <span data-ttu-id="880ab-622">Strona</span><span class="sxs-lookup"><span data-stu-id="880ab-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="880ab-623">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="880ab-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="880ab-624">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-624">*Pages/Index*</span></span> |
| <span data-ttu-id="880ab-625">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="880ab-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="880ab-626">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="880ab-627">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="880ab-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="880ab-628">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-628">*Pages/Index*</span></span> |
| <span data-ttu-id="880ab-629">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="880ab-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="880ab-630">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="880ab-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="880ab-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="880ab-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="880ab-632">`RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="880ab-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="880ab-633">Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą.</span><span class="sxs-lookup"><span data-stu-id="880ab-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="880ab-634">Jeśli używasz nazw względnych do łączenia między stronami w folderze, możesz zmienić nazwę tego folderu.</span><span class="sxs-lookup"><span data-stu-id="880ab-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="880ab-635">Wszystkie linki nadal działają (ponieważ nie zawierają nazwy folderu).</span><span class="sxs-lookup"><span data-stu-id="880ab-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="880ab-636">Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="880ab-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="880ab-637">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="880ab-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="880ab-638">ViewData — atrybut</span><span class="sxs-lookup"><span data-stu-id="880ab-638">ViewData attribute</span></span>

<span data-ttu-id="880ab-639">Dane można przekazywać do strony z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="880ab-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="880ab-640">Właściwości kontrolerów lub Razor modeli stron z `[ViewData]` atrybutem są przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="880ab-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="880ab-641">W poniższym przykładzie `AboutModel` zawiera właściwość, która jest `Title` oznaczona za pomocą `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="880ab-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="880ab-642">`Title`Właściwość jest ustawiona na tytuł strony informacje:</span><span class="sxs-lookup"><span data-stu-id="880ab-642">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="880ab-643">Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="880ab-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="880ab-644">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="880ab-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="880ab-645">TempData</span><span class="sxs-lookup"><span data-stu-id="880ab-645">TempData</span></span>

<span data-ttu-id="880ab-646">ASP.NET Core uwidacznia Właściwość [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="880ab-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="880ab-647">Ta właściwość przechowuje dane, dopóki nie zostanie odczytana.</span><span class="sxs-lookup"><span data-stu-id="880ab-647">This property stores data until it's read.</span></span> <span data-ttu-id="880ab-648">`Keep`Metody i `Peek` mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="880ab-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="880ab-649">`TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="880ab-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="880ab-650">Poniższy kod ustawia wartość `Message` użycia `TempData` :</span><span class="sxs-lookup"><span data-stu-id="880ab-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="880ab-651">W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="880ab-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="880ab-652">Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="880ab-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="880ab-653">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="880ab-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="880ab-654">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="880ab-654">Multiple handlers per page</span></span>

<span data-ttu-id="880ab-655">Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="880ab-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="880ab-656">Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="880ab-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="880ab-657">Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="880ab-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="880ab-658">`asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę.</span><span class="sxs-lookup"><span data-stu-id="880ab-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="880ab-659">`asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="880ab-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="880ab-660">Model strony:</span><span class="sxs-lookup"><span data-stu-id="880ab-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="880ab-661">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="880ab-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="880ab-662">Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="880ab-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="880ab-663">W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="880ab-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="880ab-664">Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="880ab-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="880ab-665">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="880ab-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="880ab-666">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="880ab-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="880ab-667">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="880ab-667">Custom routes</span></span>

<span data-ttu-id="880ab-668">Użyj `@page` dyrektywy, aby:</span><span class="sxs-lookup"><span data-stu-id="880ab-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="880ab-669">Określ trasę niestandardową dla strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-669">Specify a custom route to a page.</span></span> <span data-ttu-id="880ab-670">Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="880ab-671">Dołącz segmenty do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-671">Append segments to a page's default route.</span></span> <span data-ttu-id="880ab-672">Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="880ab-673">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="880ab-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="880ab-674">Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="880ab-675">Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="880ab-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="880ab-676">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="880ab-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="880ab-677">Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="880ab-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="880ab-678">Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="880ab-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="880ab-679">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="880ab-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="880ab-680">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="880ab-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="880ab-681">`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="880ab-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="880ab-682">Konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="880ab-682">Configuration and settings</span></span>

<span data-ttu-id="880ab-683">Aby skonfigurować opcje zaawansowane, użyj metody rozszerzenia `AddRazorPagesOptions` w konstruktorze MVC:</span><span class="sxs-lookup"><span data-stu-id="880ab-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="880ab-684">Obecnie można użyć, `RazorPagesOptions` Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="880ab-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="880ab-685">W przyszłości włączysz więcej rozszerzeń w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="880ab-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="880ab-686">Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="880ab-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="880ab-687">[Pobierz lub Wyświetl przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="880ab-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="880ab-688">Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="880ab-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="880ab-689">Określ, że Razor strony znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="880ab-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="880ab-690">Domyślnie Razor strony są umieszczane w katalogu */Pages* .</span><span class="sxs-lookup"><span data-stu-id="880ab-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="880ab-691">Dodaj [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikacji:</span><span class="sxs-lookup"><span data-stu-id="880ab-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="880ab-692">Określ, że Razor strony znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="880ab-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="880ab-693">Dodaj [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="880ab-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="880ab-694">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="880ab-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
