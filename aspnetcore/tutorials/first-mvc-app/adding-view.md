---
title: Część 3 Dodawanie widoku do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 3 serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804613"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="845e5-103">Część 3 Dodawanie widoku do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="845e5-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="845e5-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="845e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="845e5-105">W tej sekcji zmodyfikujesz klasę, `HelloWorldController` Aby używać [Razor](xref:mvc/views/razor) plików do wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="845e5-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="845e5-106">To czyści proces generowania odpowiedzi HTML na klienta.</span><span class="sxs-lookup"><span data-stu-id="845e5-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="845e5-107">Szablony widoków są tworzone przy użyciu Razor .</span><span class="sxs-lookup"><span data-stu-id="845e5-107">View templates are created using Razor.</span></span> <span data-ttu-id="845e5-108">RazorSzablony widoków oparte na programie:</span><span class="sxs-lookup"><span data-stu-id="845e5-108">Razor-based view templates:</span></span>

* <span data-ttu-id="845e5-109">Mieć rozszerzenie *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="845e5-110">Zapewnij elegancki sposób tworzenia danych wyjściowych HTML przy użyciu języka C#.</span><span class="sxs-lookup"><span data-stu-id="845e5-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="845e5-111">Obecnie `Index` Metoda zwraca ciąg z komunikatem w klasie Controller.</span><span class="sxs-lookup"><span data-stu-id="845e5-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="845e5-112">W `HelloWorldController` klasie Zastąp `Index` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="845e5-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="845e5-113">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="845e5-113">The preceding code:</span></span>

* <span data-ttu-id="845e5-114">Wywołuje <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="845e5-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="845e5-115">Używa szablonu widoku do wygenerowania odpowiedzi HTML.</span><span class="sxs-lookup"><span data-stu-id="845e5-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="845e5-116">Metody kontrolera:</span><span class="sxs-lookup"><span data-stu-id="845e5-116">Controller methods:</span></span>

