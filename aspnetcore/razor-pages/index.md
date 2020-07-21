---
title: Wprowadzenie do Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się Razor , jak strony w ASP.NET Core sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: e7e9bc76a2bba1c15b32c97f0f3629285f256499
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568708"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="babce-103">Wprowadzenie do Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="babce-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="babce-104">[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="babce-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="babce-105">Strony mogą sprawiać, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="babce-105"> Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="babce-106">Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="babce-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="babce-107">Ten dokument zawiera wprowadzenie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="babce-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="babce-108">Nie jest to samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="babce-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="babce-109">Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="babce-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="babce-110">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="babce-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="babce-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="babce-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="babce-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="babce-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="babce-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="babce-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="babce-114">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="babce-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="babce-115">Tworzenie Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="babce-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="babce-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="babce-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="babce-117">Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="babce-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="babce-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="babce-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="babce-119">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="babce-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="babce-120">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="babce-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="babce-121">Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="babce-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="babce-122">Page</span><span class="sxs-lookup"><span data-stu-id="babce-122"> Pages</span></span>

Razor<span data-ttu-id="babce-123">Strony są włączone w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="babce-123"> Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="babce-124">Weź pod uwagę podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="babce-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="babce-125">Poprzedni kod wygląda podobnie jak [ Razor plik widoku](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="babce-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="babce-126">Co sprawia, że jest to [`@page`](xref:mvc/views/razor#page) dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="babce-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="babce-127">`@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="babce-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="babce-128">`@page`musi być pierwszą Razor dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="babce-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="babce-129">`@page`wpływa na zachowanie innych [Razor](xref:mvc/views/razor) konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="babce-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> Razor<span data-ttu-id="babce-130">Nazwy plików stron mają sufiks *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="babce-130"> Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="babce-131">Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="babce-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="babce-132">Plik *Pages/index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="babce-133">Model strony *stron/index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="babce-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="babce-134">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak Razor plik stronicowania dołączony do *. cs* .</span><span class="sxs-lookup"><span data-stu-id="babce-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="babce-135">Na przykład Poprzednia Razor strona to *Pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="babce-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="babce-136">Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="babce-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="babce-137">Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="babce-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="babce-138">W poniższej tabeli przedstawiono Razor ścieżkę strony i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="babce-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="babce-139">Nazwa i ścieżka pliku</span><span class="sxs-lookup"><span data-stu-id="babce-139">File name and path</span></span>               | <span data-ttu-id="babce-140">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="babce-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="babce-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="babce-142">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="babce-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="babce-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="babce-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="babce-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="babce-146">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="babce-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="babce-147">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="babce-147">Notes:</span></span>

* <span data-ttu-id="babce-148">Środowisko uruchomieniowe domyślnie wyszukuje Razor pliki stron w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="babce-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="babce-149">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="babce-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="babce-150">Napisz podstawowy formularz</span><span class="sxs-lookup"><span data-stu-id="babce-150">Write a basic form</span></span>

Razor<span data-ttu-id="babce-151">Strony są przeznaczone do tworzenia wspólnych wzorców używanych z przeglądarkami sieci Web, które są łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="babce-151"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="babce-152">[Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w Razor klasie Page.</span><span class="sxs-lookup"><span data-stu-id="babce-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="babce-153">Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="babce-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="babce-154">Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .</span><span class="sxs-lookup"><span data-stu-id="babce-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="babce-155">Model danych:</span><span class="sxs-lookup"><span data-stu-id="babce-155">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="babce-156">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="babce-156">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="babce-157">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="babce-158">Model strony *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="babce-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="babce-159">Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="babce-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="babce-160">`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="babce-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="babce-161">Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="babce-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="babce-162">Ta separacja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="babce-162">This separation allows:</span></span>

* <span data-ttu-id="babce-163">Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="babce-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="babce-164">Testy jednostkowe</span><span class="sxs-lookup"><span data-stu-id="babce-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="babce-165">Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz).</span><span class="sxs-lookup"><span data-stu-id="babce-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="babce-166">Można dodać metody obsługi dla dowolnego zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="babce-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="babce-167">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="babce-167">The most common handlers are:</span></span>

* <span data-ttu-id="babce-168">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="babce-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="babce-169">W poprzednim kodzie `OnGet` Metoda wyświetla stronę *onmode. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="babce-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="babce-170">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="babce-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="babce-171">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="babce-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="babce-172">Poprzedni kod jest typowy dla Razor stron.</span><span class="sxs-lookup"><span data-stu-id="babce-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="babce-173">Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="babce-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="babce-174">`OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="babce-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="babce-175">Większość elementów podstawowych MVC, takich jak [powiązania modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation)i akcje, działa tak samo, jak w przypadku kontrolerów i Razor stron.</span><span class="sxs-lookup"><span data-stu-id="babce-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="babce-176">Poprzednia `OnPostAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="babce-176">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="babce-177">Podstawowy przepływ `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="babce-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="babce-178">Sprawdź, czy występują błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="babce-178">Check for validation errors.</span></span>

* <span data-ttu-id="babce-179">Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.</span><span class="sxs-lookup"><span data-stu-id="babce-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="babce-180">W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji.</span><span class="sxs-lookup"><span data-stu-id="babce-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="babce-181">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="babce-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="babce-182">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="babce-183">Renderowany kod HTML ze *stron/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="babce-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="babce-184">W poprzednim kodzie, ogłaszanie formularza:</span><span class="sxs-lookup"><span data-stu-id="babce-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="babce-185">Z prawidłowymi danymi:</span><span class="sxs-lookup"><span data-stu-id="babce-185">With valid data:</span></span>

  * <span data-ttu-id="babce-186">`OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="babce-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="babce-187">Metoda `RedirectToPage` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="babce-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="babce-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="babce-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="babce-189">Jest wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="babce-189">Is an action result.</span></span>
    * <span data-ttu-id="babce-190">Jest podobny do `RedirectToAction` lub `RedirectToRoute` (używany w kontrolerach i widokach).</span><span class="sxs-lookup"><span data-stu-id="babce-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="babce-191">Jest dostosowywany dla stron.</span><span class="sxs-lookup"><span data-stu-id="babce-191">Is customized for pages.</span></span> <span data-ttu-id="babce-192">W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="babce-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="babce-193">`RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="babce-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="babce-194">Z błędami walidacji, które są przesyłane do serwera:</span><span class="sxs-lookup"><span data-stu-id="babce-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="babce-195">`OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="babce-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="babce-196">Metoda `Page` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="babce-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="babce-197">Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` .</span><span class="sxs-lookup"><span data-stu-id="babce-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="babce-198">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="babce-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="babce-199">Metoda obsługi, która zwraca `void` renderowanie strony.</span><span class="sxs-lookup"><span data-stu-id="babce-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="babce-200">W poprzednim przykładzie, księgowanie formularza bez wartości powoduje, że [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zwraca wartość false.</span><span class="sxs-lookup"><span data-stu-id="babce-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="babce-201">W tym przykładzie na kliencie nie są wyświetlane błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="babce-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="babce-202">Przekazywanie błędów sprawdzania poprawności jest omówione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="babce-202">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="babce-203">Z błędami walidacji wykrytymi przez weryfikację po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="babce-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="babce-204">Dane **nie** są ogłaszane na serwerze.</span><span class="sxs-lookup"><span data-stu-id="babce-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="babce-205">Sprawdzanie poprawności po stronie klienta zostało wyjaśnione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="babce-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="babce-206">`Customer`Właściwość używa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atrybutu, aby zrezygnować z powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="babce-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="babce-207">`[BindProperty]`**nie** powinien być używany dla modeli zawierających właściwości, które nie powinny być zmieniane przez klienta.</span><span class="sxs-lookup"><span data-stu-id="babce-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="babce-208">Aby uzyskać więcej informacji, zobacz temat [przefinalizowanie](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="babce-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

Razor<span data-ttu-id="babce-209">Domyślnie strony powiążą właściwości tylko z `GET` niezleceniami.</span><span class="sxs-lookup"><span data-stu-id="babce-209"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="babce-210">Powiązanie z właściwościami eliminuje konieczność pisania kodu w celu przekonwertowania danych HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="babce-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="babce-211">Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="babce-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="babce-212">Przeglądanie pliku widoku *stron/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="babce-213">W poprzednim kodzie [pomocnik tagów wejściowych](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` wiąże `<input>` element HTML z `Customer.Name` wyrażeniem modelu.</span><span class="sxs-lookup"><span data-stu-id="babce-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="babce-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)udostępnia pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="babce-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="babce-215">Strona główna</span><span class="sxs-lookup"><span data-stu-id="babce-215">The home page</span></span>

<span data-ttu-id="babce-216">*Index. cshtml* to Strona główna:</span><span class="sxs-lookup"><span data-stu-id="babce-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="babce-217">Skojarzona `PageModel` Klasa (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="babce-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="babce-218">Plik *index. cshtml* zawiera następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="babce-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="babce-219">`<a /a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji.</span><span class="sxs-lookup"><span data-stu-id="babce-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="babce-220">Link zawiera dane trasy z IDENTYFIKATORem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="babce-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="babce-221">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="babce-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="babce-222">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="babce-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="babce-223">Plik *index. cshtml* zawiera znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="babce-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="babce-224">Renderowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="babce-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="babce-225">Gdy przycisk Usuń jest renderowany w języku HTML, jego [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) zawiera parametry dla:</span><span class="sxs-lookup"><span data-stu-id="babce-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="babce-226">Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.</span><span class="sxs-lookup"><span data-stu-id="babce-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="babce-227">`handler`, Określony przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="babce-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="babce-228">Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza.</span><span class="sxs-lookup"><span data-stu-id="babce-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="babce-229">Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="babce-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="babce-230">Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="babce-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="babce-231">Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="babce-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="babce-232">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="babce-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="babce-233">Pobiera `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="babce-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="babce-234">Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="babce-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="babce-235">Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty, a baza danych zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="babce-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="babce-236">Wywołania <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> do przekierowania na stronę indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="babce-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="babce-237">Plik Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="babce-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="babce-238">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="babce-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="babce-239">Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="babce-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="babce-240">Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="babce-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="babce-241">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="babce-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="babce-242">Plik *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="babce-242">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="babce-243">Walidacja</span><span class="sxs-lookup"><span data-stu-id="babce-243">Validation</span></span>

<span data-ttu-id="babce-244">Reguły walidacji:</span><span class="sxs-lookup"><span data-stu-id="babce-244">Validation rules:</span></span>

* <span data-ttu-id="babce-245">Są deklaratywnie określone w klasie modelu.</span><span class="sxs-lookup"><span data-stu-id="babce-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="babce-246">Są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="babce-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="babce-247"><xref:System.ComponentModel.DataAnnotations>Przestrzeń nazw zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="babce-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="babce-248">Adnotacje DataAnnotation zawierają również atrybuty formatowania, takie jak [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) Pomoc dotycząca formatowania i nie zapewniają weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="babce-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="babce-249">Rozważmy `Customer` model:</span><span class="sxs-lookup"><span data-stu-id="babce-249">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="babce-250">Za pomocą następującego pliku widoku *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="babce-251">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="babce-251">The preceding code:</span></span>

* <span data-ttu-id="babce-252">Obejmuje skrypty sprawdzania poprawności jQuery i jQuery.</span><span class="sxs-lookup"><span data-stu-id="babce-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="babce-253">Używa `<div />` `<span />` [pomocników tagów](xref:mvc/views/tag-helpers/intro) i do włączania:</span><span class="sxs-lookup"><span data-stu-id="babce-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="babce-254">Sprawdzanie poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="babce-254">Client-side validation.</span></span>
  * <span data-ttu-id="babce-255">Renderowanie błędów walidacji.</span><span class="sxs-lookup"><span data-stu-id="babce-255">Validation error rendering.</span></span>

* <span data-ttu-id="babce-256">Generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="babce-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="babce-257">Opublikowanie formularza tworzenia bez wartości nazwa powoduje wyświetlenie komunikatu o błędzie "Nazwa pola jest wymagana."</span><span class="sxs-lookup"><span data-stu-id="babce-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="babce-258">w formularzu.</span><span class="sxs-lookup"><span data-stu-id="babce-258">on the form.</span></span> <span data-ttu-id="babce-259">Jeśli na kliencie jest włączona obsługa języka JavaScript, przeglądarka wyświetli komunikat o błędzie bez publikowania na serwerze.</span><span class="sxs-lookup"><span data-stu-id="babce-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="babce-260">Ten `[StringLength(10)]` atrybut jest generowany `data-val-length-max="10"` na RENDEROWANYM kodzie HTML.</span><span class="sxs-lookup"><span data-stu-id="babce-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="babce-261">`data-val-length-max`zapobiega wprowadzaniu przez przeglądarki więcej niż określoną maksymalną długość.</span><span class="sxs-lookup"><span data-stu-id="babce-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="babce-262">Jeśli jest używane narzędzie, takie jak [programu Fiddler](https://www.telerik.com/fiddler) , do edytowania i powtarzania wpisu:</span><span class="sxs-lookup"><span data-stu-id="babce-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="babce-263">O nazwie dłuższej niż 10.</span><span class="sxs-lookup"><span data-stu-id="babce-263">With the name longer than 10.</span></span>
* <span data-ttu-id="babce-264">Komunikat o błędzie "Nazwa pola musi być ciągiem o maksymalnej długości 10".</span><span class="sxs-lookup"><span data-stu-id="babce-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="babce-265">jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="babce-265">is returned.</span></span>

<span data-ttu-id="babce-266">Rozważmy następujący `Movie` model:</span><span class="sxs-lookup"><span data-stu-id="babce-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="babce-267">Atrybuty walidacji określają zachowanie do wymuszania na właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="babce-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="babce-268">`Required`Atrybuty i `MinimumLength` wskazują, że właściwość musi mieć wartość, ale nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.</span><span class="sxs-lookup"><span data-stu-id="babce-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="babce-269">Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="babce-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="babce-270">W poprzednim kodzie "gatunek":</span><span class="sxs-lookup"><span data-stu-id="babce-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="babce-271">Należy używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="babce-271">Must only use letters.</span></span>
  * <span data-ttu-id="babce-272">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="babce-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="babce-273">Odstępy, cyfry i znaki specjalne są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="babce-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="babce-274">`RegularExpression`"Ocena":</span><span class="sxs-lookup"><span data-stu-id="babce-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="babce-275">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="babce-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="babce-276">Zezwala na znaki specjalne i cyfry w kolejnych odstępach.</span><span class="sxs-lookup"><span data-stu-id="babce-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="babce-277">"PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".</span><span class="sxs-lookup"><span data-stu-id="babce-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="babce-278">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="babce-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="babce-279">`StringLength`Atrybut ustawia maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.</span><span class="sxs-lookup"><span data-stu-id="babce-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="babce-280">Typy wartości (takie jak `decimal` ,,, `int` `float` `DateTime` ) są z założenia wymagane i nie wymagają `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="babce-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="babce-281">Na stronie Tworzenie dla `Movie` modelu są wyświetlane błędy z nieprawidłowymi wartościami:</span><span class="sxs-lookup"><span data-stu-id="babce-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="babce-283">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="babce-283">For more information, see:</span></span>

* [<span data-ttu-id="babce-284">Dodawanie walidacji do aplikacji filmowej</span><span class="sxs-lookup"><span data-stu-id="babce-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="babce-285">[Walidacja modelu w ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="babce-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="babce-286">Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="babce-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="babce-287">`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="babce-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="babce-288">W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="babce-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="babce-289">Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:</span><span class="sxs-lookup"><span data-stu-id="babce-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor<span data-ttu-id="babce-290">Strony powracają do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="babce-290"> Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="babce-291">XSRF/CSRF i Razor strony</span><span class="sxs-lookup"><span data-stu-id="babce-291">XSRF/CSRF and Razor Pages</span></span>

Razor<span data-ttu-id="babce-292">Strony są chronione przez [weryfikację przed fałszerstwem](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="babce-292"> Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="babce-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="babce-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="babce-294">Używanie układów, częściowych, szablonów i pomocników tagów ze Razor stronami</span><span class="sxs-lookup"><span data-stu-id="babce-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="babce-295">Strony współpracują ze wszystkimi możliwościami Razor aparatu widoku.</span><span class="sxs-lookup"><span data-stu-id="babce-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="babce-296">Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*i *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku widoków konwencjonalnych Razor .</span><span class="sxs-lookup"><span data-stu-id="babce-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="babce-297">Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="babce-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="babce-298">Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="babce-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="babce-299">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="babce-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="babce-300">Steruje układem każdej strony (chyba że strona nie jest częścią układu).</span><span class="sxs-lookup"><span data-stu-id="babce-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="babce-301">Importuje struktury HTML, takie jak JavaScript i stylesheets.</span><span class="sxs-lookup"><span data-stu-id="babce-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="babce-302">Zawartość Razor strony jest renderowana, gdzie `@RenderBody()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="babce-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="babce-303">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="babce-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="babce-304">Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="babce-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="babce-305">Układ znajduje się w *stronie/w folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="babce-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="babce-306">Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="babce-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="babce-307">Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej Razor strony w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="babce-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="babce-308">Plik układu powinien przejść do *stron/folderu udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="babce-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="babce-309">Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="babce-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="babce-310">*Widoki/udostępnione* są wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="babce-310">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="babce-311">Strony są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.</span><span class="sxs-lookup"><span data-stu-id="babce-311"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="babce-312">Widok wyszukiwania na Razor stronie zawiera folder *strony* .</span><span class="sxs-lookup"><span data-stu-id="babce-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="babce-313">Układy, szablony i częściowe używane razem z kontrolerami MVC i konwencjonalnymi Razor widokami *działają tylko*.</span><span class="sxs-lookup"><span data-stu-id="babce-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="babce-314">Dodaj plik *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="babce-315">`@namespace`wyjaśniono w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="babce-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="babce-316">Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="babce-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="babce-317">`@namespace`Dyrektywa ustawiona na stronie:</span><span class="sxs-lookup"><span data-stu-id="babce-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="babce-318">`@namespace`Dyrektywa ustawia przestrzeń nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="babce-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="babce-319">`@model`Dyrektywa nie musi zawierać przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="babce-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="babce-320">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="babce-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="babce-321">Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="babce-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="babce-322">Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="babce-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="babce-323">Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="babce-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="babce-324">Wygenerowana przestrzeń nazw strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.</span><span class="sxs-lookup"><span data-stu-id="babce-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="babce-325">`@namespace`*działa również z konwencjonalnymi Razor widokami.*</span><span class="sxs-lookup"><span data-stu-id="babce-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="babce-326">Rozważ użycie pliku widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="babce-327">Zaktualizowane *strony/Create. cshtml* plik widoku z *_ViewImports. cshtml* i poprzedni plik układu:</span><span class="sxs-lookup"><span data-stu-id="babce-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="babce-328">W poprzednim kodzie, *_ViewImports. cshtml* zaimportował przestrzeń nazw i pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="babce-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="babce-329">Plik układu zaimportował pliki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="babce-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="babce-330">Na stronie [ Razor startowej projektu](#rpvs17) znajdują się *strony/_ValidationScriptsPartial. cshtml*, które przechwytuje walidację po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="babce-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="babce-331">Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="babce-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="babce-332">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="babce-332">URL generation for Pages</span></span>

<span data-ttu-id="babce-333">`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="babce-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="babce-334">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="babce-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="babce-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="babce-335">*/Pages*</span></span>

  * <span data-ttu-id="babce-336">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="babce-337">*Privacy. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="babce-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="babce-338">*/Customers*</span></span>

    * <span data-ttu-id="babce-339">*Create. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="babce-340">*Edytuj. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="babce-341">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-341">*Index.cshtml*</span></span>

<span data-ttu-id="babce-342">Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierowywać do *stron/Customers/index. cshtml* po powodzeniu.</span><span class="sxs-lookup"><span data-stu-id="babce-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="babce-343">Ciąg `./Index` jest względną nazwą strony używaną w celu uzyskania dostępu do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="babce-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="babce-344">Służy do generowania adresów URL na stronie *strony/klienci/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="babce-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="babce-345">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="babce-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="babce-346">Bezwzględna nazwa strony `/Index` służy do generowania adresów URL na stronie *stron/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="babce-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="babce-347">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="babce-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="babce-348">Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="babce-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="babce-349">Powyższe przykłady generowania adresów URL oferują udoskonalone Opcje i możliwości funkcjonalne w porównaniu z zakodowanym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="babce-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="babce-350">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="babce-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="babce-351">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="babce-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="babce-352">W poniższej tabeli przedstawiono, która strona indeksu została wybrana przy użyciu różnych `RedirectToPage` parametrów na stronie */Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="babce-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="babce-353">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="babce-353">RedirectToPage(x)</span></span>| <span data-ttu-id="babce-354">Strona</span><span class="sxs-lookup"><span data-stu-id="babce-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="babce-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="babce-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="babce-356">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-356">*Pages/Index*</span></span> |
| <span data-ttu-id="babce-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="babce-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="babce-358">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="babce-359">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="babce-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="babce-360">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-360">*Pages/Index*</span></span> |
| <span data-ttu-id="babce-361">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="babce-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="babce-362">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="babce-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="babce-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="babce-364">`RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="babce-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="babce-365">Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą.</span><span class="sxs-lookup"><span data-stu-id="babce-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="babce-366">Gdy nazwy względne są używane do łączenia między stronami w folderze:</span><span class="sxs-lookup"><span data-stu-id="babce-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="babce-367">Zmiana nazwy folderu nie powoduje zerwania linków względnych.</span><span class="sxs-lookup"><span data-stu-id="babce-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="babce-368">Linki nie są przerwane, ponieważ nie zawierają nazwy folderu.</span><span class="sxs-lookup"><span data-stu-id="babce-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="babce-369">Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="babce-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="babce-370">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas> i <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="babce-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="babce-371">ViewData — atrybut</span><span class="sxs-lookup"><span data-stu-id="babce-371">ViewData attribute</span></span>

<span data-ttu-id="babce-372">Dane można przekazywać do strony za pomocą <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="babce-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="babce-373">Właściwości z `[ViewData]` atrybutem mają przechowywane i ładowane wartości z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="babce-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="babce-374">W poniższym przykładzie `AboutModel` stosuje `[ViewData]` atrybut do `Title` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="babce-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="babce-375">Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="babce-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="babce-376">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="babce-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="babce-377">TempData</span><span class="sxs-lookup"><span data-stu-id="babce-377">TempData</span></span>

<span data-ttu-id="babce-378">ASP.NET Core uwidacznia <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="babce-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="babce-379">Ta właściwość przechowuje dane, dopóki nie zostanie odczytana.</span><span class="sxs-lookup"><span data-stu-id="babce-379">This property stores data until it's read.</span></span> <span data-ttu-id="babce-380"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody i <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="babce-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="babce-381">`TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="babce-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="babce-382">Poniższy kod ustawia wartość `Message` użycia `TempData` :</span><span class="sxs-lookup"><span data-stu-id="babce-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="babce-383">W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="babce-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="babce-384">Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="babce-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="babce-385">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="babce-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="babce-386">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="babce-386">Multiple handlers per page</span></span>

<span data-ttu-id="babce-387">Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="babce-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="babce-388">Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="babce-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="babce-389">Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="babce-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="babce-390">`asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę.</span><span class="sxs-lookup"><span data-stu-id="babce-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="babce-391">`asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="babce-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="babce-392">Model strony:</span><span class="sxs-lookup"><span data-stu-id="babce-392">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="babce-393">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="babce-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="babce-394">Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="babce-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="babce-395">W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="babce-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="babce-396">Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="babce-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="babce-397">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="babce-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="babce-398">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="babce-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="babce-399">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="babce-399">Custom routes</span></span>

<span data-ttu-id="babce-400">Użyj `@page` dyrektywy, aby:</span><span class="sxs-lookup"><span data-stu-id="babce-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="babce-401">Określ trasę niestandardową dla strony.</span><span class="sxs-lookup"><span data-stu-id="babce-401">Specify a custom route to a page.</span></span> <span data-ttu-id="babce-402">Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="babce-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="babce-403">Dołącz segmenty do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="babce-403">Append segments to a page's default route.</span></span> <span data-ttu-id="babce-404">Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="babce-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="babce-405">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="babce-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="babce-406">Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="babce-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="babce-407">Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="babce-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="babce-408">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="babce-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="babce-409">Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="babce-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="babce-410">Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="babce-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="babce-411">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="babce-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="babce-412">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="babce-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="babce-413">`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="babce-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="babce-414">Zaawansowana konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="babce-414">Advanced configuration and settings</span></span>

<span data-ttu-id="babce-415">Konfiguracja i ustawienia w poniższych sekcjach nie są wymagane przez większość aplikacji.</span><span class="sxs-lookup"><span data-stu-id="babce-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="babce-416">Aby skonfigurować opcje zaawansowane, użyj <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> przeciążenia, które konfiguruje <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="babce-416">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="babce-417">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="babce-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="babce-418">Aby uzyskać więcej informacji na temat Konwencji, zobacz [ Razor strony konwencje autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="babce-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="babce-419">Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="babce-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="babce-420">Określ, że Razor strony znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="babce-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="babce-421">Domyślnie Razor strony są umieszczane w katalogu */Pages* .</span><span class="sxs-lookup"><span data-stu-id="babce-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="babce-422">Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> aplikacji:</span><span class="sxs-lookup"><span data-stu-id="babce-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="babce-423">Określ, że Razor strony znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="babce-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="babce-424">Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="babce-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="babce-425">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="babce-425">Additional resources</span></span>

* <span data-ttu-id="babce-426">Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="babce-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="babce-427">[Autoryzuj atrybut i Razor strony](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="babce-427">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* [<span data-ttu-id="babce-428">Pobieranie lub wyświetlanie przykładowego kodu</span><span class="sxs-lookup"><span data-stu-id="babce-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="babce-429">[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="babce-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="babce-430">Strony to nowy aspekt ASP.NET Core MVC, który sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i bardziej produktywne.</span><span class="sxs-lookup"><span data-stu-id="babce-430"> Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="babce-431">Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="babce-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="babce-432">Ten dokument zawiera wprowadzenie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="babce-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="babce-433">Nie jest to samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="babce-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="babce-434">Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="babce-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="babce-435">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="babce-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="babce-436">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="babce-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="babce-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="babce-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="babce-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="babce-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="babce-439">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="babce-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="babce-440">Tworzenie Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="babce-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="babce-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="babce-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="babce-442">Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="babce-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="babce-443">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="babce-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="babce-444">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="babce-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="babce-445">Otwórz wygenerowany plik *csproj* z Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="babce-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="babce-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="babce-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="babce-447">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="babce-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="babce-448">Page</span><span class="sxs-lookup"><span data-stu-id="babce-448"> Pages</span></span>

Razor<span data-ttu-id="babce-449">Strony są włączone w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="babce-449"> Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="babce-450">Weź pod uwagę podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="babce-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="babce-451">Poprzedni kod wygląda podobnie jak [ Razor plik widoku](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="babce-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="babce-452">Co sprawia, że jest to `@page` dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="babce-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="babce-453">`@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="babce-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="babce-454">`@page`musi być pierwszą Razor dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="babce-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="babce-455">`@page`wpływa na zachowanie innych Razor konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="babce-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="babce-456">Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="babce-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="babce-457">Plik *Pages/index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="babce-458">Model strony *stron/index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="babce-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="babce-459">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak Razor plik stronicowania dołączony do *. cs* .</span><span class="sxs-lookup"><span data-stu-id="babce-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="babce-460">Na przykład Poprzednia Razor strona to *Pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="babce-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="babce-461">Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="babce-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="babce-462">Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="babce-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="babce-463">W poniższej tabeli przedstawiono Razor ścieżkę strony i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="babce-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="babce-464">Nazwa i ścieżka pliku</span><span class="sxs-lookup"><span data-stu-id="babce-464">File name and path</span></span>               | <span data-ttu-id="babce-465">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="babce-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="babce-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="babce-467">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="babce-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="babce-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="babce-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="babce-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="babce-471">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="babce-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="babce-472">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="babce-472">Notes:</span></span>

* <span data-ttu-id="babce-473">Środowisko uruchomieniowe domyślnie wyszukuje Razor pliki stron w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="babce-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="babce-474">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="babce-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="babce-475">Napisz podstawowy formularz</span><span class="sxs-lookup"><span data-stu-id="babce-475">Write a basic form</span></span>

Razor<span data-ttu-id="babce-476">Strony są przeznaczone do tworzenia wspólnych wzorców używanych z przeglądarkami sieci Web, które są łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="babce-476"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="babce-477">[Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w Razor klasie Page.</span><span class="sxs-lookup"><span data-stu-id="babce-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="babce-478">Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="babce-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="babce-479">Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="babce-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="babce-480">Model danych:</span><span class="sxs-lookup"><span data-stu-id="babce-480">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="babce-481">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="babce-481">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="babce-482">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="babce-483">Model strony *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="babce-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="babce-484">Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="babce-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="babce-485">`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="babce-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="babce-486">Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="babce-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="babce-487">Ta separacja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="babce-487">This separation allows:</span></span>

* <span data-ttu-id="babce-488">Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="babce-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="babce-489">[Testowanie jednostkowe](xref:test/razor-pages-tests) stron.</span><span class="sxs-lookup"><span data-stu-id="babce-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="babce-490">Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz).</span><span class="sxs-lookup"><span data-stu-id="babce-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="babce-491">Metody procedury obsługi można dodać dla dowolnego czasownika HTTP.</span><span class="sxs-lookup"><span data-stu-id="babce-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="babce-492">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="babce-492">The most common handlers are:</span></span>

* <span data-ttu-id="babce-493">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="babce-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="babce-494">Przykład [OnGet](#OnGet) .</span><span class="sxs-lookup"><span data-stu-id="babce-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="babce-495">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="babce-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="babce-496">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="babce-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="babce-497">Poprzedni kod jest typowy dla Razor stron.</span><span class="sxs-lookup"><span data-stu-id="babce-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="babce-498">Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="babce-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="babce-499">`OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="babce-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="babce-500">Większość elementów podstawowych MVC, takich jak [powiązanie modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation), [Walidacja](xref:mvc/models/validation)i wyniki akcji, jest udostępniana.</span><span class="sxs-lookup"><span data-stu-id="babce-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="babce-501">Poprzednia `OnPostAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="babce-501">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="babce-502">Podstawowy przepływ `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="babce-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="babce-503">Sprawdź, czy występują błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="babce-503">Check for validation errors.</span></span>

* <span data-ttu-id="babce-504">Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.</span><span class="sxs-lookup"><span data-stu-id="babce-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="babce-505">W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji.</span><span class="sxs-lookup"><span data-stu-id="babce-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="babce-506">Walidacja po stronie klienta jest taka sama jak w przypadku tradycyjnych ASP.NET Core aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="babce-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="babce-507">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="babce-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="babce-508">Po pomyślnym wprowadzeniu danych `OnPostAsync` Metoda obsługi wywołuje `RedirectToPage` metodę pomocnika zwracającą wystąpienie elementu `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="babce-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="babce-509">`RedirectToPage`to nowy wynik akcji, podobny do `RedirectToAction` lub `RedirectToRoute` , ale dostosowany do stron.</span><span class="sxs-lookup"><span data-stu-id="babce-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="babce-510">W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="babce-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="babce-511">`RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="babce-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="babce-512">Gdy przesłany formularz ma błędy walidacji (które są przekazywane do serwera), `OnPostAsync` Metoda obsługi wywołuje `Page` metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="babce-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="babce-513">Metoda `Page` zwraca wystąpienie klasy `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="babce-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="babce-514">Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` .</span><span class="sxs-lookup"><span data-stu-id="babce-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="babce-515">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="babce-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="babce-516">Metoda obsługi, która zwraca `void` renderowanie strony.</span><span class="sxs-lookup"><span data-stu-id="babce-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="babce-517">`Customer`Właściwość używa `[BindProperty]` atrybutu, aby zrezygnować z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="babce-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor<span data-ttu-id="babce-518">Domyślnie strony powiążą właściwości tylko z `GET` niezleceniami.</span><span class="sxs-lookup"><span data-stu-id="babce-518"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="babce-519">Utworzenie powiązania z właściwościami może zmniejszyć ilość kodu, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="babce-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="babce-520">Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="babce-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="babce-521">Strona główna (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="babce-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="babce-522">Skojarzona `PageModel` Klasa (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="babce-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="babce-523">Plik *index. cshtml* zawiera następujące znaczniki, aby utworzyć łącze do edycji dla każdego kontaktu:</span><span class="sxs-lookup"><span data-stu-id="babce-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="babce-524">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji.</span><span class="sxs-lookup"><span data-stu-id="babce-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="babce-525">Link zawiera dane trasy z IDENTYFIKATORem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="babce-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="babce-526">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="babce-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="babce-527">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="babce-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="babce-528">Pomocnicy tagów są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="babce-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="babce-529">Plik *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="babce-530">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="babce-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="babce-531">Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="babce-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="babce-532">Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="babce-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="babce-533">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="babce-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="babce-534">Plik *stron/Edytuj. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="babce-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="babce-535">Plik *index. cshtml* zawiera również znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="babce-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="babce-536">Gdy przycisk Usuń jest renderowany w języku HTML, jego `formaction` Parametry obejmują:</span><span class="sxs-lookup"><span data-stu-id="babce-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="babce-537">Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.</span><span class="sxs-lookup"><span data-stu-id="babce-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="babce-538">`handler`Określony przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="babce-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="babce-539">Oto przykład renderowanego przycisku usuwania z IDENTYFIKATORem kontaktu klienta `1` :</span><span class="sxs-lookup"><span data-stu-id="babce-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="babce-540">Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza.</span><span class="sxs-lookup"><span data-stu-id="babce-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="babce-541">Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="babce-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="babce-542">Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="babce-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="babce-543">Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="babce-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="babce-544">Poniższy kod ilustruje `OnPostDeleteAsync` procedurę obsługi:</span><span class="sxs-lookup"><span data-stu-id="babce-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="babce-545">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="babce-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="babce-546">Akceptuje `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="babce-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="babce-547">Jeśli na stronie *index. cshtml* znajduje się ograniczenie routingu `"{id:int?}"` , `id` będą pochodzić z danych tras.</span><span class="sxs-lookup"><span data-stu-id="babce-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="babce-548">Dane trasy dla programu `id` są określone w identyfikatorze URI, takich jak `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="babce-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="babce-549">Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="babce-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="babce-550">Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty z listy kontaktów klientów.</span><span class="sxs-lookup"><span data-stu-id="babce-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="babce-551">Baza danych jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="babce-551">The database is updated.</span></span>
* <span data-ttu-id="babce-552">Wywołania `RedirectToPage` do przekierowania na stronę indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="babce-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="babce-553">Oznacz właściwości strony jako wymagane</span><span class="sxs-lookup"><span data-stu-id="babce-553">Mark page properties as required</span></span>

<span data-ttu-id="babce-554">Właściwości na stronie `PageModel` można oznaczyć przy użyciu [wymaganego](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atrybutu:</span><span class="sxs-lookup"><span data-stu-id="babce-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="babce-555">Aby uzyskać więcej informacji, zobacz [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="babce-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="babce-556">Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="babce-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="babce-557">`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="babce-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="babce-558">W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="babce-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="babce-559">Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:</span><span class="sxs-lookup"><span data-stu-id="babce-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="babce-560">W ASP.NET Core 2,1 lub nowszej strony wracają Razor do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="babce-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="babce-561">To zachowanie jest włączane przez wywołanie [SetCompatibilityVersion](xref:mvc/compatibility-version) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="babce-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="babce-562">Szablony domyślne generują `SetCompatibilityVersion` wywołanie w ASP.NET Core 2,1 i 2,2.</span><span class="sxs-lookup"><span data-stu-id="babce-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="babce-563">`SetCompatibilityVersion`efektywnie ustawia Razor opcję strony `AllowMappingHeadRequestsToGetHandler` na `true` .</span><span class="sxs-lookup"><span data-stu-id="babce-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="babce-564">Zamiast korzystać z wszystkich zachowań w programie `SetCompatibilityVersion` , można jawnie zrezygnować z *określonych* zachowań.</span><span class="sxs-lookup"><span data-stu-id="babce-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="babce-565">Poniższy kod pozwala na umożliwienie `HEAD` mapowania żądań do `OnGet` programu obsługi:</span><span class="sxs-lookup"><span data-stu-id="babce-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="babce-566">XSRF/CSRF i Razor strony</span><span class="sxs-lookup"><span data-stu-id="babce-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="babce-567">Nie trzeba pisać kodu do [weryfikacji przed fałszerstwem](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="babce-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="babce-568">Generowanie i sprawdzanie poprawności tokenów antysfałszowanych są automatycznie dołączane do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="babce-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="babce-569">Używanie układów, częściowych, szablonów i pomocników tagów ze Razor stronami</span><span class="sxs-lookup"><span data-stu-id="babce-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="babce-570">Strony współpracują ze wszystkimi możliwościami Razor aparatu widoku.</span><span class="sxs-lookup"><span data-stu-id="babce-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="babce-571">Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*, *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku Razor widoków konwencjonalnych.</span><span class="sxs-lookup"><span data-stu-id="babce-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="babce-572">Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="babce-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="babce-573">Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="babce-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="babce-574">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="babce-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="babce-575">Steruje układem każdej strony (chyba że strona nie jest częścią układu).</span><span class="sxs-lookup"><span data-stu-id="babce-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="babce-576">Importuje struktury HTML, takie jak JavaScript i stylesheets.</span><span class="sxs-lookup"><span data-stu-id="babce-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="babce-577">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="babce-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="babce-578">Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="babce-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="babce-579">Układ znajduje się w *stronie/w folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="babce-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="babce-580">Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="babce-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="babce-581">Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej Razor strony w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="babce-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="babce-582">Plik układu powinien przejść do *stron/folderu udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="babce-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="babce-583">Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="babce-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="babce-584">*Widoki/udostępnione* są wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="babce-584">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="babce-585">Strony są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.</span><span class="sxs-lookup"><span data-stu-id="babce-585"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="babce-586">Widok wyszukiwania na Razor stronie zawiera folder *strony* .</span><span class="sxs-lookup"><span data-stu-id="babce-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="babce-587">Układy, szablony i częściowe, które są używane z kontrolerami MVC i konwencjonalnymi Razor widokami *działają tylko*.</span><span class="sxs-lookup"><span data-stu-id="babce-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="babce-588">Dodaj plik *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="babce-589">`@namespace`wyjaśniono w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="babce-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="babce-590">Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="babce-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="babce-591">Gdy `@namespace` dyrektywa jest używana jawnie na stronie:</span><span class="sxs-lookup"><span data-stu-id="babce-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="babce-592">Dyrektywa ustawia przestrzeń nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="babce-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="babce-593">`@model`Dyrektywa nie musi zawierać przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="babce-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="babce-594">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="babce-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="babce-595">Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="babce-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="babce-596">Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="babce-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="babce-597">Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="babce-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="babce-598">Wygenerowana przestrzeń nazw strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.</span><span class="sxs-lookup"><span data-stu-id="babce-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="babce-599">`@namespace`*działa również z konwencjonalnymi Razor widokami.*</span><span class="sxs-lookup"><span data-stu-id="babce-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="babce-600">Oryginalne *strony/Utwórz plik widoku. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="babce-601">Zaktualizowane *strony/Utwórz plik widoku. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="babce-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="babce-602">Na stronie [ Razor startowej projektu](#rpvs17) znajdują się *strony/_ValidationScriptsPartial. cshtml*, które przechwytuje walidację po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="babce-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="babce-603">Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="babce-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="babce-604">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="babce-604">URL generation for Pages</span></span>

<span data-ttu-id="babce-605">`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="babce-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="babce-606">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="babce-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="babce-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="babce-607">*/Pages*</span></span>

  * <span data-ttu-id="babce-608">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="babce-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="babce-609">*/Customers*</span></span>

    * <span data-ttu-id="babce-610">*Create. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="babce-611">*Edytuj. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="babce-612">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="babce-612">*Index.cshtml*</span></span>

<span data-ttu-id="babce-613">Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierują do *stron/index. cshtml* po powodzeniu.</span><span class="sxs-lookup"><span data-stu-id="babce-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="babce-614">Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="babce-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="babce-615">Ten ciąg `/Index` może służyć do generowania identyfikatorów URI na stronie *stron/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="babce-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="babce-616">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="babce-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="babce-617">Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="babce-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="babce-618">Powyższe przykłady generowania adresów URL oferują ulepszone opcje i możliwości funkcjonalne w porównaniu z zakodowana adresem URL.</span><span class="sxs-lookup"><span data-stu-id="babce-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="babce-619">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="babce-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="babce-620">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="babce-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="babce-621">W poniższej tabeli przedstawiono, która strona indeksu została wybrana z różnymi `RedirectToPage` parametrami *stron/Customers/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="babce-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="babce-622">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="babce-622">RedirectToPage(x)</span></span>| <span data-ttu-id="babce-623">Strona</span><span class="sxs-lookup"><span data-stu-id="babce-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="babce-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="babce-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="babce-625">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-625">*Pages/Index*</span></span> |
| <span data-ttu-id="babce-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="babce-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="babce-627">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="babce-628">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="babce-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="babce-629">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-629">*Pages/Index*</span></span> |
| <span data-ttu-id="babce-630">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="babce-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="babce-631">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="babce-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="babce-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="babce-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="babce-633">`RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="babce-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="babce-634">Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą.</span><span class="sxs-lookup"><span data-stu-id="babce-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="babce-635">Jeśli używasz nazw względnych do łączenia między stronami w folderze, możesz zmienić nazwę tego folderu.</span><span class="sxs-lookup"><span data-stu-id="babce-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="babce-636">Wszystkie linki nadal działają (ponieważ nie zawierają nazwy folderu).</span><span class="sxs-lookup"><span data-stu-id="babce-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="babce-637">Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="babce-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="babce-638">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="babce-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="babce-639">ViewData — atrybut</span><span class="sxs-lookup"><span data-stu-id="babce-639">ViewData attribute</span></span>

<span data-ttu-id="babce-640">Dane można przekazywać do strony z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="babce-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="babce-641">Właściwości kontrolerów lub Razor modeli stron z `[ViewData]` atrybutem są przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="babce-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="babce-642">W poniższym przykładzie `AboutModel` zawiera właściwość, która jest `Title` oznaczona za pomocą `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="babce-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="babce-643">`Title`Właściwość jest ustawiona na tytuł strony informacje:</span><span class="sxs-lookup"><span data-stu-id="babce-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="babce-644">Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="babce-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="babce-645">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="babce-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="babce-646">TempData</span><span class="sxs-lookup"><span data-stu-id="babce-646">TempData</span></span>

<span data-ttu-id="babce-647">ASP.NET Core uwidacznia Właściwość [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="babce-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="babce-648">Ta właściwość przechowuje dane, dopóki nie zostanie odczytana.</span><span class="sxs-lookup"><span data-stu-id="babce-648">This property stores data until it's read.</span></span> <span data-ttu-id="babce-649">`Keep`Metody i `Peek` mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="babce-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="babce-650">`TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="babce-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="babce-651">Poniższy kod ustawia wartość `Message` użycia `TempData` :</span><span class="sxs-lookup"><span data-stu-id="babce-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="babce-652">W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="babce-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="babce-653">Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="babce-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="babce-654">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="babce-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="babce-655">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="babce-655">Multiple handlers per page</span></span>

<span data-ttu-id="babce-656">Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="babce-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="babce-657">Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="babce-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="babce-658">Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="babce-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="babce-659">`asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę.</span><span class="sxs-lookup"><span data-stu-id="babce-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="babce-660">`asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="babce-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="babce-661">Model strony:</span><span class="sxs-lookup"><span data-stu-id="babce-661">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="babce-662">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="babce-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="babce-663">Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="babce-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="babce-664">W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="babce-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="babce-665">Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="babce-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="babce-666">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="babce-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="babce-667">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="babce-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="babce-668">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="babce-668">Custom routes</span></span>

<span data-ttu-id="babce-669">Użyj `@page` dyrektywy, aby:</span><span class="sxs-lookup"><span data-stu-id="babce-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="babce-670">Określ trasę niestandardową dla strony.</span><span class="sxs-lookup"><span data-stu-id="babce-670">Specify a custom route to a page.</span></span> <span data-ttu-id="babce-671">Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="babce-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="babce-672">Dołącz segmenty do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="babce-672">Append segments to a page's default route.</span></span> <span data-ttu-id="babce-673">Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="babce-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="babce-674">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="babce-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="babce-675">Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="babce-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="babce-676">Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="babce-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="babce-677">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="babce-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="babce-678">Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="babce-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="babce-679">Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="babce-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="babce-680">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="babce-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="babce-681">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="babce-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="babce-682">`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="babce-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="babce-683">Konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="babce-683">Configuration and settings</span></span>

<span data-ttu-id="babce-684">Aby skonfigurować opcje zaawansowane, użyj metody rozszerzenia `AddRazorPagesOptions` w konstruktorze MVC:</span><span class="sxs-lookup"><span data-stu-id="babce-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="babce-685">Obecnie można użyć, `RazorPagesOptions` Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="babce-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="babce-686">W przyszłości włączysz więcej rozszerzeń w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="babce-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="babce-687">Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="babce-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="babce-688">[Pobierz lub Wyświetl przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="babce-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="babce-689">Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="babce-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="babce-690">Określ, że Razor strony znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="babce-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="babce-691">Domyślnie Razor strony są umieszczane w katalogu */Pages* .</span><span class="sxs-lookup"><span data-stu-id="babce-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="babce-692">Dodaj [program with Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikacji:</span><span class="sxs-lookup"><span data-stu-id="babce-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="babce-693">Określ, że Razor strony znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="babce-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="babce-694">Dodaj element [with Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="babce-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="babce-695">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="babce-695">Additional resources</span></span>

* <span data-ttu-id="babce-696">[Autoryzuj atrybut i Razor strony](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="babce-696">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
