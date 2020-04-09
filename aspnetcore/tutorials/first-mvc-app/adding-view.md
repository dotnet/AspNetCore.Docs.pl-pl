---
title: Dodawanie widoku do aplikacji core mvc ASP.NET
author: rick-anderson
description: Dodawanie widoku do prostej aplikacji Core MVC ASP.NET
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660210"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="a0d5d-103">Dodawanie widoku do aplikacji core mvc ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a0d5d-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="a0d5d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0d5d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0d5d-105">W tej sekcji `HelloWorldController` można zmodyfikować klasy za pomocą [razor](xref:mvc/views/razor) plików widoku do czyszczenia hermetyzować proces generowania odpowiedzi HTML do klienta.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="a0d5d-106">Plik szablonu widoku można utworzyć za pomocą razor.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-106">You create a view template file using Razor.</span></span> <span data-ttu-id="a0d5d-107">Szablony widoku oparte na brzytwie mają rozszerzenie pliku *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="a0d5d-108">Zapewniają one elegancki sposób tworzenia danych wyjściowych HTML z języka C#.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="a0d5d-109">Obecnie `Index` metoda zwraca ciąg z komunikatem, który jest zakodowany na stałe w klasie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="a0d5d-110">W `HelloWorldController` klasie zastąp `Index` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="a0d5d-111">Poprzedni kod wywołuje metodę kontrolera. <xref:Microsoft.AspNetCore.Mvc.Controller.View*></span><span class="sxs-lookup"><span data-stu-id="a0d5d-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="a0d5d-112">Używa szablonu widoku do generowania odpowiedzi HTML.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="a0d5d-113">Metody kontrolera (znane również jako `Index` *metody akcji),* takie <xref:Microsoft.AspNetCore.Mvc.IActionResult> jak powyższa metoda, <xref:Microsoft.AspNetCore.Mvc.ActionResult>zazwyczaj zwracają `string`(lub klasę pochodną), a nie typ podobny.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="a0d5d-114">Dodawanie widoku</span><span class="sxs-lookup"><span data-stu-id="a0d5d-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0d5d-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0d5d-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0d5d-116">Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="a0d5d-117">Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="a0d5d-118">W oknie dialogowym **Dodaj nowy przedmiot — MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="a0d5d-119">W polu wyszukiwania w prawym górnym rogu wprowadź *widok*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="a0d5d-120">Wybierz **widok razor**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-120">Select **Razor View**</span></span>

  * <span data-ttu-id="a0d5d-121">Zachowaj wartość pola **Nazwa,** *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="a0d5d-122">Wybierz **pozycję Dodaj**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-122">Select **Add**</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0d5d-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0d5d-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0d5d-125">Dodaj `Index` widok dla `HelloWorldController`pliku .</span><span class="sxs-lookup"><span data-stu-id="a0d5d-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="a0d5d-126">Dodaj nowy folder o nazwie *Widoki/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="a0d5d-127">Dodaj nowy plik do nazwy folderu *Widoki/HelloWorld* *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0d5d-128">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a0d5d-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0d5d-129">Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="a0d5d-130">Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="a0d5d-131">W oknie dialogowym **Nowy plik:**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="a0d5d-132">Wybierz **pozycję ASP .NET Core** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="a0d5d-133">Wybierz **pozycję Strona widoku MVC** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="a0d5d-134">Wpisz *indeks* w polu **Nazwa.**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="a0d5d-135">Wybierz **pozycję Nowy**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-135">Select **New**.</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="a0d5d-137">Zastąp zawartość pliku widoku *Widoki/HelloWorld/Index.cshtml* Razor na następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="a0d5d-138">Przejdź do adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a0d5d-139">Metoda `Index` w `HelloWorldController` nie zrobił wiele; uruchomiono instrukcję `return View();`, która określiła, że metoda powinna używać pliku szablonu widoku do renderowania odpowiedzi na przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="a0d5d-140">Ponieważ nie określono nazwy pliku szablonu widoku, domyślnie MVC używał domyślnego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="a0d5d-141">Domyślny plik widoku ma taką samą nazwę jak metoda (`Index`), więc w */Views/HelloWorld/Index.cshtml* jest używany.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="a0d5d-142">Na poniższej ilustracji znajduje się napis "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="a0d5d-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="a0d5d-143">zakodowane w widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-143">hard-coded in the view.</span></span>

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="a0d5d-145">Zmienianie widoków i stron układu</span><span class="sxs-lookup"><span data-stu-id="a0d5d-145">Change views and layout pages</span></span>