* <span data-ttu-id="845e5-117">Są nazywane *metodami akcji*.</span><span class="sxs-lookup"><span data-stu-id="845e5-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="845e5-118">Na przykład `Index` Metoda akcji w poprzednim kodzie.</span><span class="sxs-lookup"><span data-stu-id="845e5-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="845e5-119">Zwykle zwracają <xref:Microsoft.AspNetCore.Mvc.IActionResult> klasę lub klasy pochodnej, a <xref:Microsoft.AspNetCore.Mvc.ActionResult> nie typu, takiego jak `string` .</span><span class="sxs-lookup"><span data-stu-id="845e5-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="845e5-120">Dodawanie widoku</span><span class="sxs-lookup"><span data-stu-id="845e5-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="845e5-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="845e5-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="845e5-122">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="845e5-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="845e5-123">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="845e5-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="845e5-124">W oknie dialogowym **Dodaj nowy element — MvcMovie** :</span><span class="sxs-lookup"><span data-stu-id="845e5-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="845e5-125">W polu wyszukiwania w prawym górnym rogu wprowadź *Widok*</span><span class="sxs-lookup"><span data-stu-id="845e5-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="845e5-126">Wybierz **Razor Widok**</span><span class="sxs-lookup"><span data-stu-id="845e5-126">Select **Razor View**</span></span>
* <span data-ttu-id="845e5-127">Zachowaj wartość pola **Nazwa** , *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="845e5-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="845e5-128">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="845e5-128">Select **Add**</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="845e5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="845e5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="845e5-131">Dodaj `Index` widok dla `HelloWorldController` :</span><span class="sxs-lookup"><span data-stu-id="845e5-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="845e5-132">Dodaj nowy folder o nazwie *viewss/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="845e5-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="845e5-133">Dodaj nowy plik do pliku *viewss/HelloWorld* Name *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="845e5-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="845e5-134">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="845e5-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="845e5-135">Kliknij przycisk sterowania w folderze *widoki* , a następnie **Dodaj > nowy folder** i Nazwij folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="845e5-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="845e5-136">Kliknij przycisk sterowania w folderze *widoki/HelloWorld* , a następnie **Dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="845e5-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="845e5-137">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="845e5-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="845e5-138">W lewym okienku wybierz pozycję **ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="845e5-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="845e5-139">W środkowym okienku wybierz pozycję **Razor Widok** .</span><span class="sxs-lookup"><span data-stu-id="845e5-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="845e5-140">Wpisz *indeks* w polu **Nazwa** .</span><span class="sxs-lookup"><span data-stu-id="845e5-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="845e5-141">Wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="845e5-141">Select **New**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="845e5-143">Zastąp zawartość pliku widoku *widoki/HelloWorld/index. cshtml* Razor następującym:</span><span class="sxs-lookup"><span data-stu-id="845e5-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="845e5-144">Przejdź do `https://localhost:{PORT}/HelloWorld` :</span><span class="sxs-lookup"><span data-stu-id="845e5-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="845e5-145">`Index`Metoda w wykonaniu `HelloWorldController` instrukcji `return View();` , która określa, że metoda powinna używać pliku szablonu widoku, aby renderować odpowiedź do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="845e5-146">Nazwa pliku szablonu widoku nie została określona, więc MVC domyślnie używa domyślnego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="845e5-147">Jeśli nazwa pliku widoku nie jest określona, zwracany jest widok domyślny.</span><span class="sxs-lookup"><span data-stu-id="845e5-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="845e5-148">Widok domyślny ma taką samą nazwę jak Metoda akcji, `Index` w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="845e5-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="845e5-149">Używany jest */views/HelloWorld/index.cshtml* szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="845e5-150">Na poniższej ilustracji przedstawiono ciąg "Hello z naszego szablonu widoku!"</span><span class="sxs-lookup"><span data-stu-id="845e5-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="845e5-151">zakodowane w widoku:</span><span class="sxs-lookup"><span data-stu-id="845e5-151">hard-coded in the view:</span></span>

  ![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="845e5-153">Zmień widoki i strony układu</span><span class="sxs-lookup"><span data-stu-id="845e5-153">Change views and layout pages</span></span>

<span data-ttu-id="845e5-154">Wybierz menu linki **MvcMovie**, **Home** i **privacy**.</span><span class="sxs-lookup"><span data-stu-id="845e5-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="845e5-155">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="845e5-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="845e5-156">Układ menu jest implementowany w pliku *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="845e5-157">Otwórz plik *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="845e5-158">Szablony [układów](xref:mvc/views/layout) umożliwiają:</span><span class="sxs-lookup"><span data-stu-id="845e5-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="845e5-159">Określanie układu kontenera HTML lokacji w jednym miejscu.</span><span class="sxs-lookup"><span data-stu-id="845e5-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="845e5-160">Zastosowanie układu kontenera HTML na wielu stronach w lokacji.</span><span class="sxs-lookup"><span data-stu-id="845e5-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="845e5-161">Znajdź `@RenderBody()` wiersz.</span><span class="sxs-lookup"><span data-stu-id="845e5-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="845e5-162">`RenderBody` jest symbolem zastępczym, w którym wszystkie utworzone strony specyficzne dla widoku są *widoczne na stronie* układ.</span><span class="sxs-lookup"><span data-stu-id="845e5-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="845e5-163">Na przykład po wybraniu linku **prywatności** widok **widoki/główna/prywatność. cshtml** jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="845e5-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="845e5-164">Zmień tytuł, stopkę i łącze menu w pliku układu</span><span class="sxs-lookup"><span data-stu-id="845e5-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="845e5-165">Zastąp zawartość pliku *viewss/Shared/_Layout. cshtml* następującym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="845e5-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="845e5-166">Zmiany są wyróżnione:</span><span class="sxs-lookup"><span data-stu-id="845e5-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="845e5-167">Poprzednia Adiustacja wprowadziła następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="845e5-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="845e5-168">Trzy wystąpienia elementu `MvcMovie` do `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="845e5-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="845e5-169">Element zakotwiczony `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` do `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .</span><span class="sxs-lookup"><span data-stu-id="845e5-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="845e5-170">W powyższym znaczniku `asp-area=""` [atrybut pomocnika znacznika zakotwiczenia](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) i wartość atrybutu zostały pominięte, ponieważ ta aplikacja nie korzysta z [obszarów](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="845e5-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="845e5-171">**Uwaga**: `Movies` kontroler nie został zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="845e5-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="845e5-172">W tym momencie `Movie App` łącze nie działa.</span><span class="sxs-lookup"><span data-stu-id="845e5-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="845e5-173">Zapisz zmiany i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="845e5-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="845e5-174">Zwróć uwagę, jak tytuł na karcie Przeglądarka wyświetla **zasady zachowania poufności informacji — aplikacja dla filmów** zamiast **zasad ochrony prywatności — film MVC**:</span><span class="sxs-lookup"><span data-stu-id="845e5-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta prywatność](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="845e5-176">Wybierz link **Home** (Strona główna).</span><span class="sxs-lookup"><span data-stu-id="845e5-176">Select the **Home** link.</span></span>

<span data-ttu-id="845e5-177">Zwróć uwagę, że tytuł i tekst zakotwiczenia wyświetlają **aplikację filmu**.</span><span class="sxs-lookup"><span data-stu-id="845e5-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="845e5-178">Zmiany zostały wprowadzone raz w szablonie układu i wszystkie strony w witrynie odzwierciedlają nowy tekst linku i nowy tytuł.</span><span class="sxs-lookup"><span data-stu-id="845e5-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="845e5-179">Przejrzyj plik *views/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="845e5-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="845e5-180">Plik *views/_ViewStart. cshtml* umieszcza w pliku *views/Shared/_Layout. cshtml* w każdym widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="845e5-181">`Layout`Właściwość może służyć do ustawiania innego widoku układu lub ustawiania tego ustawienia tak, aby `null` nie był używany żaden plik układu.</span><span class="sxs-lookup"><span data-stu-id="845e5-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="845e5-182">Otwórz plik widoku *views/HelloWorld/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="845e5-183">Zmień tytuł i `<h2>` element jako wyróżniony w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="845e5-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="845e5-184">Tytuł i `<h2>` element są nieco inne, więc jest jasne, która część kodu zmienia ekran.</span><span class="sxs-lookup"><span data-stu-id="845e5-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="845e5-185">`ViewData["Title"] = "Movie List";` w powyższym kodzie ustawia `Title` Właściwość `ViewData` słownika na "Lista filmów".</span><span class="sxs-lookup"><span data-stu-id="845e5-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="845e5-186">`Title`Właściwość jest używana w `<title>` elemencie HTML na stronie układu:</span><span class="sxs-lookup"><span data-stu-id="845e5-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="845e5-187">Zapisz zmiany i przejdź do `https://localhost:{PORT}/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="845e5-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="845e5-188">Należy zauważyć, że następujące zmiany zostały zmienione:</span><span class="sxs-lookup"><span data-stu-id="845e5-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="845e5-189">Tytuł przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-189">Browser title.</span></span>
* <span data-ttu-id="845e5-190">Nagłówek podstawowy.</span><span class="sxs-lookup"><span data-stu-id="845e5-190">Primary heading.</span></span>
* <span data-ttu-id="845e5-191">Nagłówki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="845e5-191">Secondary headings.</span></span>

<span data-ttu-id="845e5-192">Jeśli w przeglądarce nie wprowadzono żadnych zmian, może to oznaczać, że zawartość jest buforowana.</span><span class="sxs-lookup"><span data-stu-id="845e5-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="845e5-193">Naciśnij kombinację klawiszy CTRL + F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera.</span><span class="sxs-lookup"><span data-stu-id="845e5-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="845e5-194">Zostanie utworzony tytuł przeglądarki z `ViewData["Title"]` ustawioną w szablonie *index. cshtml* , a dodatkowa "-Movie App" dodana w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="845e5-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="845e5-195">Zawartość szablonu widoku *index. cshtml* jest scalana z szablonem widok *widoki/udostępnione/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="845e5-196">Pojedyncza odpowiedź HTML jest wysyłana do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="845e5-197">Szablony układów ułatwiają wprowadzanie zmian, które są stosowane na wszystkich stronach w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="845e5-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="845e5-198">Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="845e5-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="845e5-200">Mała wersja "Data" — "Hello z naszego szablonu widoku!"</span><span class="sxs-lookup"><span data-stu-id="845e5-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="845e5-201">wiadomość jest jednak sztywno zakodowana.</span><span class="sxs-lookup"><span data-stu-id="845e5-201">message, is hard-coded however.</span></span> <span data-ttu-id="845e5-202">Aplikacja MVC ma "V" (widok), "C" (kontroler), ale nie "M" (model) jeszcze.</span><span class="sxs-lookup"><span data-stu-id="845e5-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="845e5-203">Przekazywanie danych z kontrolera do widoku</span><span class="sxs-lookup"><span data-stu-id="845e5-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="845e5-204">Akcje kontrolera są wywoływane w odpowiedzi na żądanie przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="845e5-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="845e5-205">Klasa kontrolera to miejsce, w którym zapisano kod obsługujący żądania przeglądarki przychodzącej.</span><span class="sxs-lookup"><span data-stu-id="845e5-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="845e5-206">Kontroler pobiera dane ze źródła danych i decyduje o typie odpowiedzi wysyłanej z powrotem do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="845e5-207">Szablony widoków mogą być używane z poziomu kontrolera do generowania i formatowania odpowiedzi HTML w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="845e5-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="845e5-208">Kontrolery są odpowiedzialne za dostarczanie danych wymaganych w celu renderowania odpowiedzi przez szablon widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="845e5-209">Szablony widoków **nie** powinny:</span><span class="sxs-lookup"><span data-stu-id="845e5-209">View templates should **not**:</span></span>

* <span data-ttu-id="845e5-210">Wykonaj logikę biznesową</span><span class="sxs-lookup"><span data-stu-id="845e5-210">Do business logic</span></span>
* <span data-ttu-id="845e5-211">Bezpośrednie współpracujące z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="845e5-211">Interact with a database directly.</span></span>

<span data-ttu-id="845e5-212">Szablon widoku powinien współpracować tylko z danymi, które są do niego udostępniane przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="845e5-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="845e5-213">Utrzymywanie tego "oddzielenia obaw" pomaga zachować kod:</span><span class="sxs-lookup"><span data-stu-id="845e5-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="845e5-214">Czyst.</span><span class="sxs-lookup"><span data-stu-id="845e5-214">Clean.</span></span>
* <span data-ttu-id="845e5-215">Weryfikowalne.</span><span class="sxs-lookup"><span data-stu-id="845e5-215">Testable.</span></span>
* <span data-ttu-id="845e5-216">Utrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="845e5-216">Maintainable.</span></span>

<span data-ttu-id="845e5-217">Obecnie `Welcome` Metoda w `HelloWorldController` klasie przyjmuje `name` parametr i, `ID` a następnie wyprowadza wartości bezpośrednio do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="845e5-218">Zamiast przetworzyć tę odpowiedź jako ciąg, należy zmienić kontroler tak, aby używał szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="845e5-219">Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie dane muszą zostać przesłane z kontrolera do widoku w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="845e5-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="845e5-220">W tym celu należy mieć kontroler umieszczający dane dynamiczne (parametry), których szablon widoku ma w `ViewData` słowniku.</span><span class="sxs-lookup"><span data-stu-id="845e5-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="845e5-221">Szablon widoku może następnie uzyskiwać dostęp do danych dynamicznych.</span><span class="sxs-lookup"><span data-stu-id="845e5-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="845e5-222">W *HelloWorldController.cs* Zmień metodę, `Welcome` Aby dodać `Message` `NumTimes` wartość i do `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="845e5-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="845e5-223">`ViewData`Słownik jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu.</span><span class="sxs-lookup"><span data-stu-id="845e5-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="845e5-224">`ViewData`Obiekt nie ma zdefiniowanych właściwości do momentu dodania elementu.</span><span class="sxs-lookup"><span data-stu-id="845e5-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="845e5-225">[System powiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje nazwane parametry `name` i `numTimes` z ciągu zapytania na parametry w metodzie.</span><span class="sxs-lookup"><span data-stu-id="845e5-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="845e5-226">Zakończenie `HelloWorldController` :</span><span class="sxs-lookup"><span data-stu-id="845e5-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="845e5-227">`ViewData`Obiekt dictionary zawiera dane, które zostaną przesłane do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="845e5-228">Utwórz szablon widoku powitalnego o nazwie *przeglądający/HelloWorld/Welcome. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="845e5-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="845e5-229">Utworzysz pętlę w szablonie widoku *Welcome. cshtml* , który wyświetla "Hello" `NumTimes` .</span><span class="sxs-lookup"><span data-stu-id="845e5-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="845e5-230">Zastąp zawartość *widoków/HelloWorld/Welcome. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="845e5-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="845e5-231">Zapisz zmiany i przejdź do następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="845e5-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="845e5-232">Dane są pobierane z adresu URL i przesyłane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="845e5-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="845e5-233">Kontroler umieszcza dane w `ViewData` słowniku i przekazuje ten obiekt do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="845e5-234">Widok następnie renderuje dane jako HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-234">The view then renders the data as HTML to the browser.</span></span>

![Widok prywatności pokazujący etykietę powitalną i frazę Hello Rick pokazywane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="845e5-236">W poprzednim przykładzie `ViewData` słownik został użyty do przekazania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="845e5-237">W dalszej części tego samouczka model widoku służy do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="845e5-238">Podejście model widoku do przekazywania danych jest preferowane przez `ViewData` podejście słownika.</span><span class="sxs-lookup"><span data-stu-id="845e5-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="845e5-239">W następnym samouczku zostanie utworzona baza danych filmów.</span><span class="sxs-lookup"><span data-stu-id="845e5-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="845e5-240">[Poprzedni: dodawanie kontrolera](adding-controller.md) 
>  [Dalej: Dodawanie modelu](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="845e5-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="845e5-241">W tej sekcji `HelloWorldController` Klasa została zmodyfikowana tak, aby używać [Razor](xref:mvc/views/razor) plików do wyświetlania w celu czystego hermetyzowania procesu generowania odpowiedzi HTML na klienta.</span><span class="sxs-lookup"><span data-stu-id="845e5-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="845e5-242">Tworzysz plik szablonu widoku przy użyciu Razor .</span><span class="sxs-lookup"><span data-stu-id="845e5-242">You create a view template file using Razor.</span></span> <span data-ttu-id="845e5-243">RazorSzablony widoków oparte na programie mają rozszerzenie *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="845e5-244">Zapewniają elegancki sposób tworzenia danych wyjściowych HTML przy użyciu języka C#.</span><span class="sxs-lookup"><span data-stu-id="845e5-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="845e5-245">Obecnie `Index` Metoda zwraca ciąg z komunikatem, który jest zakodowany w klasie Controller.</span><span class="sxs-lookup"><span data-stu-id="845e5-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="845e5-246">W `HelloWorldController` klasie Zastąp `Index` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="845e5-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="845e5-247">Poprzedni kod wywołuje <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodę kontrolera.</span><span class="sxs-lookup"><span data-stu-id="845e5-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="845e5-248">Używa szablonu widoku do wygenerowania odpowiedzi HTML.</span><span class="sxs-lookup"><span data-stu-id="845e5-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="845e5-249">Metody kontrolera (znane także jako *metody akcji*), takie jak `Index` powyższa metoda, zazwyczaj zwracają <xref:Microsoft.AspNetCore.Mvc.IActionResult> (lub klasę pochodną <xref:Microsoft.AspNetCore.Mvc.ActionResult> ), a nie typ taki jak `string` .</span><span class="sxs-lookup"><span data-stu-id="845e5-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="845e5-250">Dodawanie widoku</span><span class="sxs-lookup"><span data-stu-id="845e5-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="845e5-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="845e5-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="845e5-252">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="845e5-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="845e5-253">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="845e5-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="845e5-254">W oknie dialogowym **Dodaj nowy element — MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="845e5-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="845e5-255">W polu wyszukiwania w prawym górnym rogu wprowadź *Widok*</span><span class="sxs-lookup"><span data-stu-id="845e5-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="845e5-256">Wybierz **Razor Widok**</span><span class="sxs-lookup"><span data-stu-id="845e5-256">Select **Razor View**</span></span>

  * <span data-ttu-id="845e5-257">Zachowaj wartość pola **Nazwa** , *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="845e5-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="845e5-258">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="845e5-258">Select **Add**</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="845e5-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="845e5-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="845e5-261">Dodaj `Index` widok dla `HelloWorldController` .</span><span class="sxs-lookup"><span data-stu-id="845e5-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="845e5-262">Dodaj nowy folder o nazwie *viewss/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="845e5-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="845e5-263">Dodaj nowy plik do pliku *viewss/HelloWorld* Name *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="845e5-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="845e5-264">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="845e5-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="845e5-265">Kliknij prawym przyciskiem myszy folder *widoki* , a następnie **Dodaj > nowy folder** i nadaj mu nazwę folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="845e5-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="845e5-266">Kliknij prawym przyciskiem myszy folder *widoki/HelloWorld* , a następnie **Dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="845e5-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="845e5-267">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="845e5-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="845e5-268">W lewym okienku wybierz pozycję **Sieć Web** .</span><span class="sxs-lookup"><span data-stu-id="845e5-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="845e5-269">W środkowym okienku wybierz pozycję **pusty plik HTML** .</span><span class="sxs-lookup"><span data-stu-id="845e5-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="845e5-270">Wpisz *index. cshtml* w polu **Nazwa** .</span><span class="sxs-lookup"><span data-stu-id="845e5-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="845e5-271">Wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="845e5-271">Select **New**.</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="845e5-273">Zastąp zawartość pliku widoku *widoki/HelloWorld/index. cshtml* Razor następującym:</span><span class="sxs-lookup"><span data-stu-id="845e5-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="845e5-274">Przejdź do adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="845e5-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="845e5-275">Metoda w niewykonanym `Index` `HelloWorldController` stopniu; uruchomiła instrukcję `return View();` , która określa, że metoda powinna używać pliku szablonu widoku, aby renderować odpowiedź do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="845e5-276">Ponieważ nazwa pliku szablonu widoku nie została określona, MVC domyślnie używa domyślnego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="845e5-277">Domyślny plik widoku ma taką samą nazwę jak Metoda ( `Index` ), więc w */views/HelloWorld/index.cshtml* jest używana.</span><span class="sxs-lookup"><span data-stu-id="845e5-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="845e5-278">Na poniższej ilustracji przedstawiono ciąg "Hello z naszego szablonu widoku!"</span><span class="sxs-lookup"><span data-stu-id="845e5-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="845e5-279">zakodowane w widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-279">hard-coded in the view.</span></span>

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="845e5-281">Zmień widoki i strony układu</span><span class="sxs-lookup"><span data-stu-id="845e5-281">Change views and layout pages</span></span>

<span data-ttu-id="845e5-282">Wybierz linki menu (**MvcMovie**, **Home** i **privacy**).</span><span class="sxs-lookup"><span data-stu-id="845e5-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="845e5-283">Każda Strona wyświetla ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="845e5-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="845e5-284">Układ menu jest implementowany w pliku *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="845e5-285">Otwórz plik *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="845e5-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="845e5-286">Szablony [układów](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="845e5-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="845e5-287">Znajdź `@RenderBody()` wiersz.</span><span class="sxs-lookup"><span data-stu-id="845e5-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="845e5-288">`RenderBody` jest symbolem zastępczym, w którym wszystkie utworzone strony specyficzne dla widoku są *widoczne na stronie* układ.</span><span class="sxs-lookup"><span data-stu-id="845e5-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="845e5-289">Na przykład po wybraniu linku **prywatności** widok **widoki/główna/prywatność. cshtml** jest renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="845e5-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="845e5-290">Zmień tytuł, stopkę i łącze menu w pliku układu</span><span class="sxs-lookup"><span data-stu-id="845e5-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="845e5-291">W elementach tytuł i stopka Zmień `MvcMovie` na `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="845e5-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="845e5-292">Zmień element zakotwiczenia `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .</span><span class="sxs-lookup"><span data-stu-id="845e5-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="845e5-293">Następujące znaczniki pokazują wyróżnione zmiany:</span><span class="sxs-lookup"><span data-stu-id="845e5-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="845e5-294">W powyższym znaczniku `asp-area` [atrybut pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) został pominięty, ponieważ ta aplikacja nie korzysta z [obszarów](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="845e5-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="845e5-295">**Uwaga**: `Movies` kontroler nie został zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="845e5-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="845e5-296">W tym momencie `Movie App` łącze nie działa.</span><span class="sxs-lookup"><span data-stu-id="845e5-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="845e5-297">Zapisz zmiany i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="845e5-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="845e5-298">Zwróć uwagę, jak tytuł na karcie Przeglądarka wyświetla **zasady zachowania poufności informacji — aplikacja dla filmów** zamiast **zasad ochrony prywatności — film MVC**:</span><span class="sxs-lookup"><span data-stu-id="845e5-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta prywatność](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="845e5-300">Wybierz link **domowy** i zwróć uwagę na to, że tytuł i tekst zakotwiczenia również wyświetlają **aplikację Movie**.</span><span class="sxs-lookup"><span data-stu-id="845e5-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="845e5-301">Udało nam się wprowadzić zmiany w szablonie układu i wszystkie strony w witrynie będą odzwierciedlały nowy tekst linku i nowy tytuł.</span><span class="sxs-lookup"><span data-stu-id="845e5-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="845e5-302">Przejrzyj plik *views/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="845e5-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="845e5-303">Plik *views/_ViewStart. cshtml* umieszcza w pliku *views/Shared/_Layout. cshtml* w każdym widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="845e5-304">`Layout`Właściwość może służyć do ustawiania innego widoku układu lub ustawiania tego ustawienia tak, aby `null` nie był używany żaden plik układu.</span><span class="sxs-lookup"><span data-stu-id="845e5-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="845e5-305">Zmień tytuł i `<h2>` element pliku widoku *widoki/HelloWorld/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="845e5-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="845e5-306">Tytuł i `<h2>` element są nieco inne, więc można zobaczyć, który bit kodu zmienia ekran.</span><span class="sxs-lookup"><span data-stu-id="845e5-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="845e5-307">`ViewData["Title"] = "Movie List";` w powyższym kodzie ustawia `Title` Właściwość `ViewData` słownika na "Lista filmów".</span><span class="sxs-lookup"><span data-stu-id="845e5-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="845e5-308">`Title`Właściwość jest używana w `<title>` elemencie HTML na stronie układu:</span><span class="sxs-lookup"><span data-stu-id="845e5-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="845e5-309">Zapisz zmiany i przejdź do `https://localhost:{PORT}/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="845e5-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="845e5-310">Zwróć uwagę, że tytuł przeglądarki, nagłówek podstawowy i pomocnicze nagłówki zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="845e5-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="845e5-311">(Jeśli w przeglądarce nie są widoczne zmiany, może być wyświetlana zawartość z pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="845e5-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="845e5-312">Naciśnij kombinację klawiszy CTRL + F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera. Zostanie utworzony tytuł przeglądarki z `ViewData["Title"]` ustawioną w szablonie *index. cshtml* , a dodatkowa "-Movie App" dodana w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="845e5-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="845e5-313">Zwróć uwagę na to, jak zawartość w szablonie widoku *index. cshtml* została scalona z szablonem *widoków/Shared/_Layout. cshtml* . do przeglądarki została WYSŁANA pojedyncza odpowiedź html.</span><span class="sxs-lookup"><span data-stu-id="845e5-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="845e5-314">Szablony układów ułatwiają wprowadzanie zmian, które są stosowane do wszystkich stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="845e5-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="845e5-315">Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="845e5-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="845e5-317">Nasz mały bit "Data" (w tym przypadku "Hello z naszego szablonu widoku!")</span><span class="sxs-lookup"><span data-stu-id="845e5-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="845e5-318">komunikat) jest zakodowany na stałe.</span><span class="sxs-lookup"><span data-stu-id="845e5-318">message) is hard-coded, though.</span></span> <span data-ttu-id="845e5-319">Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie jest jeszcze "M" (model).</span><span class="sxs-lookup"><span data-stu-id="845e5-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="845e5-320">Przekazywanie danych z kontrolera do widoku</span><span class="sxs-lookup"><span data-stu-id="845e5-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="845e5-321">Akcje kontrolera są wywoływane w odpowiedzi na żądanie przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="845e5-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="845e5-322">Klasa kontrolera to miejsce, w którym zapisano kod obsługujący żądania przeglądarki przychodzącej.</span><span class="sxs-lookup"><span data-stu-id="845e5-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="845e5-323">Kontroler pobiera dane ze źródła danych i decyduje o typie odpowiedzi wysyłanej z powrotem do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="845e5-324">Szablony widoków mogą być używane z poziomu kontrolera do generowania i formatowania odpowiedzi HTML w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="845e5-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="845e5-325">Kontrolery są odpowiedzialne za dostarczanie danych wymaganych w celu renderowania odpowiedzi przez szablon widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="845e5-326">Najlepsze rozwiązanie: szablony widoków **nie** powinny wykonywać logiki biznesowej ani bezpośrednio korzystać z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="845e5-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="845e5-327">Zamiast tego szablon widoku powinien współpracować tylko z danymi, które są udostępniane przez kontroler.</span><span class="sxs-lookup"><span data-stu-id="845e5-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="845e5-328">Utrzymywanie tego "separacji zagadnień" pomaga zachować kod, weryfikowalne i łatwość utrzymania.</span><span class="sxs-lookup"><span data-stu-id="845e5-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="845e5-329">Obecnie `Welcome` Metoda w `HelloWorldController` klasie przyjmuje `name` parametr i, `ID` a następnie wyprowadza wartości bezpośrednio do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="845e5-330">Zamiast przetworzyć tę odpowiedź jako ciąg, należy zmienić kontroler tak, aby używał szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="845e5-331">Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą zostać przesłane z kontrolera do widoku w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="845e5-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="845e5-332">W tym celu należy mieć kontroler umieszczający dane dynamiczne (parametry) wymagane przez szablon widoku w `ViewData` słowniku, do którego będzie miał dostęp ten szablon.</span><span class="sxs-lookup"><span data-stu-id="845e5-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="845e5-333">W *HelloWorldController.cs* Zmień metodę, `Welcome` Aby dodać `Message` `NumTimes` wartość i do `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="845e5-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="845e5-334">`ViewData`Słownik jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; `ViewData` obiekt nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim elementu.</span><span class="sxs-lookup"><span data-stu-id="845e5-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="845e5-335">[System powiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje nazwane parametry ( `name` i `numTimes` ) z ciągu zapytania na pasku adresu na parametry w metodzie.</span><span class="sxs-lookup"><span data-stu-id="845e5-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="845e5-336">Pełny plik *HelloWorldController.cs* wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="845e5-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="845e5-337">`ViewData`Obiekt dictionary zawiera dane, które zostaną przesłane do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="845e5-338">Utwórz szablon widoku powitalnego o nazwie *przeglądający/HelloWorld/Welcome. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="845e5-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="845e5-339">Utworzysz pętlę w szablonie widoku *Welcome. cshtml* , który wyświetla "Hello" `NumTimes` .</span><span class="sxs-lookup"><span data-stu-id="845e5-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="845e5-340">Zastąp zawartość *widoków/HelloWorld/Welcome. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="845e5-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="845e5-341">Zapisz zmiany i przejdź do następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="845e5-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="845e5-342">Dane są pobierane z adresu URL i przesyłane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="845e5-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="845e5-343">Kontroler umieszcza dane w `ViewData` słowniku i przekazuje ten obiekt do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="845e5-344">Widok następnie renderuje dane jako HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="845e5-344">The view then renders the data as HTML to the browser.</span></span>

![Widok prywatności pokazujący etykietę powitalną i frazę Hello Rick pokazywane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="845e5-346">W powyższym przykładzie `ViewData` słownik został użyty do przekazania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="845e5-347">W dalszej części tego samouczka model widoku służy do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="845e5-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="845e5-348">Podejście model widoku do przekazywania danych jest ogólnie preferowane względem `ViewData` podejścia słownika.</span><span class="sxs-lookup"><span data-stu-id="845e5-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="845e5-349">Aby uzyskać więcej informacji [, zobacz Kiedy używać ViewBag, ViewData lub TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="845e5-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="845e5-350">W następnym samouczku zostanie utworzona baza danych filmów.</span><span class="sxs-lookup"><span data-stu-id="845e5-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="845e5-351">[Poprzedni](adding-controller.md) 
>  [Dalej](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="845e5-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
