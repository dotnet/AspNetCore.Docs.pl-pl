---
title: Wprowadzenie do Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się Razor , jak strony w ASP.NET Core sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 203d77a2caaba9156419a04754fe162dd57052c1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012712"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="21117-103">Wprowadzenie do Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21117-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21117-104">[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="21117-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="21117-105">RazorStrony mogą sprawiać, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="21117-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="21117-106">Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="21117-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="21117-107">Ten dokument zawiera wprowadzenie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="21117-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="21117-108">Nie jest to samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="21117-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="21117-109">Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="21117-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="21117-110">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="21117-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21117-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="21117-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21117-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21117-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="21117-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21117-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21117-114">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="21117-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="21117-115">Tworzenie Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="21117-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21117-116">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21117-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="21117-117">Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="21117-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21117-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21117-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21117-119">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="21117-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21117-120">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="21117-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="21117-121">Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="21117-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="21117-122">RazorPage</span><span class="sxs-lookup"><span data-stu-id="21117-122">Razor Pages</span></span>

<span data-ttu-id="21117-123">RazorStrony są włączone w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="21117-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="21117-124">Weź pod uwagę podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="21117-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="21117-125">Poprzedni kod wygląda podobnie jak [ Razor plik widoku](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="21117-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="21117-126">Co sprawia, że jest to [`@page`](xref:mvc/views/razor#page) dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="21117-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="21117-127">`@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="21117-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="21117-128">`@page`musi być pierwszą Razor dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="21117-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="21117-129">`@page`wpływa na zachowanie innych [Razor](xref:mvc/views/razor) konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="21117-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="21117-130">RazorNazwy plików stron mają sufiks *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="21117-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="21117-131">Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="21117-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="21117-132">Plik *Pages/index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="21117-133">Model strony *stron/index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="21117-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="21117-134">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak Razor plik stronicowania dołączony do *. cs* .</span><span class="sxs-lookup"><span data-stu-id="21117-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="21117-135">Na przykład Poprzednia Razor strona to *Pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="21117-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="21117-136">Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="21117-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="21117-137">Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="21117-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="21117-138">W poniższej tabeli przedstawiono Razor ścieżkę strony i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="21117-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="21117-139">Nazwa i ścieżka pliku</span><span class="sxs-lookup"><span data-stu-id="21117-139">File name and path</span></span>               | <span data-ttu-id="21117-140">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="21117-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="21117-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="21117-142">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="21117-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="21117-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="21117-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="21117-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="21117-146">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="21117-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="21117-147">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="21117-147">Notes:</span></span>

* <span data-ttu-id="21117-148">Środowisko uruchomieniowe domyślnie wyszukuje Razor pliki stron w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="21117-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="21117-149">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="21117-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="21117-150">Napisz podstawowy formularz</span><span class="sxs-lookup"><span data-stu-id="21117-150">Write a basic form</span></span>

<span data-ttu-id="21117-151">RazorStrony są przeznaczone do tworzenia wspólnych wzorców używanych z przeglądarkami sieci Web, które są łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="21117-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="21117-152">[Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w Razor klasie Page.</span><span class="sxs-lookup"><span data-stu-id="21117-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="21117-153">Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="21117-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="21117-154">Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .</span><span class="sxs-lookup"><span data-stu-id="21117-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="21117-155">Baza danych w pamięci wymaga `Microsoft.EntityFrameworkCore.InMemory` pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="21117-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="21117-156">Model danych:</span><span class="sxs-lookup"><span data-stu-id="21117-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="21117-157">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="21117-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="21117-158">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="21117-159">Model strony *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="21117-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="21117-160">Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="21117-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="21117-161">`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="21117-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="21117-162">Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="21117-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="21117-163">Ta separacja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="21117-163">This separation allows:</span></span>

* <span data-ttu-id="21117-164">Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="21117-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="21117-165">Testowanie jednostek</span><span class="sxs-lookup"><span data-stu-id="21117-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="21117-166">Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz).</span><span class="sxs-lookup"><span data-stu-id="21117-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="21117-167">Można dodać metody obsługi dla dowolnego zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="21117-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="21117-168">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="21117-168">The most common handlers are:</span></span>

* <span data-ttu-id="21117-169">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="21117-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="21117-170">W poprzednim kodzie `OnGet` Metoda wyświetla stronę *onmode. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="21117-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="21117-171">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="21117-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="21117-172">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="21117-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="21117-173">Poprzedni kod jest typowy dla Razor stron.</span><span class="sxs-lookup"><span data-stu-id="21117-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="21117-174">Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="21117-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="21117-175">`OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="21117-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="21117-176">Większość elementów podstawowych MVC, takich jak [powiązania modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation)i akcje, działa tak samo, jak w przypadku kontrolerów i Razor stron.</span><span class="sxs-lookup"><span data-stu-id="21117-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="21117-177">Poprzednia `OnPostAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="21117-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="21117-178">Podstawowy przepływ `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="21117-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="21117-179">Sprawdź, czy występują błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="21117-179">Check for validation errors.</span></span>

* <span data-ttu-id="21117-180">Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.</span><span class="sxs-lookup"><span data-stu-id="21117-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="21117-181">W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji.</span><span class="sxs-lookup"><span data-stu-id="21117-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="21117-182">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="21117-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="21117-183">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="21117-184">Renderowany kod HTML ze *stron/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="21117-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="21117-185">W poprzednim kodzie, ogłaszanie formularza:</span><span class="sxs-lookup"><span data-stu-id="21117-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="21117-186">Z prawidłowymi danymi:</span><span class="sxs-lookup"><span data-stu-id="21117-186">With valid data:</span></span>

  * <span data-ttu-id="21117-187">`OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="21117-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="21117-188">Metoda `RedirectToPage` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="21117-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="21117-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="21117-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="21117-190">Jest wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="21117-190">Is an action result.</span></span>
    * <span data-ttu-id="21117-191">Jest podobny do `RedirectToAction` lub `RedirectToRoute` (używany w kontrolerach i widokach).</span><span class="sxs-lookup"><span data-stu-id="21117-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="21117-192">Jest dostosowywany dla stron.</span><span class="sxs-lookup"><span data-stu-id="21117-192">Is customized for pages.</span></span> <span data-ttu-id="21117-193">W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="21117-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="21117-194">`RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="21117-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="21117-195">Z błędami walidacji, które są przesyłane do serwera:</span><span class="sxs-lookup"><span data-stu-id="21117-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="21117-196">`OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="21117-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="21117-197">Metoda `Page` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="21117-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="21117-198">Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` .</span><span class="sxs-lookup"><span data-stu-id="21117-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="21117-199">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="21117-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="21117-200">Metoda obsługi, która zwraca `void` renderowanie strony.</span><span class="sxs-lookup"><span data-stu-id="21117-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="21117-201">W poprzednim przykładzie, księgowanie formularza bez wartości powoduje, że [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zwraca wartość false.</span><span class="sxs-lookup"><span data-stu-id="21117-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="21117-202">W tym przykładzie na kliencie nie są wyświetlane błędy sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="21117-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="21117-203">Przekazywanie błędów sprawdzania poprawności jest omówione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="21117-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="21117-204">Z błędami walidacji wykrytymi przez weryfikację po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="21117-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="21117-205">Dane **nie** są ogłaszane na serwerze.</span><span class="sxs-lookup"><span data-stu-id="21117-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="21117-206">Sprawdzanie poprawności po stronie klienta zostało wyjaśnione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="21117-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="21117-207">`Customer`Właściwość używa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atrybutu, aby zrezygnować z powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="21117-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="21117-208">`[BindProperty]`**nie** powinien być używany dla modeli zawierających właściwości, które nie powinny być zmieniane przez klienta.</span><span class="sxs-lookup"><span data-stu-id="21117-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="21117-209">Aby uzyskać więcej informacji, zobacz temat [przefinalizowanie](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="21117-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="21117-210">RazorDomyślnie strony powiążą właściwości tylko z `GET` niezleceniami.</span><span class="sxs-lookup"><span data-stu-id="21117-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="21117-211">Powiązanie z właściwościami eliminuje konieczność pisania kodu w celu przekonwertowania danych HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="21117-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="21117-212">Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="21117-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="21117-213">Przeglądanie pliku widoku *stron/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="21117-214">W poprzednim kodzie [pomocnik tagów wejściowych](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` wiąże `<input>` element HTML z `Customer.Name` wyrażeniem modelu.</span><span class="sxs-lookup"><span data-stu-id="21117-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="21117-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)udostępnia pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="21117-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="21117-216">Strona główna</span><span class="sxs-lookup"><span data-stu-id="21117-216">The home page</span></span>

<span data-ttu-id="21117-217">*Index. cshtml* to Strona główna:</span><span class="sxs-lookup"><span data-stu-id="21117-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="21117-218">Skojarzona `PageModel` Klasa (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="21117-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="21117-219">Plik *index. cshtml* zawiera następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="21117-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="21117-220">`<a /a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji.</span><span class="sxs-lookup"><span data-stu-id="21117-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="21117-221">Link zawiera dane trasy z IDENTYFIKATORem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="21117-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="21117-222">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="21117-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="21117-223">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="21117-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="21117-224">Plik *index. cshtml* zawiera znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="21117-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="21117-225">Renderowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="21117-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="21117-226">Gdy przycisk Usuń jest renderowany w języku HTML, jego [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) zawiera parametry dla:</span><span class="sxs-lookup"><span data-stu-id="21117-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="21117-227">Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.</span><span class="sxs-lookup"><span data-stu-id="21117-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="21117-228">`handler`, Określony przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="21117-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="21117-229">Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza.</span><span class="sxs-lookup"><span data-stu-id="21117-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="21117-230">Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="21117-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="21117-231">Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="21117-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="21117-232">Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="21117-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="21117-233">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="21117-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="21117-234">Pobiera `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="21117-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="21117-235">Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="21117-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="21117-236">Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty, a baza danych zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="21117-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="21117-237">Wywołania <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> do przekierowania na stronę indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="21117-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="21117-238">Plik Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="21117-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="21117-239">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="21117-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="21117-240">Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="21117-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="21117-241">Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="21117-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="21117-242">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="21117-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="21117-243">Plik *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="21117-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="21117-244">Weryfikacja</span><span class="sxs-lookup"><span data-stu-id="21117-244">Validation</span></span>

<span data-ttu-id="21117-245">Reguły walidacji:</span><span class="sxs-lookup"><span data-stu-id="21117-245">Validation rules:</span></span>

* <span data-ttu-id="21117-246">Są deklaratywnie określone w klasie modelu.</span><span class="sxs-lookup"><span data-stu-id="21117-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="21117-247">Są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="21117-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="21117-248"><xref:System.ComponentModel.DataAnnotations>Przestrzeń nazw zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="21117-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="21117-249">Adnotacje DataAnnotation zawierają również atrybuty formatowania, takie jak [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) Pomoc dotycząca formatowania i nie zapewniają weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="21117-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="21117-250">Rozważmy `Customer` model:</span><span class="sxs-lookup"><span data-stu-id="21117-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="21117-251">Za pomocą następującego pliku widoku *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="21117-252">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="21117-252">The preceding code:</span></span>

* <span data-ttu-id="21117-253">Obejmuje skrypty sprawdzania poprawności jQuery i jQuery.</span><span class="sxs-lookup"><span data-stu-id="21117-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="21117-254">Używa `<div />` `<span />` [pomocników tagów](xref:mvc/views/tag-helpers/intro) i do włączania:</span><span class="sxs-lookup"><span data-stu-id="21117-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="21117-255">Sprawdzanie poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="21117-255">Client-side validation.</span></span>
  * <span data-ttu-id="21117-256">Renderowanie błędów walidacji.</span><span class="sxs-lookup"><span data-stu-id="21117-256">Validation error rendering.</span></span>

* <span data-ttu-id="21117-257">Generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="21117-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="21117-258">Opublikowanie formularza tworzenia bez wartości nazwa powoduje wyświetlenie komunikatu o błędzie "Nazwa pola jest wymagana."</span><span class="sxs-lookup"><span data-stu-id="21117-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="21117-259">w formularzu.</span><span class="sxs-lookup"><span data-stu-id="21117-259">on the form.</span></span> <span data-ttu-id="21117-260">Jeśli na kliencie jest włączona obsługa języka JavaScript, przeglądarka wyświetli komunikat o błędzie bez publikowania na serwerze.</span><span class="sxs-lookup"><span data-stu-id="21117-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="21117-261">Ten `[StringLength(10)]` atrybut jest generowany `data-val-length-max="10"` na RENDEROWANYM kodzie HTML.</span><span class="sxs-lookup"><span data-stu-id="21117-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="21117-262">`data-val-length-max`zapobiega wprowadzaniu przez przeglądarki więcej niż określoną maksymalną długość.</span><span class="sxs-lookup"><span data-stu-id="21117-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="21117-263">Jeśli jest używane narzędzie, takie jak [programu Fiddler](https://www.telerik.com/fiddler) , do edytowania i powtarzania wpisu:</span><span class="sxs-lookup"><span data-stu-id="21117-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="21117-264">O nazwie dłuższej niż 10.</span><span class="sxs-lookup"><span data-stu-id="21117-264">With the name longer than 10.</span></span>
* <span data-ttu-id="21117-265">Komunikat o błędzie "Nazwa pola musi być ciągiem o maksymalnej długości 10".</span><span class="sxs-lookup"><span data-stu-id="21117-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="21117-266">.</span><span class="sxs-lookup"><span data-stu-id="21117-266">is returned.</span></span>

<span data-ttu-id="21117-267">Rozważmy następujący `Movie` model:</span><span class="sxs-lookup"><span data-stu-id="21117-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="21117-268">Atrybuty walidacji określają zachowanie do wymuszania na właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="21117-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="21117-269">`Required`Atrybuty i `MinimumLength` wskazują, że właściwość musi mieć wartość, ale nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.</span><span class="sxs-lookup"><span data-stu-id="21117-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="21117-270">Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="21117-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="21117-271">W poprzednim kodzie "gatunek":</span><span class="sxs-lookup"><span data-stu-id="21117-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="21117-272">Należy używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="21117-272">Must only use letters.</span></span>
  * <span data-ttu-id="21117-273">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="21117-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="21117-274">Odstępy, cyfry i znaki specjalne są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="21117-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="21117-275">`RegularExpression`"Ocena":</span><span class="sxs-lookup"><span data-stu-id="21117-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="21117-276">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="21117-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="21117-277">Zezwala na znaki specjalne i cyfry w kolejnych odstępach.</span><span class="sxs-lookup"><span data-stu-id="21117-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="21117-278">"PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".</span><span class="sxs-lookup"><span data-stu-id="21117-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="21117-279">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="21117-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="21117-280">`StringLength`Atrybut ustawia maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.</span><span class="sxs-lookup"><span data-stu-id="21117-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="21117-281">Typy wartości (takie jak `decimal` ,,, `int` `float` `DateTime` ) są z założenia wymagane i nie wymagają `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="21117-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="21117-282">Na stronie Tworzenie dla `Movie` modelu są wyświetlane błędy z nieprawidłowymi wartościami:</span><span class="sxs-lookup"><span data-stu-id="21117-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="21117-284">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="21117-284">For more information, see:</span></span>

* [<span data-ttu-id="21117-285">Dodawanie walidacji do aplikacji filmowej</span><span class="sxs-lookup"><span data-stu-id="21117-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="21117-286">[Walidacja modelu w ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="21117-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="21117-287">Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="21117-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="21117-288">`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="21117-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="21117-289">W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="21117-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="21117-290">Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:</span><span class="sxs-lookup"><span data-stu-id="21117-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="21117-291">RazorStrony powracają do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="21117-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="21117-292">XSRF/CSRF i Razor strony</span><span class="sxs-lookup"><span data-stu-id="21117-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="21117-293">RazorStrony są chronione przez [weryfikację przed fałszerstwem](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="21117-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="21117-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem.</span><span class="sxs-lookup"><span data-stu-id="21117-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="21117-295">Używanie układów, częściowych, szablonów i pomocników tagów ze Razor stronami</span><span class="sxs-lookup"><span data-stu-id="21117-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="21117-296">Strony współpracują ze wszystkimi możliwościami Razor aparatu widoku.</span><span class="sxs-lookup"><span data-stu-id="21117-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="21117-297">Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*i *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku widoków konwencjonalnych Razor .</span><span class="sxs-lookup"><span data-stu-id="21117-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="21117-298">Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="21117-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="21117-299">Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="21117-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="21117-300">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="21117-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="21117-301">Steruje układem każdej strony (chyba że strona nie jest częścią układu).</span><span class="sxs-lookup"><span data-stu-id="21117-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="21117-302">Importuje struktury HTML, takie jak JavaScript i stylesheets.</span><span class="sxs-lookup"><span data-stu-id="21117-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="21117-303">Zawartość Razor strony jest renderowana, gdzie `@RenderBody()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="21117-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="21117-304">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="21117-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="21117-305">Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="21117-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="21117-306">Układ znajduje się w *stronie/w folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="21117-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="21117-307">Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="21117-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="21117-308">Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej Razor strony w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="21117-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="21117-309">Plik układu powinien przejść do *stron/folderu udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="21117-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="21117-310">Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="21117-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="21117-311">*Widoki/udostępnione* są wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="21117-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="21117-312">RazorStrony są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.</span><span class="sxs-lookup"><span data-stu-id="21117-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="21117-313">Widok wyszukiwania na Razor stronie zawiera folder *strony* .</span><span class="sxs-lookup"><span data-stu-id="21117-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="21117-314">Układy, szablony i częściowe używane razem z kontrolerami MVC i konwencjonalnymi Razor widokami *działają tylko*.</span><span class="sxs-lookup"><span data-stu-id="21117-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="21117-315">Dodaj plik *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="21117-316">`@namespace`wyjaśniono w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="21117-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="21117-317">Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="21117-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="21117-318">`@namespace`Dyrektywa ustawiona na stronie:</span><span class="sxs-lookup"><span data-stu-id="21117-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="21117-319">`@namespace`Dyrektywa ustawia przestrzeń nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="21117-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="21117-320">`@model`Dyrektywa nie musi zawierać przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="21117-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="21117-321">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="21117-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="21117-322">Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="21117-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="21117-323">Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="21117-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="21117-324">Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="21117-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="21117-325">Wygenerowana przestrzeń nazw strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.</span><span class="sxs-lookup"><span data-stu-id="21117-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="21117-326">`@namespace`*działa również z konwencjonalnymi Razor widokami.*</span><span class="sxs-lookup"><span data-stu-id="21117-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="21117-327">Rozważ użycie pliku widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="21117-328">Zaktualizowane *strony/Create. cshtml* plik widoku z *_ViewImports. cshtml* i poprzedni plik układu:</span><span class="sxs-lookup"><span data-stu-id="21117-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="21117-329">W poprzednim kodzie, *_ViewImports. cshtml* zaimportował przestrzeń nazw i pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="21117-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="21117-330">Plik układu zaimportował pliki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="21117-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="21117-331">Na stronie [ Razor startowej projektu](#rpvs17) znajdują się *strony/_ValidationScriptsPartial. cshtml*, które przechwytuje walidację po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="21117-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="21117-332">Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="21117-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="21117-333">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="21117-333">URL generation for Pages</span></span>

<span data-ttu-id="21117-334">`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="21117-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="21117-335">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="21117-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="21117-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="21117-336">*/Pages*</span></span>

  * <span data-ttu-id="21117-337">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="21117-338">*Privacy. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="21117-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="21117-339">*/Customers*</span></span>

    * <span data-ttu-id="21117-340">*Create. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="21117-341">*Edytuj. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="21117-342">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-342">*Index.cshtml*</span></span>

<span data-ttu-id="21117-343">Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierowywać do *stron/Customers/index. cshtml* po powodzeniu.</span><span class="sxs-lookup"><span data-stu-id="21117-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="21117-344">Ciąg `./Index` jest względną nazwą strony używaną w celu uzyskania dostępu do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="21117-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="21117-345">Służy do generowania adresów URL na stronie *strony/klienci/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="21117-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="21117-346">Przykład:</span><span class="sxs-lookup"><span data-stu-id="21117-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="21117-347">Bezwzględna nazwa strony `/Index` służy do generowania adresów URL na stronie *stron/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="21117-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="21117-348">Przykład:</span><span class="sxs-lookup"><span data-stu-id="21117-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="21117-349">Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="21117-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="21117-350">Powyższe przykłady generowania adresów URL oferują udoskonalone Opcje i możliwości funkcjonalne w porównaniu z zakodowanym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="21117-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="21117-351">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="21117-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="21117-352">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="21117-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="21117-353">W poniższej tabeli przedstawiono, która strona indeksu została wybrana przy użyciu różnych `RedirectToPage` parametrów na stronie */Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="21117-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="21117-354">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="21117-354">RedirectToPage(x)</span></span>| <span data-ttu-id="21117-355">Strona</span><span class="sxs-lookup"><span data-stu-id="21117-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="21117-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="21117-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="21117-357">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-357">*Pages/Index*</span></span> |
| <span data-ttu-id="21117-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="21117-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="21117-359">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="21117-360">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="21117-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="21117-361">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-361">*Pages/Index*</span></span> |
| <span data-ttu-id="21117-362">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="21117-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="21117-363">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="21117-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="21117-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="21117-365">`RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="21117-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="21117-366">Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą.</span><span class="sxs-lookup"><span data-stu-id="21117-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="21117-367">Gdy nazwy względne są używane do łączenia między stronami w folderze:</span><span class="sxs-lookup"><span data-stu-id="21117-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="21117-368">Zmiana nazwy folderu nie powoduje zerwania linków względnych.</span><span class="sxs-lookup"><span data-stu-id="21117-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="21117-369">Linki nie są przerwane, ponieważ nie zawierają nazwy folderu.</span><span class="sxs-lookup"><span data-stu-id="21117-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="21117-370">Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="21117-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="21117-371">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas> i <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="21117-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="21117-372">ViewData — atrybut</span><span class="sxs-lookup"><span data-stu-id="21117-372">ViewData attribute</span></span>

<span data-ttu-id="21117-373">Dane można przekazywać do strony za pomocą <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="21117-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="21117-374">Właściwości z `[ViewData]` atrybutem mają przechowywane i ładowane wartości z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="21117-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="21117-375">W poniższym przykładzie `AboutModel` stosuje `[ViewData]` atrybut do `Title` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="21117-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="21117-376">Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="21117-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="21117-377">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="21117-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="21117-378">TempData</span><span class="sxs-lookup"><span data-stu-id="21117-378">TempData</span></span>

<span data-ttu-id="21117-379">ASP.NET Core uwidacznia <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="21117-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="21117-380">Ta właściwość przechowuje dane, dopóki nie zostanie odczytana.</span><span class="sxs-lookup"><span data-stu-id="21117-380">This property stores data until it's read.</span></span> <span data-ttu-id="21117-381"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody i <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="21117-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="21117-382">`TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="21117-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="21117-383">Poniższy kod ustawia wartość `Message` użycia `TempData` :</span><span class="sxs-lookup"><span data-stu-id="21117-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="21117-384">W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="21117-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="21117-385">Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="21117-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="21117-386">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="21117-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="21117-387">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="21117-387">Multiple handlers per page</span></span>

<span data-ttu-id="21117-388">Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="21117-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="21117-389">Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="21117-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="21117-390">Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="21117-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="21117-391">`asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę.</span><span class="sxs-lookup"><span data-stu-id="21117-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="21117-392">`asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="21117-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="21117-393">Model strony:</span><span class="sxs-lookup"><span data-stu-id="21117-393">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="21117-394">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="21117-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="21117-395">Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="21117-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="21117-396">W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="21117-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="21117-397">Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="21117-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="21117-398">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="21117-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="21117-399">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="21117-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="21117-400">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="21117-400">Custom routes</span></span>

<span data-ttu-id="21117-401">Użyj `@page` dyrektywy, aby:</span><span class="sxs-lookup"><span data-stu-id="21117-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="21117-402">Określ trasę niestandardową dla strony.</span><span class="sxs-lookup"><span data-stu-id="21117-402">Specify a custom route to a page.</span></span> <span data-ttu-id="21117-403">Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="21117-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="21117-404">Dołącz segmenty do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="21117-404">Append segments to a page's default route.</span></span> <span data-ttu-id="21117-405">Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="21117-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="21117-406">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="21117-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="21117-407">Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="21117-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="21117-408">Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="21117-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="21117-409">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="21117-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="21117-410">Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="21117-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="21117-411">Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="21117-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="21117-412">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="21117-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="21117-413">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="21117-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="21117-414">`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="21117-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="21117-415">Zaawansowana konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="21117-415">Advanced configuration and settings</span></span>

<span data-ttu-id="21117-416">Konfiguracja i ustawienia w poniższych sekcjach nie są wymagane przez większość aplikacji.</span><span class="sxs-lookup"><span data-stu-id="21117-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="21117-417">Aby skonfigurować opcje zaawansowane, użyj <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> przeciążenia, które konfiguruje <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="21117-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="21117-418">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="21117-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="21117-419">Aby uzyskać więcej informacji na temat Konwencji, zobacz [ Razor strony konwencje autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="21117-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="21117-420">Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="21117-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="21117-421">Określ, że Razor strony znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="21117-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="21117-422">Domyślnie Razor strony są umieszczane w katalogu */Pages* .</span><span class="sxs-lookup"><span data-stu-id="21117-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="21117-423">Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> aplikacji:</span><span class="sxs-lookup"><span data-stu-id="21117-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="21117-424">Określ, że Razor strony znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="21117-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="21117-425">Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="21117-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="21117-426">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="21117-426">Additional resources</span></span>

* <span data-ttu-id="21117-427">Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="21117-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="21117-428">Autoryzuj atrybut i Razor strony</span><span class="sxs-lookup"><span data-stu-id="21117-428">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="21117-429">Pobieranie lub wyświetlanie przykładowego kodu</span><span class="sxs-lookup"><span data-stu-id="21117-429">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

<span data-ttu-id="21117-430">[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="21117-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="21117-431">RazorStrony to nowy aspekt ASP.NET Core MVC, który sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i bardziej produktywne.</span><span class="sxs-lookup"><span data-stu-id="21117-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="21117-432">Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="21117-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="21117-433">Ten dokument zawiera wprowadzenie do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="21117-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="21117-434">Nie jest to samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="21117-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="21117-435">Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="21117-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="21117-436">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="21117-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21117-437">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="21117-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21117-438">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21117-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="21117-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21117-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21117-440">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="21117-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="21117-441">Tworzenie Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="21117-441">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21117-442">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21117-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="21117-443">Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="21117-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21117-444">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="21117-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="21117-445">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="21117-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="21117-446">Otwórz wygenerowany plik *csproj* z Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="21117-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21117-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21117-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21117-448">Uruchom `dotnet new webapp` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="21117-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="21117-449">RazorPage</span><span class="sxs-lookup"><span data-stu-id="21117-449">Razor Pages</span></span>

<span data-ttu-id="21117-450">RazorStrony są włączone w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="21117-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="21117-451">Weź pod uwagę podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="21117-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="21117-452">Poprzedni kod wygląda podobnie jak [ Razor plik widoku](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="21117-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="21117-453">Co sprawia, że jest to `@page` dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="21117-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="21117-454">`@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="21117-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="21117-455">`@page`musi być pierwszą Razor dyrektywą na stronie.</span><span class="sxs-lookup"><span data-stu-id="21117-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="21117-456">`@page`wpływa na zachowanie innych Razor konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="21117-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="21117-457">Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="21117-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="21117-458">Plik *Pages/index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="21117-459">Model strony *stron/index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="21117-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="21117-460">Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak Razor plik stronicowania dołączony do *. cs* .</span><span class="sxs-lookup"><span data-stu-id="21117-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="21117-461">Na przykład Poprzednia Razor strona to *Pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="21117-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="21117-462">Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="21117-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="21117-463">Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="21117-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="21117-464">W poniższej tabeli przedstawiono Razor ścieżkę strony i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="21117-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="21117-465">Nazwa i ścieżka pliku</span><span class="sxs-lookup"><span data-stu-id="21117-465">File name and path</span></span>               | <span data-ttu-id="21117-466">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="21117-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="21117-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="21117-468">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="21117-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="21117-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="21117-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="21117-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="21117-472">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="21117-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="21117-473">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="21117-473">Notes:</span></span>

* <span data-ttu-id="21117-474">Środowisko uruchomieniowe domyślnie wyszukuje Razor pliki stron w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="21117-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="21117-475">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="21117-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="21117-476">Napisz podstawowy formularz</span><span class="sxs-lookup"><span data-stu-id="21117-476">Write a basic form</span></span>

<span data-ttu-id="21117-477">RazorStrony są przeznaczone do tworzenia wspólnych wzorców używanych z przeglądarkami sieci Web, które są łatwe do wdrożenia podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="21117-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="21117-478">[Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w Razor klasie Page.</span><span class="sxs-lookup"><span data-stu-id="21117-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="21117-479">Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="21117-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="21117-480">Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="21117-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="21117-481">Model danych:</span><span class="sxs-lookup"><span data-stu-id="21117-481">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="21117-482">Kontekst bazy danych:</span><span class="sxs-lookup"><span data-stu-id="21117-482">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="21117-483">Plik widoku *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="21117-484">Model strony *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="21117-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="21117-485">Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="21117-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="21117-486">`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="21117-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="21117-487">Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="21117-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="21117-488">Ta separacja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="21117-488">This separation allows:</span></span>

* <span data-ttu-id="21117-489">Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="21117-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="21117-490">[Testowanie jednostkowe](xref:test/razor-pages-tests) stron.</span><span class="sxs-lookup"><span data-stu-id="21117-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="21117-491">Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz).</span><span class="sxs-lookup"><span data-stu-id="21117-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="21117-492">Metody procedury obsługi można dodać dla dowolnego czasownika HTTP.</span><span class="sxs-lookup"><span data-stu-id="21117-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="21117-493">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="21117-493">The most common handlers are:</span></span>

* <span data-ttu-id="21117-494">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="21117-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="21117-495">Przykład [OnGet](#OnGet) .</span><span class="sxs-lookup"><span data-stu-id="21117-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="21117-496">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="21117-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="21117-497">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="21117-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="21117-498">Poprzedni kod jest typowy dla Razor stron.</span><span class="sxs-lookup"><span data-stu-id="21117-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="21117-499">Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="21117-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="21117-500">`OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="21117-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="21117-501">Większość elementów podstawowych MVC, takich jak [powiązanie modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation), [Walidacja](xref:mvc/models/validation)i wyniki akcji, jest udostępniana.</span><span class="sxs-lookup"><span data-stu-id="21117-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="21117-502">Poprzednia `OnPostAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="21117-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="21117-503">Podstawowy przepływ `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="21117-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="21117-504">Sprawdź, czy występują błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="21117-504">Check for validation errors.</span></span>

* <span data-ttu-id="21117-505">Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.</span><span class="sxs-lookup"><span data-stu-id="21117-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="21117-506">W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji.</span><span class="sxs-lookup"><span data-stu-id="21117-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="21117-507">Walidacja po stronie klienta jest taka sama jak w przypadku tradycyjnych ASP.NET Core aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="21117-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="21117-508">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="21117-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="21117-509">Po pomyślnym wprowadzeniu danych `OnPostAsync` Metoda obsługi wywołuje `RedirectToPage` metodę pomocnika zwracającą wystąpienie elementu `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="21117-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="21117-510">`RedirectToPage`to nowy wynik akcji, podobny do `RedirectToAction` lub `RedirectToRoute` , ale dostosowany do stron.</span><span class="sxs-lookup"><span data-stu-id="21117-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="21117-511">W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="21117-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="21117-512">`RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="21117-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="21117-513">Gdy przesłany formularz ma błędy walidacji (które są przekazywane do serwera), `OnPostAsync` Metoda obsługi wywołuje `Page` metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="21117-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="21117-514">Metoda `Page` zwraca wystąpienie klasy `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="21117-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="21117-515">Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` .</span><span class="sxs-lookup"><span data-stu-id="21117-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="21117-516">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="21117-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="21117-517">Metoda obsługi, która zwraca `void` renderowanie strony.</span><span class="sxs-lookup"><span data-stu-id="21117-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="21117-518">`Customer`Właściwość używa `[BindProperty]` atrybutu, aby zrezygnować z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="21117-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="21117-519">RazorDomyślnie strony powiążą właściwości tylko z `GET` niezleceniami.</span><span class="sxs-lookup"><span data-stu-id="21117-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="21117-520">Utworzenie powiązania z właściwościami może zmniejszyć ilość kodu, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="21117-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="21117-521">Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="21117-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="21117-522">Strona główna (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="21117-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="21117-523">Skojarzona `PageModel` Klasa (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="21117-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="21117-524">Plik *index. cshtml* zawiera następujące znaczniki, aby utworzyć łącze do edycji dla każdego kontaktu:</span><span class="sxs-lookup"><span data-stu-id="21117-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="21117-525">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji.</span><span class="sxs-lookup"><span data-stu-id="21117-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="21117-526">Link zawiera dane trasy z IDENTYFIKATORem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="21117-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="21117-527">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="21117-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="21117-528">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="21117-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="21117-529">Pomocnicy tagów są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="21117-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="21117-530">Plik *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="21117-531">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="21117-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="21117-532">Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="21117-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="21117-533">Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="21117-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="21117-534">Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="21117-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="21117-535">Plik *stron/Edytuj. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="21117-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="21117-536">Plik *index. cshtml* zawiera również znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="21117-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="21117-537">Gdy przycisk Usuń jest renderowany w języku HTML, jego `formaction` Parametry obejmują:</span><span class="sxs-lookup"><span data-stu-id="21117-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="21117-538">Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.</span><span class="sxs-lookup"><span data-stu-id="21117-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="21117-539">`handler`Określony przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="21117-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="21117-540">Oto przykład renderowanego przycisku usuwania z IDENTYFIKATORem kontaktu klienta `1` :</span><span class="sxs-lookup"><span data-stu-id="21117-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="21117-541">Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza.</span><span class="sxs-lookup"><span data-stu-id="21117-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="21117-542">Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="21117-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="21117-543">Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania.</span><span class="sxs-lookup"><span data-stu-id="21117-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="21117-544">Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="21117-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="21117-545">Poniższy kod ilustruje `OnPostDeleteAsync` procedurę obsługi:</span><span class="sxs-lookup"><span data-stu-id="21117-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="21117-546">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="21117-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="21117-547">Akceptuje `id` z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="21117-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="21117-548">Jeśli na stronie *index. cshtml* znajduje się ograniczenie routingu `"{id:int?}"` , `id` będą pochodzić z danych tras.</span><span class="sxs-lookup"><span data-stu-id="21117-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="21117-549">Dane trasy dla programu `id` są określone w identyfikatorze URI, takich jak `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="21117-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="21117-550">Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="21117-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="21117-551">Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty z listy kontaktów klientów.</span><span class="sxs-lookup"><span data-stu-id="21117-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="21117-552">Baza danych jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="21117-552">The database is updated.</span></span>
* <span data-ttu-id="21117-553">Wywołania `RedirectToPage` do przekierowania na stronę indeksu głównego ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="21117-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="21117-554">Oznacz właściwości strony jako wymagane</span><span class="sxs-lookup"><span data-stu-id="21117-554">Mark page properties as required</span></span>

<span data-ttu-id="21117-555">Właściwości na stronie `PageModel` można oznaczyć przy użyciu [wymaganego](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atrybutu:</span><span class="sxs-lookup"><span data-stu-id="21117-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="21117-556">Aby uzyskać więcej informacji, zobacz [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="21117-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="21117-557">Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet</span><span class="sxs-lookup"><span data-stu-id="21117-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="21117-558">`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="21117-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="21117-559">W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="21117-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="21117-560">Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:</span><span class="sxs-lookup"><span data-stu-id="21117-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="21117-561">W ASP.NET Core 2,1 lub nowszej strony wracają Razor do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="21117-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="21117-562">To zachowanie jest włączane przez wywołanie [SetCompatibilityVersion](xref:mvc/compatibility-version) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="21117-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="21117-563">Szablony domyślne generują `SetCompatibilityVersion` wywołanie w ASP.NET Core 2,1 i 2,2.</span><span class="sxs-lookup"><span data-stu-id="21117-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="21117-564">`SetCompatibilityVersion`efektywnie ustawia Razor opcję strony `AllowMappingHeadRequestsToGetHandler` na `true` .</span><span class="sxs-lookup"><span data-stu-id="21117-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="21117-565">Zamiast korzystać z wszystkich zachowań w programie `SetCompatibilityVersion` , można jawnie zrezygnować z *określonych* zachowań.</span><span class="sxs-lookup"><span data-stu-id="21117-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="21117-566">Poniższy kod pozwala na umożliwienie `HEAD` mapowania żądań do `OnGet` programu obsługi:</span><span class="sxs-lookup"><span data-stu-id="21117-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="21117-567">XSRF/CSRF i Razor strony</span><span class="sxs-lookup"><span data-stu-id="21117-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="21117-568">Nie trzeba pisać kodu do [weryfikacji przed fałszerstwem](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="21117-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="21117-569">Generowanie i sprawdzanie poprawności tokenów antysfałszowanych są automatycznie dołączane do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="21117-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="21117-570">Używanie układów, częściowych, szablonów i pomocników tagów ze Razor stronami</span><span class="sxs-lookup"><span data-stu-id="21117-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="21117-571">Strony współpracują ze wszystkimi możliwościami Razor aparatu widoku.</span><span class="sxs-lookup"><span data-stu-id="21117-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="21117-572">Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*, *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku Razor widoków konwencjonalnych.</span><span class="sxs-lookup"><span data-stu-id="21117-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="21117-573">Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="21117-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="21117-574">Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="21117-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="21117-575">[Układ](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="21117-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="21117-576">Steruje układem każdej strony (chyba że strona nie jest częścią układu).</span><span class="sxs-lookup"><span data-stu-id="21117-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="21117-577">Importuje struktury HTML, takie jak JavaScript i stylesheets.</span><span class="sxs-lookup"><span data-stu-id="21117-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="21117-578">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="21117-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="21117-579">Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="21117-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="21117-580">Układ znajduje się w *stronie/w folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="21117-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="21117-581">Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="21117-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="21117-582">Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej Razor strony w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="21117-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="21117-583">Plik układu powinien przejść do *stron/folderu udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="21117-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="21117-584">Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* .</span><span class="sxs-lookup"><span data-stu-id="21117-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="21117-585">*Widoki/udostępnione* są wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="21117-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="21117-586">RazorStrony są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.</span><span class="sxs-lookup"><span data-stu-id="21117-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="21117-587">Widok wyszukiwania na Razor stronie zawiera folder *strony* .</span><span class="sxs-lookup"><span data-stu-id="21117-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="21117-588">Układy, szablony i częściowe, które są używane z kontrolerami MVC i konwencjonalnymi Razor widokami *działają tylko*.</span><span class="sxs-lookup"><span data-stu-id="21117-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="21117-589">Dodaj plik *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="21117-590">`@namespace`wyjaśniono w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="21117-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="21117-591">Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="21117-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="21117-592">Gdy `@namespace` dyrektywa jest używana jawnie na stronie:</span><span class="sxs-lookup"><span data-stu-id="21117-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="21117-593">Dyrektywa ustawia przestrzeń nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="21117-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="21117-594">`@model`Dyrektywa nie musi zawierać przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="21117-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="21117-595">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="21117-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="21117-596">Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="21117-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="21117-597">Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="21117-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="21117-598">Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="21117-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="21117-599">Wygenerowana przestrzeń nazw strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.</span><span class="sxs-lookup"><span data-stu-id="21117-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="21117-600">`@namespace`*działa również z konwencjonalnymi Razor widokami.*</span><span class="sxs-lookup"><span data-stu-id="21117-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="21117-601">Oryginalne *strony/Utwórz plik widoku. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="21117-602">Zaktualizowane *strony/Utwórz plik widoku. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21117-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="21117-603">Na stronie [ Razor startowej projektu](#rpvs17) znajdują się *strony/_ValidationScriptsPartial. cshtml*, które przechwytuje walidację po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="21117-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="21117-604">Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="21117-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="21117-605">Generowanie adresu URL dla stron</span><span class="sxs-lookup"><span data-stu-id="21117-605">URL generation for Pages</span></span>

<span data-ttu-id="21117-606">`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="21117-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="21117-607">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="21117-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="21117-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="21117-608">*/Pages*</span></span>

  * <span data-ttu-id="21117-609">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="21117-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="21117-610">*/Customers*</span></span>

    * <span data-ttu-id="21117-611">*Create. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="21117-612">*Edytuj. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="21117-613">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21117-613">*Index.cshtml*</span></span>

<span data-ttu-id="21117-614">Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierują do *stron/index. cshtml* po powodzeniu.</span><span class="sxs-lookup"><span data-stu-id="21117-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="21117-615">Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="21117-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="21117-616">Ten ciąg `/Index` może służyć do generowania identyfikatorów URI na stronie *stron/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="21117-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="21117-617">Przykład:</span><span class="sxs-lookup"><span data-stu-id="21117-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="21117-618">Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="21117-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="21117-619">Powyższe przykłady generowania adresów URL oferują ulepszone opcje i możliwości funkcjonalne w porównaniu z zakodowana adresem URL.</span><span class="sxs-lookup"><span data-stu-id="21117-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="21117-620">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="21117-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="21117-621">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="21117-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="21117-622">W poniższej tabeli przedstawiono, która strona indeksu została wybrana z różnymi `RedirectToPage` parametrami *stron/Customers/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="21117-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="21117-623">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="21117-623">RedirectToPage(x)</span></span>| <span data-ttu-id="21117-624">Strona</span><span class="sxs-lookup"><span data-stu-id="21117-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="21117-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="21117-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="21117-626">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-626">*Pages/Index*</span></span> |
| <span data-ttu-id="21117-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="21117-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="21117-628">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="21117-629">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="21117-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="21117-630">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-630">*Pages/Index*</span></span> |
| <span data-ttu-id="21117-631">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="21117-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="21117-632">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="21117-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="21117-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="21117-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="21117-634">`RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="21117-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="21117-635">Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą.</span><span class="sxs-lookup"><span data-stu-id="21117-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="21117-636">Jeśli używasz nazw względnych do łączenia między stronami w folderze, możesz zmienić nazwę tego folderu.</span><span class="sxs-lookup"><span data-stu-id="21117-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="21117-637">Wszystkie linki nadal działają (ponieważ nie zawierają nazwy folderu).</span><span class="sxs-lookup"><span data-stu-id="21117-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="21117-638">Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:</span><span class="sxs-lookup"><span data-stu-id="21117-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="21117-639">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="21117-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="21117-640">ViewData — atrybut</span><span class="sxs-lookup"><span data-stu-id="21117-640">ViewData attribute</span></span>

<span data-ttu-id="21117-641">Dane można przekazywać do strony z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="21117-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="21117-642">Właściwości kontrolerów lub Razor modeli stron z `[ViewData]` atrybutem są przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="21117-642">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="21117-643">W poniższym przykładzie `AboutModel` zawiera właściwość, która jest `Title` oznaczona za pomocą `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="21117-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="21117-644">`Title`Właściwość jest ustawiona na tytuł strony informacje:</span><span class="sxs-lookup"><span data-stu-id="21117-644">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="21117-645">Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="21117-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="21117-646">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="21117-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="21117-647">TempData</span><span class="sxs-lookup"><span data-stu-id="21117-647">TempData</span></span>

<span data-ttu-id="21117-648">ASP.NET Core uwidacznia Właściwość [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="21117-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="21117-649">Ta właściwość przechowuje dane, dopóki nie zostanie odczytana.</span><span class="sxs-lookup"><span data-stu-id="21117-649">This property stores data until it's read.</span></span> <span data-ttu-id="21117-650">`Keep`Metody i `Peek` mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="21117-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="21117-651">`TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="21117-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="21117-652">Poniższy kod ustawia wartość `Message` użycia `TempData` :</span><span class="sxs-lookup"><span data-stu-id="21117-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="21117-653">W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="21117-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="21117-654">Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="21117-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="21117-655">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="21117-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="21117-656">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="21117-656">Multiple handlers per page</span></span>

<span data-ttu-id="21117-657">Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="21117-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="21117-658">Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="21117-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="21117-659">Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="21117-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="21117-660">`asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę.</span><span class="sxs-lookup"><span data-stu-id="21117-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="21117-661">`asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="21117-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="21117-662">Model strony:</span><span class="sxs-lookup"><span data-stu-id="21117-662">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="21117-663">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="21117-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="21117-664">Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="21117-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="21117-665">W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="21117-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="21117-666">Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="21117-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="21117-667">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="21117-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="21117-668">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="21117-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="21117-669">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="21117-669">Custom routes</span></span>

<span data-ttu-id="21117-670">Użyj `@page` dyrektywy, aby:</span><span class="sxs-lookup"><span data-stu-id="21117-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="21117-671">Określ trasę niestandardową dla strony.</span><span class="sxs-lookup"><span data-stu-id="21117-671">Specify a custom route to a page.</span></span> <span data-ttu-id="21117-672">Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="21117-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="21117-673">Dołącz segmenty do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="21117-673">Append segments to a page's default route.</span></span> <span data-ttu-id="21117-674">Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="21117-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="21117-675">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="21117-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="21117-676">Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="21117-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="21117-677">Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="21117-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="21117-678">Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="21117-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="21117-679">Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="21117-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="21117-680">Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="21117-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="21117-681">Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="21117-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="21117-682">Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="21117-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="21117-683">`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="21117-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="21117-684">Konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="21117-684">Configuration and settings</span></span>

<span data-ttu-id="21117-685">Aby skonfigurować opcje zaawansowane, użyj metody rozszerzenia `AddRazorPagesOptions` w konstruktorze MVC:</span><span class="sxs-lookup"><span data-stu-id="21117-685">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="21117-686">Obecnie można użyć, `RazorPagesOptions` Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="21117-686">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="21117-687">W przyszłości włączysz więcej rozszerzeń w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="21117-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="21117-688">Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="21117-688">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="21117-689">[Pobierz lub Wyświetl przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="21117-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="21117-690">Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="21117-690">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="21117-691">Określ, że Razor strony znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="21117-691">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="21117-692">Domyślnie Razor strony są umieszczane w katalogu */Pages* .</span><span class="sxs-lookup"><span data-stu-id="21117-692">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="21117-693">Dodaj [program with Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikacji:</span><span class="sxs-lookup"><span data-stu-id="21117-693">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="21117-694">Określ, że Razor strony znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="21117-694">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="21117-695">Dodaj element [with Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="21117-695">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="21117-696">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="21117-696">Additional resources</span></span>

* [<span data-ttu-id="21117-697">Autoryzuj atrybut i Razor strony</span><span class="sxs-lookup"><span data-stu-id="21117-697">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
