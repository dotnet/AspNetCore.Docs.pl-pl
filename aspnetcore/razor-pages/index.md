---
title: Wprowadzenie do stron Razor w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak razor Pages w ASP.NET Core sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne niż przy użyciu MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667581"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="45ad5-103">Wprowadzenie do stron Razor w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45ad5-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="45ad5-104">Autorstwa [Ricka Andersona](https://twitter.com/RickAndMSFT) i [Ryana Nowaka](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="45ad5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="45ad5-105">Razor Pages może sprawić, że scenariusze związane ze stroną kodowania będą łatwiejsze i bardziej wydajne niż przy użyciu kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="45ad5-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="45ad5-106">Jeśli szukasz samouczka, który używa podejścia Model-View-Controller, zobacz [Wprowadzenie do ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="45ad5-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="45ad5-107">Ten dokument zawiera wprowadzenie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="45ad5-108">To nie jest samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="45ad5-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="45ad5-109">Jeśli niektóre sekcje są zbyt zaawansowane, zobacz [Wprowadzenie do strony Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="45ad5-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="45ad5-110">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [Wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="45ad5-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="45ad5-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="45ad5-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45ad5-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45ad5-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="45ad5-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="45ad5-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="45ad5-114">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="45ad5-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="45ad5-115">Tworzenie projektu strony razor</span><span class="sxs-lookup"><span data-stu-id="45ad5-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45ad5-116">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45ad5-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="45ad5-117">Zobacz [Wprowadzenie do strony Razor Pages,](xref:tutorials/razor-pages/razor-pages-start) aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="45ad5-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="45ad5-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="45ad5-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="45ad5-119">Uruchom `dotnet new webapp` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="45ad5-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="45ad5-120">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="45ad5-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="45ad5-121">Uruchom `dotnet new webapp` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="45ad5-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="45ad5-122">Otwórz wygenerowany plik *csproj* z programu Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="45ad5-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="45ad5-123">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="45ad5-123">Razor Pages</span></span>

<span data-ttu-id="45ad5-124">Strona maszynki do golenia jest włączona w *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="45ad5-125">Rozważmy podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="45ad5-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="45ad5-126">Poprzedni kod wygląda podobnie do [pliku widoku Razor](xref:tutorials/first-mvc-app/adding-view) używanego w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="45ad5-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="45ad5-127">To, co sprawia, że jest inny, [`@page`](xref:mvc/views/razor#page) to dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="45ad5-127">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="45ad5-128">`@page`sprawia, że plik do akcji MVC - co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="45ad5-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="45ad5-129">`@page`musi być pierwszą dyrektywą Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="45ad5-130">`@page`wpływa na zachowanie innych konstrukcji [Razor.](xref:mvc/views/razor)</span><span class="sxs-lookup"><span data-stu-id="45ad5-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="45ad5-131">Nazwy plików Razor Pages mają sufiks *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="45ad5-132">Podobna strona, `PageModel` przy użyciu klasy, jest wyświetlana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="45ad5-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="45ad5-133">Plik *Pages/Index2.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="45ad5-134">Model strony *Pages/Index2.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="45ad5-135">Zgodnie z `PageModel` konwencją plik klasy ma taką samą nazwę jak plik Razor Page z dołączona do *pliku cs.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="45ad5-136">Na przykład poprzednia strona Razor to *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="45ad5-137">Plik zawierający `PageModel` klasę nosi nazwę *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="45ad5-138">Skojarzenia ścieżek URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="45ad5-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="45ad5-139">W poniższej tabeli przedstawiono ścieżkę strony Razor i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="45ad5-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="45ad5-140">Nazwa pliku i ścieżka</span><span class="sxs-lookup"><span data-stu-id="45ad5-140">File name and path</span></span>               | <span data-ttu-id="45ad5-141">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="45ad5-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="45ad5-142">*/Strony/Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="45ad5-143">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="45ad5-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="45ad5-144">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="45ad5-145">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="45ad5-146">*/Strony/Sklep/Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="45ad5-147">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="45ad5-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="45ad5-148">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="45ad5-148">Notes:</span></span>

* <span data-ttu-id="45ad5-149">Środowisko wykonawcze domyślnie wyszukuje pliki Razor Pages w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="45ad5-150">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="45ad5-151">Napisz formularz podstawowy</span><span class="sxs-lookup"><span data-stu-id="45ad5-151">Write a basic form</span></span>

<span data-ttu-id="45ad5-152">Razor Pages został zaprojektowany tak, aby typowe wzorce używane z przeglądarkami internetowymi były łatwe do wdrożenia podczas tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="45ad5-153">[Powiązanie modelu,](xref:mvc/models/model-binding) [Pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML *po prostu pracować* z właściwości zdefiniowane w razor page klasy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="45ad5-154">Rozważmy stronę, która implementuje podstawowy formularz `Contact` "skontaktuj się z nami" dla modelu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="45ad5-155">Dla przykładów w tym `DbContext` dokumencie jest inicjowany w pliku [Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24)</span><span class="sxs-lookup"><span data-stu-id="45ad5-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="45ad5-156">Model danych:</span><span class="sxs-lookup"><span data-stu-id="45ad5-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="45ad5-157">Kontekst db:</span><span class="sxs-lookup"><span data-stu-id="45ad5-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="45ad5-158">Plik widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="45ad5-159">Model strony *Pages/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="45ad5-160">Zgodnie z `PageModel` konwencją `<PageName>Model` klasa jest wywoływana i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="45ad5-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="45ad5-161">Klasa `PageModel` umożliwia oddzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="45ad5-162">Definiuje programy obsługi stron dla żądań wysyłanych do strony i dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="45ad5-163">Separacja ta pozwala na:</span><span class="sxs-lookup"><span data-stu-id="45ad5-163">This separation allows:</span></span>

* <span data-ttu-id="45ad5-164">Zarządzanie zależnościami stron poprzez [iniekcję zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="45ad5-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="45ad5-165">Testowanie jednostek</span><span class="sxs-lookup"><span data-stu-id="45ad5-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="45ad5-166">Strona ma `OnPostAsync` *metodę obsługi*, `POST` która jest uruchamiana na żądaniach (gdy użytkownik publikuje formularz).</span><span class="sxs-lookup"><span data-stu-id="45ad5-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="45ad5-167">Można dodać metody obsługi dla dowolnego zlecenia HTTP.</span><span class="sxs-lookup"><span data-stu-id="45ad5-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="45ad5-168">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="45ad5-168">The most common handlers are:</span></span>

* <span data-ttu-id="45ad5-169">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="45ad5-170">W poprzednim kodzie `OnGet` metoda wyświetla *CreateModel.cshtml* Razor Page.</span><span class="sxs-lookup"><span data-stu-id="45ad5-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="45ad5-171">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="45ad5-172">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="45ad5-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="45ad5-173">Powyższy kod jest typowy dla stron Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="45ad5-174">Jeśli znasz ASP.NET aplikacje za pomocą kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="45ad5-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="45ad5-175">Kod `OnPostAsync` w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="45ad5-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="45ad5-176">Większość ćwierczy MVC, takich jak [powiązanie modelu,](xref:mvc/models/model-binding) [sprawdzanie poprawności](xref:mvc/models/validation)i wyniki akcji, działa tak samo z kontrolerami i stronami razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="45ad5-177">Poprzednia `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="45ad5-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="45ad5-178">Podstawowy `OnPostAsync`przepływ:</span><span class="sxs-lookup"><span data-stu-id="45ad5-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="45ad5-179">Sprawdź, czy nie ma błędów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="45ad5-179">Check for validation errors.</span></span>

* <span data-ttu-id="45ad5-180">Jeśli nie ma żadnych błędów, zapisz dane i przekieruj.</span><span class="sxs-lookup"><span data-stu-id="45ad5-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="45ad5-181">Jeśli występują błędy, pokaż stronę ponownie z komunikatami sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="45ad5-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="45ad5-182">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="45ad5-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="45ad5-183">Plik widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="45ad5-184">Renderowany kod HTML ze *stron/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="45ad5-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="45ad5-185">W poprzednim kodzie księgowanie formularza:</span><span class="sxs-lookup"><span data-stu-id="45ad5-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="45ad5-186">Z prawidłowymi danymi:</span><span class="sxs-lookup"><span data-stu-id="45ad5-186">With valid data:</span></span>

  * <span data-ttu-id="45ad5-187">Metoda `OnPostAsync` obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="45ad5-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="45ad5-188">Metoda `RedirectToPage` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="45ad5-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="45ad5-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="45ad5-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="45ad5-190">Jest wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-190">Is an action result.</span></span>
    * <span data-ttu-id="45ad5-191">Jest podobny `RedirectToAction` `RedirectToRoute` do lub (używany w kontrolerach i widokach).</span><span class="sxs-lookup"><span data-stu-id="45ad5-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="45ad5-192">Jest dostosowany do stron.</span><span class="sxs-lookup"><span data-stu-id="45ad5-192">Is customized for pages.</span></span> <span data-ttu-id="45ad5-193">W poprzednim przykładzie przekierowuje do głównej`/Index`strony indeksu ( ).</span><span class="sxs-lookup"><span data-stu-id="45ad5-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="45ad5-194">`RedirectToPage`jest szczegółowo opisana w sekcji [generowanie adresów URL dla stron.](#url_gen)</span><span class="sxs-lookup"><span data-stu-id="45ad5-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="45ad5-195">Z błędami sprawdzania poprawności, które są przekazywane do serwera:</span><span class="sxs-lookup"><span data-stu-id="45ad5-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="45ad5-196">Metoda `OnPostAsync` obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="45ad5-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="45ad5-197">Metoda `Page` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="45ad5-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="45ad5-198">Powrót `Page` jest podobny do tego, `View`jak akcje w kontrolerach zwracają .</span><span class="sxs-lookup"><span data-stu-id="45ad5-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="45ad5-199">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="45ad5-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="45ad5-200">Metoda obsługi, `void` która zwraca renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="45ad5-201">W poprzednim przykładzie księgowanie formularza bez wyników wartości w [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zwracanie false.</span><span class="sxs-lookup"><span data-stu-id="45ad5-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="45ad5-202">W tym przykładzie nie błędy sprawdzania poprawności są wyświetlane na kliencie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="45ad5-203">Przekazywanie błędów sprawdzania poprawności jest omówione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="45ad5-204">Z błędami sprawdzania poprawności wykrytymi przez sprawdzanie poprawności po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="45ad5-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="45ad5-205">Dane **nie** są księgowane na serwerze.</span><span class="sxs-lookup"><span data-stu-id="45ad5-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="45ad5-206">Sprawdzanie poprawności po stronie klienta jest wyjaśnione w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="45ad5-207">Właściwość `Customer` używa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atrybutu, aby zdecydować się na powiązanie modelu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="45ad5-208">`[BindProperty]`**nie** powinny być używane w modelach zawierających właściwości, które nie powinny być zmieniane przez klienta.</span><span class="sxs-lookup"><span data-stu-id="45ad5-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="45ad5-209">Aby uzyskać więcej informacji, zobacz [Nadpostanie](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="45ad5-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="45ad5-210">Razor Pages, domyślnie, powiązać właściwości`GET` tylko z non-zleceń.</span><span class="sxs-lookup"><span data-stu-id="45ad5-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="45ad5-211">Powiązanie właściwości usuwa konieczność pisania kodu w celu konwersji danych HTTP do typu modelu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="45ad5-212">Powiązanie zmniejsza kod przy użyciu tej`<input asp-for="Customer.Name">`samej właściwości do renderowania pól formularza ( ) i zaakceptować dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="45ad5-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="45ad5-213">Przeglądanie pliku widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="45ad5-214">W poprzednim kodzie [pomocnik](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` znacznika wejściowego `<input>` wiąże element `Customer.Name` HTML z wyrażeniem modelu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="45ad5-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)udostępnia Pomocników Tagów.</span><span class="sxs-lookup"><span data-stu-id="45ad5-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="45ad5-216">Strona główna</span><span class="sxs-lookup"><span data-stu-id="45ad5-216">The home page</span></span>

<span data-ttu-id="45ad5-217">*Index.cshtml* jest stroną główną:</span><span class="sxs-lookup"><span data-stu-id="45ad5-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="45ad5-218">Powiązana `PageModel` klasa (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="45ad5-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="45ad5-219">Plik *Index.cshtml* zawiera następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="45ad5-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="45ad5-220">`<a /a>` [Pomocnik znacznika kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył tego atrybutu `asp-route-{value}` do wygenerowania łącza do strony Edycji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="45ad5-221">Łącze zawiera dane trasy o identyfikatorze kontaktu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="45ad5-222">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="45ad5-223">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="45ad5-224">Plik *Index.cshtml* zawiera znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="45ad5-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="45ad5-225">Renderowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="45ad5-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="45ad5-226">Gdy przycisk usuwania jest renderowany w formacie HTML, jego [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) zawiera parametry dla:</span><span class="sxs-lookup"><span data-stu-id="45ad5-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="45ad5-227">Identyfikator kontaktu z klientem `asp-route-id` określony przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="45ad5-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="45ad5-228">The `handler`, określony `asp-page-handler` przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="45ad5-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="45ad5-229">Po wybraniu przycisku `POST` żądanie formularza jest wysyłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="45ad5-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="45ad5-230">Zgodnie z konwencją nazwa metody obsługi jest wybierana `handler` na podstawie `OnPost[handler]Async`wartości parametru zgodnie ze schematem .</span><span class="sxs-lookup"><span data-stu-id="45ad5-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="45ad5-231">Ponieważ `handler` jest `delete` w tym `OnPostDeleteAsync` przykładzie, metoda obsługi `POST` jest używana do przetwarzania żądania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="45ad5-232">Jeśli `asp-page-handler` jest ustawiona na inną `remove`wartość, na przykład `OnPostRemoveAsync` , wybrana jest metoda obsługi o nazwie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="45ad5-233">Metoda: `OnPostDeleteAsync`</span><span class="sxs-lookup"><span data-stu-id="45ad5-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="45ad5-234">Pobiera `id` ciąg kwerendy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="45ad5-235">Wysyła zapytania do bazy danych `FindAsync`dla kontaktu z klientem za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="45ad5-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="45ad5-236">Jeśli kontakt z klientem zostanie znaleziony, zostanie usunięty, a baza danych zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="45ad5-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="45ad5-237">Wywołania <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> przekierowania do głównej`/Index`strony indeksu ( ).</span><span class="sxs-lookup"><span data-stu-id="45ad5-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="45ad5-238">Plik Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="45ad5-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="45ad5-239">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="45ad5-240">Ograniczenie`"{id:int}"` routingu nakazuje stronie akceptowanie żądań na stronie zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="45ad5-241">Jeśli żądanie na stronie nie zawiera danych trasy, które `int`można przekonwertować na , środowisko wykonawcze zwraca błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="45ad5-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="45ad5-242">Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="45ad5-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="45ad5-243">Plik *Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="45ad5-244">Walidacja</span><span class="sxs-lookup"><span data-stu-id="45ad5-244">Validation</span></span>

<span data-ttu-id="45ad5-245">Reguły sprawdzania poprawności:</span><span class="sxs-lookup"><span data-stu-id="45ad5-245">Validation rules:</span></span>

* <span data-ttu-id="45ad5-246">Są deklaratywnie określone w klasie modelu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="45ad5-247">Są wymuszane wszędzie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="45ad5-248">Obszar <xref:System.ComponentModel.DataAnnotations> nazw zawiera zestaw wbudowanych atrybutów sprawdzania poprawności, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="45ad5-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="45ad5-249">DataAnnotations zawiera również atrybuty formatowania, takie jak, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) które pomagają w formatowaniu i nie zapewniają sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="45ad5-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="45ad5-250">Należy `Customer` wziąć pod uwagę model:</span><span class="sxs-lookup"><span data-stu-id="45ad5-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="45ad5-251">Korzystanie z następującego pliku widoku *Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="45ad5-252">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="45ad5-252">The preceding code:</span></span>

* <span data-ttu-id="45ad5-253">Zawiera skrypty weryfikacji jQuery i jQuery.</span><span class="sxs-lookup"><span data-stu-id="45ad5-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="45ad5-254">Używa `<div />` pomocników `<span />` i [tagów,](xref:mvc/views/tag-helpers/intro) aby włączyć:</span><span class="sxs-lookup"><span data-stu-id="45ad5-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="45ad5-255">Sprawdzanie poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="45ad5-255">Client-side validation.</span></span>
  * <span data-ttu-id="45ad5-256">Renderowanie błędów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="45ad5-256">Validation error rendering.</span></span>

* <span data-ttu-id="45ad5-257">Generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="45ad5-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="45ad5-258">Zamieszczając formularz Utwórz bez wartości nazwy wyświetla komunikat o błędzie "Wymagane jest pole Nazwa".</span><span class="sxs-lookup"><span data-stu-id="45ad5-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="45ad5-259">na formularzu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-259">on the form.</span></span> <span data-ttu-id="45ad5-260">Jeśli javascript jest włączony na kliencie, przeglądarka wyświetla błąd bez księgowania na serwerze.</span><span class="sxs-lookup"><span data-stu-id="45ad5-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="45ad5-261">Atrybut `[StringLength(10)]` generuje `data-val-length-max="10"` na renderowanym html.</span><span class="sxs-lookup"><span data-stu-id="45ad5-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="45ad5-262">`data-val-length-max`uniemożliwia przeglądarkom wprowadzenie większej niż maksymalna określona długość.</span><span class="sxs-lookup"><span data-stu-id="45ad5-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="45ad5-263">Jeśli narzędzie takie jak [Fiddler](https://www.telerik.com/fiddler) jest używane do edytowania i odtwarzania posta:</span><span class="sxs-lookup"><span data-stu-id="45ad5-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="45ad5-264">Z nazwą dłuższą niż 10.</span><span class="sxs-lookup"><span data-stu-id="45ad5-264">With the name longer than 10.</span></span>
* <span data-ttu-id="45ad5-265">Komunikat o błędzie "Nazwa pola musi być ciągiem o maksymalnej długości 10."</span><span class="sxs-lookup"><span data-stu-id="45ad5-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="45ad5-266">zostanie zwrócona.</span><span class="sxs-lookup"><span data-stu-id="45ad5-266">is returned.</span></span>

<span data-ttu-id="45ad5-267">Należy wziąć `Movie` pod uwagę następujący model:</span><span class="sxs-lookup"><span data-stu-id="45ad5-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="45ad5-268">Atrybuty sprawdzania poprawności określają zachowanie do wymuszania właściwości modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="45ad5-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="45ad5-269">`Required` Atrybuty `MinimumLength` i atrybuty wskazują, że właściwość musi mieć wartość, ale nic nie uniemożliwia użytkownikowi wprowadzania biały znak, aby spełnić tę walidację.</span><span class="sxs-lookup"><span data-stu-id="45ad5-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="45ad5-270">Atrybut `RegularExpression` jest używany do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="45ad5-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="45ad5-271">W poprzednim kodzie "Genre":</span><span class="sxs-lookup"><span data-stu-id="45ad5-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="45ad5-272">Może używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="45ad5-272">Must only use letters.</span></span>
  * <span data-ttu-id="45ad5-273">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="45ad5-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="45ad5-274">Białe znaki, liczby i znaki specjalne nie są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="45ad5-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="45ad5-275">"Ocena": `RegularExpression`</span><span class="sxs-lookup"><span data-stu-id="45ad5-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="45ad5-276">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="45ad5-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="45ad5-277">Umożliwia znaki specjalne i liczby w kolejnych spacjach.</span><span class="sxs-lookup"><span data-stu-id="45ad5-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="45ad5-278">"PG-13" jest ważny dla oceny, ale nie dla "Gatunku".</span><span class="sxs-lookup"><span data-stu-id="45ad5-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="45ad5-279">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="45ad5-280">Atrybut `StringLength` ustawia maksymalną długość właściwości ciągu i opcjonalnie jego minimalną długość.</span><span class="sxs-lookup"><span data-stu-id="45ad5-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="45ad5-281">Typy wartości `decimal`(takie `float` `DateTime`jak , `int`, , ) są `[Required]` z natury wymagane i nie potrzebują atrybutu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="45ad5-282">Strona Utwórz `Movie` dla modelu wyświetla błędy z nieprawidłowymi wartościami:</span><span class="sxs-lookup"><span data-stu-id="45ad5-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formularz widoku filmu z wieloma błędami sprawdzania poprawności po stronie klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="45ad5-284">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="45ad5-284">For more information, see:</span></span>

* [<span data-ttu-id="45ad5-285">Dodawanie sprawdzania poprawności do aplikacji Movie</span><span class="sxs-lookup"><span data-stu-id="45ad5-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="45ad5-286">[Sprawdzanie poprawności modelu w ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="45ad5-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="45ad5-287">Obsługa żądań HEAD za pomocą rezerwowego programu OnGet</span><span class="sxs-lookup"><span data-stu-id="45ad5-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="45ad5-288">`HEAD`żądania umożliwiają pobieranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="45ad5-289">W `GET` przeciwieństwie `HEAD` do żądań żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45ad5-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="45ad5-290">Zwykle program `OnHead` obsługi jest tworzony i `HEAD` wywoływany dla żądań:</span><span class="sxs-lookup"><span data-stu-id="45ad5-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="45ad5-291">Razor Pages powraca do `OnGet` wywoływania `OnHead` programu obsługi, jeśli nie jest zdefiniowany program obsługi.</span><span class="sxs-lookup"><span data-stu-id="45ad5-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="45ad5-292">Strony XSRF/CSRF i Razor</span><span class="sxs-lookup"><span data-stu-id="45ad5-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="45ad5-293">Strony brzytwy są chronione przez [walidację antyforgery.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="45ad5-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="45ad5-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wstrzykuje tokeny antyforgery do elementów formularza HTML.</span><span class="sxs-lookup"><span data-stu-id="45ad5-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="45ad5-295">Używanie układów, częściowych, szablonów i pomocników znaczników ze stronami Razor</span><span class="sxs-lookup"><span data-stu-id="45ad5-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="45ad5-296">Strony działają ze wszystkimi możliwościami silnika widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="45ad5-297">Układy, częściowe, szablony, Pomocników tagów, *_ViewStart.cshtml*i *_ViewImports.cshtml* działają w taki sam sposób, jak w przypadku konwencjonalnych widoków Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="45ad5-298">Rozsyłajmy tę stronę, korzystając z niektórych z tych możliwości.</span><span class="sxs-lookup"><span data-stu-id="45ad5-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="45ad5-299">Dodawanie [strony układu](xref:mvc/views/layout) do *strony/udostępnione/_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="45ad5-300">[Układ:](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="45ad5-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="45ad5-301">Steruje układem każdej strony (chyba że strona zrezygnuje z układu).</span><span class="sxs-lookup"><span data-stu-id="45ad5-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="45ad5-302">Importuje struktury HTML, takie jak JavaScript i arkusze stylów.</span><span class="sxs-lookup"><span data-stu-id="45ad5-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="45ad5-303">Zawartość strony Razor są renderowane, gdzie `@RenderBody()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="45ad5-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="45ad5-304">Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="45ad5-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="45ad5-305">[Właściwość Layout](xref:mvc/views/layout#specifying-a-layout) jest ustawiona w *pliku Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="45ad5-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="45ad5-306">Układ znajduje się w folderze *Strony/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="45ad5-307">Strony wyszukają inne widoki (układy, szablony, częściowe) hierarchicznie, zaczynając w tym samym folderze co bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="45ad5-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="45ad5-308">Układ w folderze *Strony/Udostępnione* może być używany z dowolnej strony Razor w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="45ad5-309">Plik układu powinien znajdować się w folderze *Strony/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="45ad5-310">Zaleca **się,** aby nie umieszczać pliku układu w folderze *Widoki/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="45ad5-311">*Widoki/Udostępnione* jest wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="45ad5-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="45ad5-312">Strony razor są przeznaczone do polegania na hierarchii folderów, a nie na konwencjach ścieżek.</span><span class="sxs-lookup"><span data-stu-id="45ad5-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="45ad5-313">Wyświetlanie wyszukiwania ze strony Razor zawiera folder *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="45ad5-314">Układy, szablony i częściowe używane z kontrolerami MVC i konwencjonalnymi widokami Razor *po prostu działają.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="45ad5-315">Dodawanie pliku *Pages/_ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="45ad5-316">`@namespace`jest wyjaśnione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="45ad5-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="45ad5-317">Dyrektywa `@addTagHelper` wprowadza [wbudowane Pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="45ad5-318">Dyrektywa `@namespace` ustawiona na stronie:</span><span class="sxs-lookup"><span data-stu-id="45ad5-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="45ad5-319">Dyrektywa `@namespace` ustawia obszar nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="45ad5-320">Dyrektywa `@model` nie musi zawierać obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="45ad5-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="45ad5-321">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports.cshtml,* określony obszar nazw dostarcza prefiks wygenerowanego obszaru nazw `@namespace` na stronie, która importuje dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="45ad5-322">Pozostała część wygenerowanego obszaru nazw (część sufiksu) to ścieżka względna oddzielona kropkami między folderem zawierającym *_ViewImports.cshtml* a folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="45ad5-323">Na przykład `PageModel` klasa *Pages/Customers/Edit.cshtml.cs* jawnie ustawia obszar nazw:</span><span class="sxs-lookup"><span data-stu-id="45ad5-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="45ad5-324">Plik *Pages/_ViewImports.cshtml* ustawia następujący obszar nazw:</span><span class="sxs-lookup"><span data-stu-id="45ad5-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="45ad5-325">Wygenerowany obszar nazw dla *strony Pages/Customers/Edit.cshtml* Razor `PageModel` Page jest taki sam jak klasa.</span><span class="sxs-lookup"><span data-stu-id="45ad5-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="45ad5-326">`@namespace`*współpracuje również z konwencjonalnymi widokami Razor.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="45ad5-327">Należy wziąć pod uwagę plik widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="45ad5-328">Zaktualizowany plik widoku *Pages/Create.cshtml* z *_ViewImports.cshtml* i poprzednim plikiem układu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="45ad5-329">W poprzednim kodzie *_ViewImports.cshtml* zaimportował obszar nazw i Pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="45ad5-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="45ad5-330">Plik układu zaimportował pliki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="45ad5-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="45ad5-331">[Projekt startowy Razor Pages](#rpvs17) zawiera *pages/_ValidationScriptsPartial.cshtml*, który łączy sprawdzanie poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="45ad5-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="45ad5-332">Aby uzyskać więcej informacji <xref:mvc/views/partial>na temat widoków częściowych, zobacz .</span><span class="sxs-lookup"><span data-stu-id="45ad5-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="45ad5-333">Generowanie adresów URL dla stron</span><span class="sxs-lookup"><span data-stu-id="45ad5-333">URL generation for Pages</span></span>

<span data-ttu-id="45ad5-334">Strona, `Create` pokazana wcześniej, `RedirectToPage`używa:</span><span class="sxs-lookup"><span data-stu-id="45ad5-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="45ad5-335">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="45ad5-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="45ad5-336">*/Strony*</span><span class="sxs-lookup"><span data-stu-id="45ad5-336">*/Pages*</span></span>

  * <span data-ttu-id="45ad5-337">*Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="45ad5-338">*Prywatność.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="45ad5-339">*/Klienci*</span><span class="sxs-lookup"><span data-stu-id="45ad5-339">*/Customers*</span></span>

    * <span data-ttu-id="45ad5-340">*Utwórz.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="45ad5-341">*Edytuj.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="45ad5-342">*Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-342">*Index.cshtml*</span></span>

<span data-ttu-id="45ad5-343">*Strony Pages/Customers/Create.cshtml* i *Pages/Customers/Edit.cshtml* przekierowują strony *do stron/klientów/index.cshtml* po sukcesie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="45ad5-344">Ciąg `./Index` jest względną nazwą strony używaną do uzyskiwania dostępu do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="45ad5-345">Służy do generowania adresów URL na stronie *Pages/Customers/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="45ad5-346">Przykład:</span><span class="sxs-lookup"><span data-stu-id="45ad5-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="45ad5-347">Bezwzględna nazwa `/Index` strony służy do generowania adresów URL na stronie *Pages/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="45ad5-348">Przykład:</span><span class="sxs-lookup"><span data-stu-id="45ad5-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="45ad5-349">Nazwa strony jest ścieżką do strony z głównego */pages* folderu, w tym interlinia `/` (na przykład ). `/Index`</span><span class="sxs-lookup"><span data-stu-id="45ad5-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="45ad5-350">Poprzednie przykłady generowania adresów URL oferują ulepszone opcje i funkcjonalne możliwości w stosunku do twardego kodowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="45ad5-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="45ad5-351">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie z tym, jak trasa jest zdefiniowana w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="45ad5-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="45ad5-352">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="45ad5-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="45ad5-353">W poniższej tabeli przedstawiono, `RedirectToPage` która strona indeksu jest zaznaczona przy użyciu różnych parametrów w *pages/customers/create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="45ad5-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="45ad5-354">RedirectToPage(x)</span></span>| <span data-ttu-id="45ad5-355">Strona</span><span class="sxs-lookup"><span data-stu-id="45ad5-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="45ad5-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="45ad5-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="45ad5-357">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-357">*Pages/Index*</span></span> |
| <span data-ttu-id="45ad5-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="45ad5-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="45ad5-359">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="45ad5-360">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="45ad5-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="45ad5-361">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-361">*Pages/Index*</span></span> |
| <span data-ttu-id="45ad5-362">RedirectToPage("Indeks")</span><span class="sxs-lookup"><span data-stu-id="45ad5-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="45ad5-363">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="45ad5-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="45ad5-365">Parametr `RedirectToPage` jest *łączony* ze ścieżką bieżącej strony w celu obliczenia nazwy strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="45ad5-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="45ad5-366">Względne łączenie nazw jest przydatne, gdy place budowy o złożonej strukturze.</span><span class="sxs-lookup"><span data-stu-id="45ad5-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="45ad5-367">Gdy nazwy względne są używane do łączenia między stronami w folderze:</span><span class="sxs-lookup"><span data-stu-id="45ad5-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="45ad5-368">Zmiana nazwy folderu nie powoduje przerwania względnych łączy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="45ad5-369">Łącza nie są przerywane, ponieważ nie zawierają nazwy folderu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="45ad5-370">Aby przekierować do strony w innym [obszarze,](xref:mvc/controllers/areas)określ obszar:</span><span class="sxs-lookup"><span data-stu-id="45ad5-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="45ad5-371">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas> i <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="45ad5-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="45ad5-372">Atrybut ViewData</span><span class="sxs-lookup"><span data-stu-id="45ad5-372">ViewData attribute</span></span>

<span data-ttu-id="45ad5-373">Dane mogą być przekazywane <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>do strony z .</span><span class="sxs-lookup"><span data-stu-id="45ad5-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="45ad5-374">Właściwości z `[ViewData]` atrybutem mają swoje wartości przechowywane <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>i ładowane z .</span><span class="sxs-lookup"><span data-stu-id="45ad5-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="45ad5-375">W poniższym przykładzie `AboutModel` stosuje `[ViewData]` atrybut do `Title` właściwości:</span><span class="sxs-lookup"><span data-stu-id="45ad5-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="45ad5-376">Na stronie Informacje przejdź `Title` do właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="45ad5-377">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="45ad5-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="45ad5-378">TempData ( TempData )</span><span class="sxs-lookup"><span data-stu-id="45ad5-378">TempData</span></span>

<span data-ttu-id="45ad5-379">ASP.NET Core eksponuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>plik .</span><span class="sxs-lookup"><span data-stu-id="45ad5-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="45ad5-380">Ta właściwość przechowuje dane, dopóki nie jest odczytywany.</span><span class="sxs-lookup"><span data-stu-id="45ad5-380">This property stores data until it's read.</span></span> <span data-ttu-id="45ad5-381">I <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> metody mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="45ad5-382">`TempData`jest przydatne do przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="45ad5-383">Poniższy kod ustawia `Message` wartość `TempData`użycia:</span><span class="sxs-lookup"><span data-stu-id="45ad5-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="45ad5-384">Następujące znaczniki w pliku *Pages/Customers/Index.cshtml* wyświetlają wartość `Message` użycia `TempData`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="45ad5-385">Model strony *Pages/Customers/Index.cshtml.cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="45ad5-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="45ad5-386">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="45ad5-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="45ad5-387">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="45ad5-387">Multiple handlers per page</span></span>

<span data-ttu-id="45ad5-388">Następująca strona generuje znaczniki dla dwóch `asp-page-handler` programów obsługi przy użyciu Pomocnika znaczników:</span><span class="sxs-lookup"><span data-stu-id="45ad5-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="45ad5-389">Formularz w poprzednim przykładzie ma dwa przyciski `FormActionTagHelper` przesyłania, z których każdy używa do przesyłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="45ad5-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="45ad5-390">Atrybut `asp-page-handler` jest towarzyszem `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="45ad5-391">`asp-page-handler`generuje adresy URL, które przesyłają do każdej z metod obsługi zdefiniowanych przez stronę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="45ad5-392">`asp-page`nie jest określony, ponieważ próbka łączy się z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="45ad5-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="45ad5-393">Model strony:</span><span class="sxs-lookup"><span data-stu-id="45ad5-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="45ad5-394">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="45ad5-395">Nazwane metody obsługi są tworzone przez `On<HTTP Verb>` biorąc `Async` tekst w nazwie po i przed (jeśli jest obecny).</span><span class="sxs-lookup"><span data-stu-id="45ad5-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="45ad5-396">W poprzednim przykładzie metody strony są OnPost**JoinList**Async i OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="45ad5-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="45ad5-397">Po usunięciu *OnPost* i *Async* `JoinList` nazwy `JoinListUC`obsługi są i .</span><span class="sxs-lookup"><span data-stu-id="45ad5-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="45ad5-398">Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="45ad5-399">Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`którą się przesyła, to .</span><span class="sxs-lookup"><span data-stu-id="45ad5-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="45ad5-400">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="45ad5-400">Custom routes</span></span>

<span data-ttu-id="45ad5-401">Za `@page` pomocą dyrektywy można:</span><span class="sxs-lookup"><span data-stu-id="45ad5-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="45ad5-402">Określ niestandardową trasę do strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-402">Specify a custom route to a page.</span></span> <span data-ttu-id="45ad5-403">Na przykład trasę do strony Informacje można `/Some/Other/Path` `@page "/Some/Other/Path"`ustawić za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="45ad5-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="45ad5-404">Dołączanie segmentów do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-404">Append segments to a page's default route.</span></span> <span data-ttu-id="45ad5-405">Na przykład segment "element" można dodać do domyślnej `@page "item"`trasy strony za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="45ad5-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="45ad5-406">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="45ad5-407">Na przykład parametr ID, `id`może być wymagany dla `@page "{id}"`strony z .</span><span class="sxs-lookup"><span data-stu-id="45ad5-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="45ad5-408">Ścieżka względna katalogu głównego wyznaczona`~`przez tyldę ( ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="45ad5-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="45ad5-409">Na przykład `@page "~/Some/Other/Path"` jest taka `@page "/Some/Other/Path"`sama jak .</span><span class="sxs-lookup"><span data-stu-id="45ad5-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="45ad5-410">Jeśli nie podoba Ci się `?handler=JoinList` ciąg zapytania w adresie URL, zmień trasę, aby umieścić nazwę programu obsługi w części ścieżki adresu URL.</span><span class="sxs-lookup"><span data-stu-id="45ad5-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="45ad5-411">Trasę można dostosować, dodając szablon trasy ujęty w `@page` cudzysłowie po dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="45ad5-412">Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="45ad5-413">Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH/JoinListUC`którą się przesyła, to .</span><span class="sxs-lookup"><span data-stu-id="45ad5-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="45ad5-414">`?` Poniżej `handler` oznacza, że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="45ad5-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="45ad5-415">Zaawansowana konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="45ad5-415">Advanced configuration and settings</span></span>

<span data-ttu-id="45ad5-416">Konfiguracja i ustawienia w poniższych sekcjach nie są wymagane przez większość aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="45ad5-417">Aby skonfigurować opcje zaawansowane, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>użyj metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="45ad5-417">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="45ad5-418">Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="45ad5-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="45ad5-419">Aby uzyskać więcej informacji na temat konwencji, zobacz [Konwencje autoryzacji stron razor](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="45ad5-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="45ad5-420">Aby wstępnie skompilować widoki, zobacz [Kompilacja widoku Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="45ad5-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="45ad5-421">Określ, że strony Razor znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="45ad5-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="45ad5-422">Domyślnie strony Razor są zakorzenione w katalogu */Pages.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="45ad5-423">Dodaj, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> aby określić, że strony Razor znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) aplikacji:</span><span class="sxs-lookup"><span data-stu-id="45ad5-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="45ad5-424">Określanie, że strony Razor znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="45ad5-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="45ad5-425">Dodaj, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> aby określić, że strony Razor znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="45ad5-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="45ad5-426">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="45ad5-426">Additional resources</span></span>

* <span data-ttu-id="45ad5-427">Zobacz [Wprowadzenie do strony Razor Pages](xref:tutorials/razor-pages/razor-pages-start), która opiera się na tym wprowadzeniu</span><span class="sxs-lookup"><span data-stu-id="45ad5-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="45ad5-428">Pobieranie lub wyświetlanie przykładowego kodu</span><span class="sxs-lookup"><span data-stu-id="45ad5-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

<span data-ttu-id="45ad5-429">Autorstwa [Ricka Andersona](https://twitter.com/RickAndMSFT) i [Ryana Nowaka](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="45ad5-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="45ad5-430">Razor Pages to nowy aspekt ASP.NET Core MVC, który sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="45ad5-430">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="45ad5-431">Jeśli szukasz samouczka, który używa podejścia Model-View-Controller, zobacz [Wprowadzenie do ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="45ad5-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="45ad5-432">Ten dokument zawiera wprowadzenie do stron Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="45ad5-433">To nie jest samouczek krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="45ad5-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="45ad5-434">Jeśli niektóre sekcje są zbyt zaawansowane, zobacz [Wprowadzenie do strony Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="45ad5-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="45ad5-435">Aby zapoznać się z omówieniem ASP.NET Core, zobacz [Wprowadzenie do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="45ad5-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="45ad5-436">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="45ad5-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45ad5-437">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45ad5-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="45ad5-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="45ad5-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="45ad5-439">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="45ad5-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="45ad5-440">Tworzenie projektu strony razor</span><span class="sxs-lookup"><span data-stu-id="45ad5-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45ad5-441">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45ad5-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="45ad5-442">Zobacz [Wprowadzenie do strony Razor Pages,](xref:tutorials/razor-pages/razor-pages-start) aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="45ad5-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="45ad5-443">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="45ad5-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="45ad5-444">Uruchom `dotnet new webapp` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="45ad5-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="45ad5-445">Otwórz wygenerowany plik *csproj* z programu Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="45ad5-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="45ad5-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="45ad5-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="45ad5-447">Uruchom `dotnet new webapp` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="45ad5-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="45ad5-448">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="45ad5-448">Razor Pages</span></span>

<span data-ttu-id="45ad5-449">Strona maszynki do golenia jest włączona w *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-449">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="45ad5-450">Rozważmy podstawową stronę:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="45ad5-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="45ad5-451">Poprzedni kod wygląda podobnie do [pliku widoku Razor](xref:tutorials/first-mvc-app/adding-view) używanego w aplikacji ASP.NET Core z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="45ad5-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="45ad5-452">To, co sprawia, że jest inny, `@page` to dyrektywa.</span><span class="sxs-lookup"><span data-stu-id="45ad5-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="45ad5-453">`@page`sprawia, że plik do akcji MVC - co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="45ad5-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="45ad5-454">`@page`musi być pierwszą dyrektywą Razor na stronie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="45ad5-455">`@page`wpływa na zachowanie innych konstrukcji Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="45ad5-456">Podobna strona, `PageModel` przy użyciu klasy, jest wyświetlana w następujących dwóch plikach.</span><span class="sxs-lookup"><span data-stu-id="45ad5-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="45ad5-457">Plik *Pages/Index2.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="45ad5-458">Model strony *Pages/Index2.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="45ad5-459">Zgodnie z `PageModel` konwencją plik klasy ma taką samą nazwę jak plik Razor Page z dołączona do *pliku cs.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="45ad5-460">Na przykład poprzednia strona Razor to *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="45ad5-461">Plik zawierający `PageModel` klasę nosi nazwę *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="45ad5-462">Skojarzenia ścieżek URL ze stronami są określane przez lokalizację strony w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="45ad5-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="45ad5-463">W poniższej tabeli przedstawiono ścieżkę strony Razor i pasujący adres URL:</span><span class="sxs-lookup"><span data-stu-id="45ad5-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="45ad5-464">Nazwa pliku i ścieżka</span><span class="sxs-lookup"><span data-stu-id="45ad5-464">File name and path</span></span>               | <span data-ttu-id="45ad5-465">pasujący adres URL</span><span class="sxs-lookup"><span data-stu-id="45ad5-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="45ad5-466">*/Strony/Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="45ad5-467">`/` lub `/Index`</span><span class="sxs-lookup"><span data-stu-id="45ad5-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="45ad5-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="45ad5-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="45ad5-470">*/Strony/Sklep/Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="45ad5-471">`/Store` lub `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="45ad5-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="45ad5-472">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="45ad5-472">Notes:</span></span>

* <span data-ttu-id="45ad5-473">Środowisko wykonawcze domyślnie wyszukuje pliki Razor Pages w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="45ad5-474">`Index`jest stroną domyślną, gdy adres URL nie zawiera strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="45ad5-475">Napisz formularz podstawowy</span><span class="sxs-lookup"><span data-stu-id="45ad5-475">Write a basic form</span></span>

<span data-ttu-id="45ad5-476">Razor Pages został zaprojektowany tak, aby typowe wzorce używane z przeglądarkami internetowymi były łatwe do wdrożenia podczas tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-476">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="45ad5-477">[Powiązanie modelu,](xref:mvc/models/model-binding) [Pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML *po prostu pracować* z właściwości zdefiniowane w razor page klasy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="45ad5-478">Rozważmy stronę, która implementuje podstawowy formularz `Contact` "skontaktuj się z nami" dla modelu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="45ad5-479">Dla przykładów w tym `DbContext` dokumencie jest inicjowany w pliku [Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16)</span><span class="sxs-lookup"><span data-stu-id="45ad5-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="45ad5-480">Model danych:</span><span class="sxs-lookup"><span data-stu-id="45ad5-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="45ad5-481">Kontekst db:</span><span class="sxs-lookup"><span data-stu-id="45ad5-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="45ad5-482">Plik widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="45ad5-483">Model strony *Pages/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="45ad5-484">Zgodnie z `PageModel` konwencją `<PageName>Model` klasa jest wywoływana i znajduje się w tej samej przestrzeni nazw co strona.</span><span class="sxs-lookup"><span data-stu-id="45ad5-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="45ad5-485">Klasa `PageModel` umożliwia oddzielenie logiki strony od jej prezentacji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="45ad5-486">Definiuje programy obsługi stron dla żądań wysyłanych do strony i dane używane do renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="45ad5-487">Separacja ta pozwala na:</span><span class="sxs-lookup"><span data-stu-id="45ad5-487">This separation allows:</span></span>

* <span data-ttu-id="45ad5-488">Zarządzanie zależnościami stron poprzez [iniekcję zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="45ad5-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="45ad5-489">[Jednostka testująca](xref:test/razor-pages-tests) strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="45ad5-490">Strona ma `OnPostAsync` *metodę obsługi*, `POST` która jest uruchamiana na żądaniach (gdy użytkownik publikuje formularz).</span><span class="sxs-lookup"><span data-stu-id="45ad5-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="45ad5-491">Metody procedury obsługi można dodać dla dowolnego czasownika HTTP.</span><span class="sxs-lookup"><span data-stu-id="45ad5-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="45ad5-492">Najczęstsze procedury obsługi to:</span><span class="sxs-lookup"><span data-stu-id="45ad5-492">The most common handlers are:</span></span>

* <span data-ttu-id="45ad5-493">`OnGet` — na potrzeby inicjowania stanu wymaganego dla strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="45ad5-494">[OnGet](#OnGet) próbki.</span><span class="sxs-lookup"><span data-stu-id="45ad5-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="45ad5-495">`OnPost` — na potrzeby obsługi przesłanych formularzy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="45ad5-496">Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="45ad5-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="45ad5-497">Powyższy kod jest typowy dla stron Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="45ad5-498">Jeśli znasz ASP.NET aplikacje za pomocą kontrolerów i widoków:</span><span class="sxs-lookup"><span data-stu-id="45ad5-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="45ad5-499">Kod `OnPostAsync` w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="45ad5-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="45ad5-500">Większość ćwierczy MVC, takich jak [powiązanie modelu,](xref:mvc/models/model-binding) [sprawdzanie poprawności,](xref:mvc/models/validation) [Sprawdzanie poprawności](xref:mvc/models/validation)i wyniki akcji są współużytkowane.</span><span class="sxs-lookup"><span data-stu-id="45ad5-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="45ad5-501">Poprzednia `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="45ad5-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="45ad5-502">Podstawowy `OnPostAsync`przepływ:</span><span class="sxs-lookup"><span data-stu-id="45ad5-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="45ad5-503">Sprawdź, czy nie ma błędów sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="45ad5-503">Check for validation errors.</span></span>

* <span data-ttu-id="45ad5-504">Jeśli nie ma żadnych błędów, zapisz dane i przekieruj.</span><span class="sxs-lookup"><span data-stu-id="45ad5-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="45ad5-505">Jeśli występują błędy, pokaż stronę ponownie z komunikatami sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="45ad5-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="45ad5-506">Sprawdzanie poprawności po stronie klienta jest identyczne z tradycyjnymi aplikacjami core MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="45ad5-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="45ad5-507">W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="45ad5-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="45ad5-508">Po pomyślnym wprowadzeniu danych `OnPostAsync` metoda obsługi `RedirectToPage` wywołuje metodę pomocnika, aby `RedirectToPageResult`zwrócić wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="45ad5-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="45ad5-509">`RedirectToPage`jest nowym wynikiem akcji, `RedirectToAction` `RedirectToRoute`podobnym do lub , ale dostosowanym do stron.</span><span class="sxs-lookup"><span data-stu-id="45ad5-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="45ad5-510">W poprzednim przykładzie przekierowuje do głównej`/Index`strony indeksu ( ).</span><span class="sxs-lookup"><span data-stu-id="45ad5-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="45ad5-511">`RedirectToPage`jest szczegółowo opisana w sekcji [generowanie adresów URL dla stron.](#url_gen)</span><span class="sxs-lookup"><span data-stu-id="45ad5-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="45ad5-512">Gdy przesłany formularz ma błędy sprawdzania poprawności (które`OnPostAsync` są przekazywane `Page` do serwera), metoda obsługi wywołuje metodę pomocnika.</span><span class="sxs-lookup"><span data-stu-id="45ad5-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="45ad5-513">Metoda `Page` zwraca wystąpienie klasy `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="45ad5-514">Powrót `Page` jest podobny do tego, `View`jak akcje w kontrolerach zwracają .</span><span class="sxs-lookup"><span data-stu-id="45ad5-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="45ad5-515">`PageResult`jest domyślnym typem zwracanym dla metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="45ad5-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="45ad5-516">Metoda obsługi, `void` która zwraca renderowania strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="45ad5-517">Właściwość `Customer` używa `[BindProperty]` atrybutu, aby zdecydować się na powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="45ad5-518">Razor Pages, domyślnie, powiązać właściwości`GET` tylko z non-zleceń.</span><span class="sxs-lookup"><span data-stu-id="45ad5-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="45ad5-519">Utworzenie powiązania z właściwościami może zmniejszyć ilość kodu, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="45ad5-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="45ad5-520">Powiązanie zmniejsza kod przy użyciu tej`<input asp-for="Customer.Name">`samej właściwości do renderowania pól formularza ( ) i zaakceptować dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="45ad5-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="45ad5-521">Strona główna (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="45ad5-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="45ad5-522">Powiązana `PageModel` klasa (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="45ad5-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="45ad5-523">Plik *Index.cshtml* zawiera następujące znaczniki umożliwiające utworzenie łącza edycji dla każdego kontaktu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="45ad5-524">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocnik znacznika kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył tego atrybutu `asp-route-{value}` do wygenerowania łącza do strony Edycji.</span><span class="sxs-lookup"><span data-stu-id="45ad5-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="45ad5-525">Łącze zawiera dane trasy o identyfikatorze kontaktu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="45ad5-526">Na przykład `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="45ad5-527">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="45ad5-528">Pomocnik tagów jest włączona przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="45ad5-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="45ad5-529">Plik *Pages/Edit.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="45ad5-530">Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="45ad5-531">Ograniczenie`"{id:int}"` routingu nakazuje stronie akceptowanie żądań na stronie zawierającej `int` dane trasy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="45ad5-532">Jeśli żądanie na stronie nie zawiera danych trasy, które `int`można przekonwertować na , środowisko wykonawcze zwraca błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="45ad5-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="45ad5-533">Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:</span><span class="sxs-lookup"><span data-stu-id="45ad5-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="45ad5-534">Plik *Pages/Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="45ad5-535">Plik *Index.cshtml* zawiera również znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="45ad5-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="45ad5-536">Gdy przycisk usuwania jest renderowany `formaction` w formacie HTML, zawiera parametry dla:</span><span class="sxs-lookup"><span data-stu-id="45ad5-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="45ad5-537">Identyfikator kontaktu z klientem `asp-route-id` określony przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="45ad5-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="45ad5-538">Określony `handler` przez `asp-page-handler` atrybut.</span><span class="sxs-lookup"><span data-stu-id="45ad5-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="45ad5-539">Oto przykład renderowanego przycisku usuwania z identyfikatorem `1`kontaktu z klientem:</span><span class="sxs-lookup"><span data-stu-id="45ad5-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="45ad5-540">Po wybraniu przycisku `POST` żądanie formularza jest wysyłane do serwera.</span><span class="sxs-lookup"><span data-stu-id="45ad5-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="45ad5-541">Zgodnie z konwencją nazwa metody obsługi jest wybierana `handler` na podstawie `OnPost[handler]Async`wartości parametru zgodnie ze schematem .</span><span class="sxs-lookup"><span data-stu-id="45ad5-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="45ad5-542">Ponieważ `handler` jest `delete` w tym `OnPostDeleteAsync` przykładzie, metoda obsługi `POST` jest używana do przetwarzania żądania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="45ad5-543">Jeśli `asp-page-handler` jest ustawiona na inną `remove`wartość, na przykład `OnPostRemoveAsync` , wybrana jest metoda obsługi o nazwie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="45ad5-544">Poniższy kod `OnPostDeleteAsync` pokazuje program obsługi:</span><span class="sxs-lookup"><span data-stu-id="45ad5-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="45ad5-545">Metoda: `OnPostDeleteAsync`</span><span class="sxs-lookup"><span data-stu-id="45ad5-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="45ad5-546">Akceptuje ciąg `id` zapytania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="45ad5-547">Jeśli dyrektywa strony *Index.cshtml* zawierała `"{id:int?}"` `id` ograniczenie routingu, pochodziłoby z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="45ad5-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="45ad5-548">Dane trasy `id` dla jest określony w `https://localhost:5001/Customers/2`identyfikatorze URI, takich jak .</span><span class="sxs-lookup"><span data-stu-id="45ad5-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="45ad5-549">Wysyła zapytania do bazy danych `FindAsync`dla kontaktu z klientem za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="45ad5-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="45ad5-550">Jeśli kontakt z klientem zostanie znaleziony, zostaną one usunięte z listy kontaktów z klientami.</span><span class="sxs-lookup"><span data-stu-id="45ad5-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="45ad5-551">Baza danych jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="45ad5-551">The database is updated.</span></span>
* <span data-ttu-id="45ad5-552">Wywołania `RedirectToPage` przekierowania do głównej`/Index`strony indeksu ( ).</span><span class="sxs-lookup"><span data-stu-id="45ad5-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="45ad5-553">Oznaczanie właściwości strony zgodnie z wymaganiami</span><span class="sxs-lookup"><span data-stu-id="45ad5-553">Mark page properties as required</span></span>

<span data-ttu-id="45ad5-554">Właściwości na `PageModel` a mogą być oznaczone atrybutem [Wymagane:](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)</span><span class="sxs-lookup"><span data-stu-id="45ad5-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="45ad5-555">Aby uzyskać więcej informacji, zobacz [Sprawdzanie poprawności modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="45ad5-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="45ad5-556">Obsługa żądań HEAD za pomocą rezerwowego programu OnGet</span><span class="sxs-lookup"><span data-stu-id="45ad5-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="45ad5-557">`HEAD`żądania umożliwiają pobieranie nagłówków dla określonego zasobu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="45ad5-558">W `GET` przeciwieństwie `HEAD` do żądań żądania nie zwracają treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45ad5-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="45ad5-559">Zwykle program `OnHead` obsługi jest tworzony i `HEAD` wywoływany dla żądań:</span><span class="sxs-lookup"><span data-stu-id="45ad5-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="45ad5-560">W ASP.NET Core 2.1 lub nowszym Razor Pages `OnGet` powraca `OnHead` do wywoływania programu obsługi, jeśli nie jest zdefiniowany program obsługi.</span><span class="sxs-lookup"><span data-stu-id="45ad5-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="45ad5-561">To zachowanie jest włączone przez wywołanie [SetCompatibilityVersion](xref:mvc/compatibility-version) w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="45ad5-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="45ad5-562">Domyślne szablony `SetCompatibilityVersion` generują wywołanie w ASP.NET Core 2.1 i 2.2.</span><span class="sxs-lookup"><span data-stu-id="45ad5-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="45ad5-563">`SetCompatibilityVersion`skutecznie ustawia opcję `AllowMappingHeadRequestsToGetHandler` Razor `true`Pages na .</span><span class="sxs-lookup"><span data-stu-id="45ad5-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="45ad5-564">Zamiast włączać wszystkie zachowania za `SetCompatibilityVersion`pomocą , możesz wyraźnie zdecydować się na *określone* zachowania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="45ad5-565">Poniższy kod zezwala na `HEAD` mapowanie żądań `OnGet` do programu obsługi:</span><span class="sxs-lookup"><span data-stu-id="45ad5-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="45ad5-566">Strony XSRF/CSRF i Razor</span><span class="sxs-lookup"><span data-stu-id="45ad5-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="45ad5-567">Nie musisz pisać żadnego kodu do [sprawdzania poprawności antyforgery.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="45ad5-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="45ad5-568">Generowanie i walidacja tokenów antyforgery są automatycznie uwzględniane na stronach Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="45ad5-569">Używanie układów, częściowych, szablonów i pomocników znaczników ze stronami Razor</span><span class="sxs-lookup"><span data-stu-id="45ad5-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="45ad5-570">Strony działają ze wszystkimi możliwościami silnika widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="45ad5-571">Układy, częściowe, szablony, Pomocników tagów, *_ViewStart.cshtml*, *_ViewImports.cshtml* działają w taki sam sposób, jak w przypadku konwencjonalnych widoków Razor.</span><span class="sxs-lookup"><span data-stu-id="45ad5-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="45ad5-572">Rozsyłajmy tę stronę, korzystając z niektórych z tych możliwości.</span><span class="sxs-lookup"><span data-stu-id="45ad5-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="45ad5-573">Dodawanie [strony układu](xref:mvc/views/layout) do *strony/udostępnione/_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="45ad5-574">[Układ:](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="45ad5-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="45ad5-575">Steruje układem każdej strony (chyba że strona zrezygnuje z układu).</span><span class="sxs-lookup"><span data-stu-id="45ad5-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="45ad5-576">Importuje struktury HTML, takie jak JavaScript i arkusze stylów.</span><span class="sxs-lookup"><span data-stu-id="45ad5-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="45ad5-577">Więcej informacji można znaleźć na [stronie układu.](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="45ad5-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="45ad5-578">[Właściwość Layout](xref:mvc/views/layout#specifying-a-layout) jest ustawiona w *pliku Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="45ad5-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="45ad5-579">Układ znajduje się w folderze *Strony/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="45ad5-580">Strony wyszukają inne widoki (układy, szablony, częściowe) hierarchicznie, zaczynając w tym samym folderze co bieżąca strona.</span><span class="sxs-lookup"><span data-stu-id="45ad5-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="45ad5-581">Układ w folderze *Strony/Udostępnione* może być używany z dowolnej strony Razor w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="45ad5-582">Plik układu powinien znajdować się w folderze *Strony/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="45ad5-583">Zaleca **się,** aby nie umieszczać pliku układu w folderze *Widoki/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="45ad5-584">*Widoki/Udostępnione* jest wzorcem widoków MVC.</span><span class="sxs-lookup"><span data-stu-id="45ad5-584">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="45ad5-585">Strony razor są przeznaczone do polegania na hierarchii folderów, a nie na konwencjach ścieżek.</span><span class="sxs-lookup"><span data-stu-id="45ad5-585">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="45ad5-586">Wyświetlanie wyszukiwania ze strony Razor zawiera folder *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="45ad5-587">Układy, szablony i części używane z kontrolerami MVC i konwencjonalnymi widokami Razor *po prostu działają.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="45ad5-588">Dodawanie pliku *Pages/_ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="45ad5-589">`@namespace`jest wyjaśnione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="45ad5-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="45ad5-590">Dyrektywa `@addTagHelper` wprowadza [wbudowane Pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="45ad5-591">Gdy `@namespace` dyrektywa jest używana jawnie na stronie:</span><span class="sxs-lookup"><span data-stu-id="45ad5-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="45ad5-592">Dyrektywa ustawia obszar nazw dla strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="45ad5-593">Dyrektywa `@model` nie musi zawierać obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="45ad5-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="45ad5-594">Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports.cshtml,* określony obszar nazw dostarcza prefiks wygenerowanego obszaru nazw `@namespace` na stronie, która importuje dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="45ad5-595">Pozostała część wygenerowanego obszaru nazw (część sufiksu) to ścieżka względna oddzielona kropkami między folderem zawierającym *_ViewImports.cshtml* a folderem zawierającym stronę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="45ad5-596">Na przykład `PageModel` klasa *Pages/Customers/Edit.cshtml.cs* jawnie ustawia obszar nazw:</span><span class="sxs-lookup"><span data-stu-id="45ad5-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="45ad5-597">Plik *Pages/_ViewImports.cshtml* ustawia następujący obszar nazw:</span><span class="sxs-lookup"><span data-stu-id="45ad5-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="45ad5-598">Wygenerowany obszar nazw dla *strony Pages/Customers/Edit.cshtml* Razor `PageModel` Page jest taki sam jak klasa.</span><span class="sxs-lookup"><span data-stu-id="45ad5-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="45ad5-599">`@namespace`*współpracuje również z konwencjonalnymi widokami Razor.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="45ad5-600">Oryginalny plik widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="45ad5-601">Zaktualizowany plik widoku *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="45ad5-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="45ad5-602">[Projekt startowy Razor Pages](#rpvs17) zawiera *pages/_ValidationScriptsPartial.cshtml*, który łączy sprawdzanie poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="45ad5-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="45ad5-603">Aby uzyskać więcej informacji <xref:mvc/views/partial>na temat widoków częściowych, zobacz .</span><span class="sxs-lookup"><span data-stu-id="45ad5-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="45ad5-604">Generowanie adresów URL dla stron</span><span class="sxs-lookup"><span data-stu-id="45ad5-604">URL generation for Pages</span></span>

<span data-ttu-id="45ad5-605">Strona, `Create` pokazana wcześniej, `RedirectToPage`używa:</span><span class="sxs-lookup"><span data-stu-id="45ad5-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="45ad5-606">Aplikacja ma następującą strukturę plików/folderów:</span><span class="sxs-lookup"><span data-stu-id="45ad5-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="45ad5-607">*/Strony*</span><span class="sxs-lookup"><span data-stu-id="45ad5-607">*/Pages*</span></span>

  * <span data-ttu-id="45ad5-608">*Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="45ad5-609">*/Klienci*</span><span class="sxs-lookup"><span data-stu-id="45ad5-609">*/Customers*</span></span>

    * <span data-ttu-id="45ad5-610">*Utwórz.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="45ad5-611">*Edytuj.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="45ad5-612">*Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="45ad5-612">*Index.cshtml*</span></span>

<span data-ttu-id="45ad5-613">*Strony Pages/Customers/Create.cshtml* i *Pages/Customers/Edit.cshtml* przekierowują strony *pages/index.cshtml* po sukcesie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="45ad5-614">Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="45ad5-615">Ciąg `/Index` może służyć do generowania identyfikatorów URI na stronie *Pages/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="45ad5-616">Przykład:</span><span class="sxs-lookup"><span data-stu-id="45ad5-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="45ad5-617">Nazwa strony jest ścieżką do strony z głównego */pages* folderu, w tym interlinia `/` (na przykład ). `/Index`</span><span class="sxs-lookup"><span data-stu-id="45ad5-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="45ad5-618">Poprzednie przykłady generowania adresów URL oferują ulepszone opcje i funkcjonalne możliwości za ok.</span><span class="sxs-lookup"><span data-stu-id="45ad5-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="45ad5-619">Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie z tym, jak trasa jest zdefiniowana w ścieżce docelowej.</span><span class="sxs-lookup"><span data-stu-id="45ad5-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="45ad5-620">Generowanie adresów URL dla stron obsługuje nazwy względne.</span><span class="sxs-lookup"><span data-stu-id="45ad5-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="45ad5-621">W poniższej tabeli przedstawiono, `RedirectToPage` która strona indeksu jest wybrana z różnymi parametrami niż *Strony/Klienci/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="45ad5-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="45ad5-622">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="45ad5-622">RedirectToPage(x)</span></span>| <span data-ttu-id="45ad5-623">Strona</span><span class="sxs-lookup"><span data-stu-id="45ad5-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="45ad5-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="45ad5-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="45ad5-625">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-625">*Pages/Index*</span></span> |
| <span data-ttu-id="45ad5-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="45ad5-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="45ad5-627">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="45ad5-628">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="45ad5-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="45ad5-629">*Strony/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-629">*Pages/Index*</span></span> |
| <span data-ttu-id="45ad5-630">RedirectToPage("Indeks")</span><span class="sxs-lookup"><span data-stu-id="45ad5-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="45ad5-631">*Strony/klienci/indeks*</span><span class="sxs-lookup"><span data-stu-id="45ad5-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="45ad5-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`i `RedirectToPage("../Index")` są *nazwami względnymi*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="45ad5-633">Parametr `RedirectToPage` jest *łączony* ze ścieżką bieżącej strony w celu obliczenia nazwy strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="45ad5-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="45ad5-634">Względne łączenie nazw jest przydatne, gdy place budowy o złożonej strukturze.</span><span class="sxs-lookup"><span data-stu-id="45ad5-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="45ad5-635">Jeśli nazwy względne są używane do łączenia między stronami w folderze, można zmienić nazwę tego folderu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="45ad5-636">Wszystkie łącza nadal działają (ponieważ nie zawierały nazwy folderu).</span><span class="sxs-lookup"><span data-stu-id="45ad5-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="45ad5-637">Aby przekierować do strony w innym [obszarze,](xref:mvc/controllers/areas)określ obszar:</span><span class="sxs-lookup"><span data-stu-id="45ad5-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="45ad5-638">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="45ad5-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="45ad5-639">Atrybut ViewData</span><span class="sxs-lookup"><span data-stu-id="45ad5-639">ViewData attribute</span></span>

<span data-ttu-id="45ad5-640">Dane mogą być przekazywane do strony z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="45ad5-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="45ad5-641">Właściwości na kontrolerach lub modelach `[ViewData]` razor Page z atrybutem mają swoje wartości przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="45ad5-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="45ad5-642">W poniższym przykładzie `AboutModel` `Title` zawiera właściwość oznaczoną `[ViewData]`symbolem .</span><span class="sxs-lookup"><span data-stu-id="45ad5-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="45ad5-643">Właściwość `Title` jest ustawiona na tytuł strony Informacje:</span><span class="sxs-lookup"><span data-stu-id="45ad5-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="45ad5-644">Na stronie Informacje przejdź `Title` do właściwości jako właściwości modelu:</span><span class="sxs-lookup"><span data-stu-id="45ad5-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="45ad5-645">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="45ad5-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="45ad5-646">TempData ( TempData )</span><span class="sxs-lookup"><span data-stu-id="45ad5-646">TempData</span></span>

<span data-ttu-id="45ad5-647">ASP.NET Core udostępnia [właściwość TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="45ad5-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="45ad5-648">Ta właściwość przechowuje dane, dopóki nie jest odczytywany.</span><span class="sxs-lookup"><span data-stu-id="45ad5-648">This property stores data until it's read.</span></span> <span data-ttu-id="45ad5-649">I `Keep` `Peek` metody mogą służyć do badania danych bez usuwania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="45ad5-650">`TempData`jest przydatne do przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.</span><span class="sxs-lookup"><span data-stu-id="45ad5-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="45ad5-651">Poniższy kod ustawia `Message` wartość `TempData`użycia:</span><span class="sxs-lookup"><span data-stu-id="45ad5-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="45ad5-652">Następujące znaczniki w pliku *Pages/Customers/Index.cshtml* wyświetlają wartość `Message` użycia `TempData`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="45ad5-653">Model strony *Pages/Customers/Index.cshtml.cs* stosuje `[TempData]` atrybut do `Message` właściwości.</span><span class="sxs-lookup"><span data-stu-id="45ad5-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="45ad5-654">Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="45ad5-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="45ad5-655">Wiele programów obsługi na stronie</span><span class="sxs-lookup"><span data-stu-id="45ad5-655">Multiple handlers per page</span></span>

<span data-ttu-id="45ad5-656">Następująca strona generuje znaczniki dla dwóch `asp-page-handler` programów obsługi przy użyciu Pomocnika znaczników:</span><span class="sxs-lookup"><span data-stu-id="45ad5-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="45ad5-657">Formularz w poprzednim przykładzie ma dwa przyciski `FormActionTagHelper` przesyłania, z których każdy używa do przesyłania do innego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="45ad5-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="45ad5-658">Atrybut `asp-page-handler` jest towarzyszem `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="45ad5-659">`asp-page-handler`generuje adresy URL, które przesyłają do każdej z metod obsługi zdefiniowanych przez stronę.</span><span class="sxs-lookup"><span data-stu-id="45ad5-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="45ad5-660">`asp-page`nie jest określony, ponieważ próbka łączy się z bieżącą stroną.</span><span class="sxs-lookup"><span data-stu-id="45ad5-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="45ad5-661">Model strony:</span><span class="sxs-lookup"><span data-stu-id="45ad5-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="45ad5-662">Poprzedni kod używa *nazwanych metod obsługi*.</span><span class="sxs-lookup"><span data-stu-id="45ad5-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="45ad5-663">Nazwane metody obsługi są tworzone przez `On<HTTP Verb>` biorąc `Async` tekst w nazwie po i przed (jeśli jest obecny).</span><span class="sxs-lookup"><span data-stu-id="45ad5-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="45ad5-664">W poprzednim przykładzie metody strony są OnPost**JoinList**Async i OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="45ad5-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="45ad5-665">Po usunięciu *OnPost* i *Async* `JoinList` nazwy `JoinListUC`obsługi są i .</span><span class="sxs-lookup"><span data-stu-id="45ad5-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="45ad5-666">Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="45ad5-667">Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`którą się przesyła, to .</span><span class="sxs-lookup"><span data-stu-id="45ad5-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="45ad5-668">Trasy niestandardowe</span><span class="sxs-lookup"><span data-stu-id="45ad5-668">Custom routes</span></span>

<span data-ttu-id="45ad5-669">Za `@page` pomocą dyrektywy można:</span><span class="sxs-lookup"><span data-stu-id="45ad5-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="45ad5-670">Określ niestandardową trasę do strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-670">Specify a custom route to a page.</span></span> <span data-ttu-id="45ad5-671">Na przykład trasę do strony Informacje można `/Some/Other/Path` `@page "/Some/Other/Path"`ustawić za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="45ad5-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="45ad5-672">Dołączanie segmentów do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-672">Append segments to a page's default route.</span></span> <span data-ttu-id="45ad5-673">Na przykład segment "element" można dodać do domyślnej `@page "item"`trasy strony za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="45ad5-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="45ad5-674">Dołącz parametry do domyślnej trasy strony.</span><span class="sxs-lookup"><span data-stu-id="45ad5-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="45ad5-675">Na przykład parametr ID, `id`może być wymagany dla `@page "{id}"`strony z .</span><span class="sxs-lookup"><span data-stu-id="45ad5-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="45ad5-676">Ścieżka względna katalogu głównego wyznaczona`~`przez tyldę ( ) na początku ścieżki jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="45ad5-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="45ad5-677">Na przykład `@page "~/Some/Other/Path"` jest taka `@page "/Some/Other/Path"`sama jak .</span><span class="sxs-lookup"><span data-stu-id="45ad5-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="45ad5-678">Jeśli nie podoba Ci się `?handler=JoinList` ciąg zapytania w adresie URL, zmień trasę, aby umieścić nazwę programu obsługi w części ścieżki adresu URL.</span><span class="sxs-lookup"><span data-stu-id="45ad5-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="45ad5-679">Trasę można dostosować, dodając szablon trasy ujęty w `@page` cudzysłowie po dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="45ad5-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="45ad5-680">Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="45ad5-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="45ad5-681">Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH/JoinListUC`którą się przesyła, to .</span><span class="sxs-lookup"><span data-stu-id="45ad5-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="45ad5-682">`?` Poniżej `handler` oznacza, że parametr trasy jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="45ad5-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="45ad5-683">Konfiguracja i ustawienia</span><span class="sxs-lookup"><span data-stu-id="45ad5-683">Configuration and settings</span></span>

<span data-ttu-id="45ad5-684">Aby skonfigurować opcje zaawansowane, `AddRazorPagesOptions` użyj metody rozszerzenia w konstruktorze MVC:</span><span class="sxs-lookup"><span data-stu-id="45ad5-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="45ad5-685">Obecnie można użyć `RazorPagesOptions` do ustawienia katalogu głównego dla stron lub dodać konwencje modelu aplikacji dla stron.</span><span class="sxs-lookup"><span data-stu-id="45ad5-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="45ad5-686">W ten sposób w przyszłości umożliwimy większą rozszerzalność.</span><span class="sxs-lookup"><span data-stu-id="45ad5-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="45ad5-687">Aby wstępnie skompilować widoki, zobacz [Kompilacja widoku Razor](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="45ad5-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="45ad5-688">[Pobierz lub wyświetl przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="45ad5-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="45ad5-689">Zobacz [Wprowadzenie do strony Razor ,](xref:tutorials/razor-pages/razor-pages-start)która opiera się na tym wprowadzeniu.</span><span class="sxs-lookup"><span data-stu-id="45ad5-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="45ad5-690">Określ, że strony Razor znajdują się w katalogu głównym zawartości</span><span class="sxs-lookup"><span data-stu-id="45ad5-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="45ad5-691">Domyślnie strony Razor są zakorzenione w katalogu */Pages.*</span><span class="sxs-lookup"><span data-stu-id="45ad5-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="45ad5-692">Dodaj [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc,](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) aby określić, że strony Razor znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) [(ContentRootPath)](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)aplikacji:</span><span class="sxs-lookup"><span data-stu-id="45ad5-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="45ad5-693">Określanie, że strony Razor znajdują się w niestandardowym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="45ad5-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="45ad5-694">Dodaj [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc,](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) aby określić, że strony Razor znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):</span><span class="sxs-lookup"><span data-stu-id="45ad5-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="45ad5-695">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="45ad5-695">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