<span data-ttu-id="a0d5d-146">Wybierz łącza menu **(MvcMovie**, **Home**i **Prywatność).**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="a0d5d-147">Każda strona zawiera ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="a0d5d-148">Układ menu jest zaimplementowany w pliku *Views/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a0d5d-149">Otwórz plik *Views/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a0d5d-150">[Szablony układu](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="a0d5d-151">Znajdź `@RenderBody()` linię.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a0d5d-152">`RenderBody`to symbol zastępczy, w którym wszystkie utworzone strony specyficzne dla widoku są wyświetlane, *zawinięte* na stronie układu.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a0d5d-153">Jeśli na przykład wybierzesz łącze **Prywatność,** widok **Widoki/Strona główna/Prywatność.cshtml** zostanie renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="a0d5d-154">Zmienianie tytułu, stopki i łącza menu w pliku układu</span><span class="sxs-lookup"><span data-stu-id="a0d5d-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="a0d5d-155">Zastąp zawartość pliku *Views/Shared/_Layout.cshtml* następującymi znacznikami.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="a0d5d-156">Zmiany są wyróżnione:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="a0d5d-157">Poprzedni znacznik dokonał następujących zmian:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="a0d5d-158">3 wystąpienia `MvcMovie` do `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="a0d5d-159">Element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` zakotwiczenia do `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="a0d5d-160">W poprzednim znacznikach pominięto atrybut i wartość atrybutu `asp-area=""` [Pomocnika znaczników zakotwiczenia,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ponieważ ta aplikacja nie używa [funkcji Obszary](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="a0d5d-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="a0d5d-161">**Uwaga:** `Movies` Kontroler nie został zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="a0d5d-162">W tym momencie łącze `Movie App` nie jest funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="a0d5d-163">Zapisz zmiany i wybierz link **Prywatność.**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="a0d5d-164">Zwróć uwagę, jak tytuł na karcie przeglądarki wyświetla **Politykę prywatności - Movie App** zamiast Polityki prywatności - **Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta Prywatność](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="a0d5d-166">Zaznacz łącze **Strona główna** i zwróć uwagę, że tytuł i tekst zakotwiczenia wyświetlają również aplikację **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="a0d5d-167">Byliśmy w stanie wprowadzić zmianę raz w szablonie układu i wszystkie strony w witrynie odzwierciedlają nowy tekst linku i nowy tytuł.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="a0d5d-168">Sprawdź plik *Views/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="a0d5d-169">Plik *Views/_ViewStart.cshtml* wprowadza do każdego widoku plik *Views/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="a0d5d-170">Właściwości `Layout` może służyć do ustawiania innego widoku układu `null` lub ustawić go na tak, aby nie plik układu będą używane.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="a0d5d-171">Zmień tytuł `<h2>` i element pliku widoku *Widoki/HelloWorld/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="a0d5d-172">Tytuł i `<h2>` element są nieco inne, dzięki czemu można zobaczyć, który bit kodu zmienia wyświetlanie.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="a0d5d-173">`ViewData["Title"] = "Movie List";`w powyższym kodzie `Title` ustawia `ViewData` właściwość słownika na "Lista filmów".</span><span class="sxs-lookup"><span data-stu-id="a0d5d-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="a0d5d-174">Właściwość `Title` jest używana `<title>` w elemencie HTML na stronie układu:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="a0d5d-175">Zapisz zmianę i `https://localhost:{PORT}/HelloWorld`przejdź do .</span><span class="sxs-lookup"><span data-stu-id="a0d5d-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a0d5d-176">Należy zauważyć, że zmienił się tytuł przeglądarki, nagłówek podstawowy i nagłówki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="a0d5d-177">(Jeśli nie widzisz zmian w przeglądarce, być może wyświetlasz zawartość w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="a0d5d-178">Naciśnij klawisze Ctrl+F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera). Tytuł przeglądarki jest `ViewData["Title"]` tworzony z zestawem w szablonie widoku *Index.cshtml* i dodatkowym "- Movie App" dodanym w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="a0d5d-179">Zawartość szablonu widoku *Index.cshtml* jest scalana z szablonem widoku *Widoki/Udostępnione/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="a0d5d-180">Pojedyncza odpowiedź HTML jest wysyłana do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="a0d5d-181">Szablony układu ułatwiają wprowadzanie zmian, które mają zastosowanie do wszystkich stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="a0d5d-182">Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a0d5d-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="a0d5d-184">Nasze trochę "danych" (w tym przypadku "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="a0d5d-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="a0d5d-185">komunikat) jest jednak zakodowany na twardo.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-185">message) is hard-coded, though.</span></span> <span data-ttu-id="a0d5d-186">Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie "M" (model) jeszcze.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="a0d5d-187">Przekazywanie danych z kontrolera do widoku</span><span class="sxs-lookup"><span data-stu-id="a0d5d-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="a0d5d-188">Akcje kontrolera są wywoływane w odpowiedzi na przychodzące żądanie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="a0d5d-189">Klasa kontrolera jest, gdy kod jest zapisywany, który obsługuje przychodzące żądania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="a0d5d-190">Kontroler pobiera dane ze źródła danych i decyduje, jaki typ odpowiedzi ma być wysyłany z powrotem do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="a0d5d-191">Szablony widoku mogą być używane z kontrolera do generowania i formatowania odpowiedzi HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="a0d5d-192">Kontrolerzy są odpowiedzialni za dostarczanie danych wymaganych do szablonu widoku do renderowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="a0d5d-193">Najlepsze rozwiązanie: Wyświetlanie szablonów **nie** należy wykonywać logiki biznesowej lub bezpośrednio wchodzić w interakcje z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="a0d5d-194">Zamiast tego szablon widoku powinien działać tylko z danymi dostarczonymi do niego przez kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="a0d5d-195">Utrzymanie tego "separacji problemów" pomaga zachować kod czyste, sprawdzalne i chywalne.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="a0d5d-196">Obecnie `Welcome` metoda w `HelloWorldController` klasie przyjmuje `name` i `ID` parametr, a następnie wyprowadza wartości bezpośrednio do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="a0d5d-197">Zamiast kontrolera renderowania tej odpowiedzi jako ciąg, należy zmienić kontroler, aby zamiast tego użyć szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="a0d5d-198">Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą być przekazywane z kontrolera do widoku w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="a0d5d-199">W tym celu kontroler umieszcza dane dynamiczne (parametry), których `ViewData` szablon widoku potrzebuje w słowniku, do który szablon widoku może uzyskać dostęp.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="a0d5d-200">W *HelloWorldController.cs*, zmień `Welcome` metodę, aby `Message` `NumTimes` dodać i `ViewData` wartość do słownika.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="a0d5d-201">Słownik `ViewData` jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; `ViewData` obiekt nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim czegoś.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="a0d5d-202">[System wiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje`name` `numTimes`nazwane parametry ( i ) z ciągu zapytania na pasku adresu do parametrów w metodzie.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="a0d5d-203">Kompletny plik *HelloWorldController.cs* wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="a0d5d-204">Obiekt `ViewData` słownika zawiera dane, które zostaną przekazane do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="a0d5d-205">Utwórz szablon widoku powitalnego o nazwie *Widoki/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="a0d5d-206">Utworzysz pętlę w szablonie widoku *Welcome.cshtml* `NumTimes`z wyświetlonym programem "Hello".</span><span class="sxs-lookup"><span data-stu-id="a0d5d-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="a0d5d-207">Zastąp zawartość *Views/HelloWorld/Welcome.cshtml* następującymi:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="a0d5d-208">Zapisz zmiany i przejdź do następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="a0d5d-209">Dane są pobierane z adresu URL i przekazywane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="a0d5d-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="a0d5d-210">Kontroler pakiety danych `ViewData` do słownika i przekazuje ten obiekt do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="a0d5d-211">Widok następnie renderuje dane w formacie HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-211">The view then renders the data as HTML to the browser.</span></span>

![Widok prywatności przedstawiający etykietę powitalną i wyrażenie Hello Rick pokazane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="a0d5d-213">W powyższym przykładzie `ViewData` słownik został użyty do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="a0d5d-214">W dalszej części samouczka model widoku jest używany do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="a0d5d-215">Podejście modelu widoku do przekazywania danych jest `ViewData` ogólnie znacznie preferowane w stosunku do podejścia słownika.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="a0d5d-216">Aby uzyskać więcej [informacji, zobacz Kiedy używać viewbag, ViewData lub TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)</span><span class="sxs-lookup"><span data-stu-id="a0d5d-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="a0d5d-217">W następnym samouczku tworzona jest baza danych filmów.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a0d5d-218">[Poprzedni](adding-controller.md)
> [następny](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="a0d5d-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0d5d-219">W tej sekcji `HelloWorldController` można zmodyfikować klasy za pomocą [razor](xref:mvc/views/razor) plików widoku do czyszczenia hermetyzować proces generowania odpowiedzi HTML do klienta.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="a0d5d-220">Plik szablonu widoku można utworzyć za pomocą razor.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-220">You create a view template file using Razor.</span></span> <span data-ttu-id="a0d5d-221">Szablony widoku oparte na brzytwie mają rozszerzenie pliku *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="a0d5d-222">Zapewniają one elegancki sposób tworzenia danych wyjściowych HTML z języka C#.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="a0d5d-223">Obecnie `Index` metoda zwraca ciąg z komunikatem, który jest zakodowany na stałe w klasie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="a0d5d-224">W `HelloWorldController` klasie zastąp `Index` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="a0d5d-225">Poprzedni kod wywołuje metodę kontrolera. <xref:Microsoft.AspNetCore.Mvc.Controller.View*></span><span class="sxs-lookup"><span data-stu-id="a0d5d-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="a0d5d-226">Używa szablonu widoku do generowania odpowiedzi HTML.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="a0d5d-227">Metody kontrolera (znane również jako `Index` *metody akcji),* takie <xref:Microsoft.AspNetCore.Mvc.IActionResult> jak powyższa metoda, <xref:Microsoft.AspNetCore.Mvc.ActionResult>zazwyczaj zwracają `string`(lub klasę pochodną), a nie typ podobny.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="a0d5d-228">Dodawanie widoku</span><span class="sxs-lookup"><span data-stu-id="a0d5d-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0d5d-229">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0d5d-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0d5d-230">Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="a0d5d-231">Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy element**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="a0d5d-232">W oknie dialogowym **Dodaj nowy przedmiot — MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="a0d5d-233">W polu wyszukiwania w prawym górnym rogu wprowadź *widok*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="a0d5d-234">Wybierz **widok razor**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-234">Select **Razor View**</span></span>

  * <span data-ttu-id="a0d5d-235">Zachowaj wartość pola **Nazwa,** *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="a0d5d-236">Wybierz **pozycję Dodaj**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-236">Select **Add**</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0d5d-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0d5d-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0d5d-239">Dodaj `Index` widok dla `HelloWorldController`pliku .</span><span class="sxs-lookup"><span data-stu-id="a0d5d-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="a0d5d-240">Dodaj nowy folder o nazwie *Widoki/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="a0d5d-241">Dodaj nowy plik do nazwy folderu *Widoki/HelloWorld* *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0d5d-242">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a0d5d-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0d5d-243">Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="a0d5d-244">Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="a0d5d-245">W oknie dialogowym **Nowy plik:**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="a0d5d-246">Wybierz **pozycję Sieć Web** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="a0d5d-247">Wybierz **pozycję Pusty plik HTML** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="a0d5d-248">Wpisz *index.cshtml* w polu **Nazwa.**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="a0d5d-249">Wybierz **pozycję Nowy**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-249">Select **New**.</span></span>

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="a0d5d-251">Zastąp zawartość pliku widoku *Widoki/HelloWorld/Index.cshtml* Razor na następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="a0d5d-252">Przejdź do adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a0d5d-253">Metoda `Index` w `HelloWorldController` nie zrobił wiele; uruchomiono instrukcję `return View();`, która określiła, że metoda powinna używać pliku szablonu widoku do renderowania odpowiedzi na przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="a0d5d-254">Ponieważ nie określono nazwy pliku szablonu widoku, domyślnie MVC używał domyślnego pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="a0d5d-255">Domyślny plik widoku ma taką samą nazwę jak metoda (`Index`), więc w */Views/HelloWorld/Index.cshtml* jest używany.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="a0d5d-256">Na poniższej ilustracji znajduje się napis "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="a0d5d-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="a0d5d-257">zakodowane w widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-257">hard-coded in the view.</span></span>

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="a0d5d-259">Zmienianie widoków i stron układu</span><span class="sxs-lookup"><span data-stu-id="a0d5d-259">Change views and layout pages</span></span>

<span data-ttu-id="a0d5d-260">Wybierz łącza menu **(MvcMovie**, **Home**i **Prywatność).**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="a0d5d-261">Każda strona zawiera ten sam układ menu.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="a0d5d-262">Układ menu jest zaimplementowany w pliku *Views/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a0d5d-263">Otwórz plik *Views/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a0d5d-264">[Szablony układu](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="a0d5d-265">Znajdź `@RenderBody()` linię.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a0d5d-266">`RenderBody`to symbol zastępczy, w którym wszystkie utworzone strony specyficzne dla widoku są wyświetlane, *zawinięte* na stronie układu.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a0d5d-267">Jeśli na przykład wybierzesz łącze **Prywatność,** widok **Widoki/Strona główna/Prywatność.cshtml** zostanie renderowany wewnątrz `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="a0d5d-268">Zmienianie tytułu, stopki i łącza menu w pliku układu</span><span class="sxs-lookup"><span data-stu-id="a0d5d-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="a0d5d-269">W elementów tytułu i `MvcMovie` stopki zmień na `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="a0d5d-270">Zmień element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` zakotwiczenia na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="a0d5d-271">Następujące znaczniki pokazuje wyróżnione zmiany:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="a0d5d-272">W poprzednim znaczników atrybut `asp-area` [zakotwiczenia Tag Helper został pominięty,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ponieważ ta aplikacja nie używa [obszarów](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="a0d5d-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="a0d5d-273">**Uwaga:** `Movies` Kontroler nie został zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="a0d5d-274">W tym momencie łącze `Movie App` nie jest funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="a0d5d-275">Zapisz zmiany i wybierz link **Prywatność.**</span><span class="sxs-lookup"><span data-stu-id="a0d5d-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="a0d5d-276">Zwróć uwagę, jak tytuł na karcie przeglądarki wyświetla **Politykę prywatności - Movie App** zamiast Polityki prywatności - **Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta Prywatność](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="a0d5d-278">Zaznacz łącze **Strona główna** i zwróć uwagę, że tytuł i tekst zakotwiczenia wyświetlają również aplikację **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="a0d5d-279">Byliśmy w stanie wprowadzić zmianę raz w szablonie układu i wszystkie strony w witrynie odzwierciedlają nowy tekst linku i nowy tytuł.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="a0d5d-280">Sprawdź plik *Views/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="a0d5d-281">Plik *Views/_ViewStart.cshtml* wprowadza do każdego widoku plik *Views/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="a0d5d-282">Właściwości `Layout` może służyć do ustawiania innego widoku układu `null` lub ustawić go na tak, aby nie plik układu będą używane.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="a0d5d-283">Zmień tytuł `<h2>` i element pliku widoku *Widoki/HelloWorld/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a0d5d-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="a0d5d-284">Tytuł i `<h2>` element są nieco inne, dzięki czemu można zobaczyć, który bit kodu zmienia wyświetlanie.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="a0d5d-285">`ViewData["Title"] = "Movie List";`w powyższym kodzie `Title` ustawia `ViewData` właściwość słownika na "Lista filmów".</span><span class="sxs-lookup"><span data-stu-id="a0d5d-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="a0d5d-286">Właściwość `Title` jest używana `<title>` w elemencie HTML na stronie układu:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="a0d5d-287">Zapisz zmianę i `https://localhost:{PORT}/HelloWorld`przejdź do .</span><span class="sxs-lookup"><span data-stu-id="a0d5d-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a0d5d-288">Należy zauważyć, że zmienił się tytuł przeglądarki, nagłówek podstawowy i nagłówki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="a0d5d-289">(Jeśli nie widzisz zmian w przeglądarce, być może wyświetlasz zawartość w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="a0d5d-290">Naciśnij klawisze Ctrl+F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera). Tytuł przeglądarki jest `ViewData["Title"]` tworzony z zestawem w szablonie widoku *Index.cshtml* i dodatkowym "- Movie App" dodanym w pliku układu.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="a0d5d-291">Zwróć również uwagę na sposób scalania zawartości szablonu widoku *Index.cshtml* z szablonem widoku *Widoki/Udostępnione/_Layout.cshtml* i wysłano do przeglądarki pojedynczą odpowiedź HTML.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="a0d5d-292">Szablony układu ułatwiają wprowadzanie zmian, które mają zastosowanie do wszystkich stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="a0d5d-293">Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a0d5d-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="a0d5d-295">Nasze trochę "danych" (w tym przypadku "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="a0d5d-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="a0d5d-296">komunikat) jest jednak zakodowany na twardo.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-296">message) is hard-coded, though.</span></span> <span data-ttu-id="a0d5d-297">Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie "M" (model) jeszcze.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="a0d5d-298">Przekazywanie danych z kontrolera do widoku</span><span class="sxs-lookup"><span data-stu-id="a0d5d-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="a0d5d-299">Akcje kontrolera są wywoływane w odpowiedzi na przychodzące żądanie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="a0d5d-300">Klasa kontrolera jest, gdy kod jest zapisywany, który obsługuje przychodzące żądania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="a0d5d-301">Kontroler pobiera dane ze źródła danych i decyduje, jaki typ odpowiedzi ma być wysyłany z powrotem do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="a0d5d-302">Szablony widoku mogą być używane z kontrolera do generowania i formatowania odpowiedzi HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="a0d5d-303">Kontrolerzy są odpowiedzialni za dostarczanie danych wymaganych do szablonu widoku do renderowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="a0d5d-304">Najlepsze rozwiązanie: Wyświetlanie szablonów **nie** należy wykonywać logiki biznesowej lub bezpośrednio wchodzić w interakcje z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="a0d5d-305">Zamiast tego szablon widoku powinien działać tylko z danymi dostarczonymi do niego przez kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="a0d5d-306">Utrzymanie tego "separacji problemów" pomaga zachować kod czyste, sprawdzalne i chywalne.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="a0d5d-307">Obecnie `Welcome` metoda w `HelloWorldController` klasie przyjmuje `name` i `ID` parametr, a następnie wyprowadza wartości bezpośrednio do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="a0d5d-308">Zamiast kontrolera renderowania tej odpowiedzi jako ciąg, należy zmienić kontroler, aby zamiast tego użyć szablonu widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="a0d5d-309">Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą być przekazywane z kontrolera do widoku w celu wygenerowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="a0d5d-310">W tym celu kontroler umieszcza dane dynamiczne (parametry), których `ViewData` szablon widoku potrzebuje w słowniku, do który szablon widoku może uzyskać dostęp.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="a0d5d-311">W *HelloWorldController.cs*, zmień `Welcome` metodę, aby `Message` `NumTimes` dodać i `ViewData` wartość do słownika.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="a0d5d-312">Słownik `ViewData` jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; `ViewData` obiekt nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim czegoś.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="a0d5d-313">[System wiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje`name` `numTimes`nazwane parametry ( i ) z ciągu zapytania na pasku adresu do parametrów w metodzie.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="a0d5d-314">Kompletny plik *HelloWorldController.cs* wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="a0d5d-315">Obiekt `ViewData` słownika zawiera dane, które zostaną przekazane do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="a0d5d-316">Utwórz szablon widoku powitalnego o nazwie *Widoki/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="a0d5d-317">Utworzysz pętlę w szablonie widoku *Welcome.cshtml* `NumTimes`z wyświetlonym programem "Hello".</span><span class="sxs-lookup"><span data-stu-id="a0d5d-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="a0d5d-318">Zastąp zawartość *Views/HelloWorld/Welcome.cshtml* następującymi:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="a0d5d-319">Zapisz zmiany i przejdź do następującego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a0d5d-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="a0d5d-320">Dane są pobierane z adresu URL i przekazywane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="a0d5d-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="a0d5d-321">Kontroler pakiety danych `ViewData` do słownika i przekazuje ten obiekt do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="a0d5d-322">Widok następnie renderuje dane w formacie HTML do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-322">The view then renders the data as HTML to the browser.</span></span>

![Widok prywatności przedstawiający etykietę powitalną i wyrażenie Hello Rick pokazane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="a0d5d-324">W powyższym przykładzie `ViewData` słownik został użyty do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="a0d5d-325">W dalszej części samouczka model widoku jest używany do przekazywania danych z kontrolera do widoku.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="a0d5d-326">Podejście modelu widoku do przekazywania danych jest `ViewData` ogólnie znacznie preferowane w stosunku do podejścia słownika.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="a0d5d-327">Aby uzyskać więcej [informacji, zobacz Kiedy używać viewbag, ViewData lub TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)</span><span class="sxs-lookup"><span data-stu-id="a0d5d-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="a0d5d-328">W następnym samouczku tworzona jest baza danych filmów.</span><span class="sxs-lookup"><span data-stu-id="a0d5d-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a0d5d-329">[Poprzedni](adding-controller.md)
> [następny](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="a0d5d-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
