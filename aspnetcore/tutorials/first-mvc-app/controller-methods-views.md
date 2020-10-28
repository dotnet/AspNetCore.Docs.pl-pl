---
title: Część 6, metody kontrolera i widoki w ASP.NET Core
author: rick-anderson
description: Część 6 Dodaj model do aplikacji ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
no-loc:
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 745703aaa4ceb39c75789bab0bde4564f3d79a30
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678550"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="53a61-103">Część 6, metody kontrolera i widoki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53a61-103">Part 6, controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="53a61-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53a61-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53a61-105">Dobrze zaczynasz korzystać z aplikacji filmowej, ale prezentacja nie jest idealna, na przykład **ReleaseDate** powinny być dwa słowa.</span><span class="sxs-lookup"><span data-stu-id="53a61-105">We have a good start to the movie app, but the presentation isn't ideal, for example, **ReleaseDate** should be two words.</span></span>

![Widok indeksu: Data wydania to jeden wyraz (bez spacji), a każda Data wydania filmu przedstawia godzinę 12 AM](working-with-sql/_static/m55.png)

<span data-ttu-id="53a61-107">Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze poniżej:</span><span class="sxs-lookup"><span data-stu-id="53a61-107">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="53a61-108">W następnym samouczku omówiono wszystkie [Adnotacje](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) .</span><span class="sxs-lookup"><span data-stu-id="53a61-108">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="53a61-109">Atrybut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="53a61-109">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="53a61-110">Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="53a61-110">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="53a61-111">`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych jest wymagana, aby Entity Framework Core prawidłowo mapować `Price` do waluty w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="53a61-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="53a61-112">Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="53a61-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="53a61-113">Przejdź do `Movies` kontrolera i przytrzymaj wskaźnik myszy nad linkiem **edycji** , aby zobaczyć docelowy adres URL.</span><span class="sxs-lookup"><span data-stu-id="53a61-113">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym adresem URL linku https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="53a61-115">Linki **Edytuj** , **szczegóły** i **Usuń** są generowane przez pomocnika podstawowego tagu zakotwiczenia MVC w pliku *views/filmy/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="53a61-115">The **Edit** , **Details** , and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="53a61-116">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w :::no-loc(Razor)::: plikach.</span><span class="sxs-lookup"><span data-stu-id="53a61-116">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="53a61-117">W powyższym kodzie, `AnchorTagHelper` dynamicznie generuje `href` wartość atrybutu HTML z metody akcji kontrolera i identyfikatora trasy. Możesz użyć **widoku źródła** z ulubionej przeglądarki lub użyć narzędzi programistycznych do sprawdzenia wygenerowanego znacznika.</span><span class="sxs-lookup"><span data-stu-id="53a61-117">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="53a61-118">Poniżej przedstawiono część wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="53a61-118">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="53a61-119">Odwołaj format zestawu [routingu](xref:mvc/controllers/routing) w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="53a61-119">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="53a61-120">ASP.NET Core przetłumaczy `https://localhost:5001/Movies/Edit/4` żądanie na `Edit` metodę akcji `Movies` kontrolera z parametrem `Id` 4.</span><span class="sxs-lookup"><span data-stu-id="53a61-120">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="53a61-121">(Metody kontrolera są również nazywane metodami akcji).</span><span class="sxs-lookup"><span data-stu-id="53a61-121">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="53a61-122">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) to jedna z najpopularniejszych nowych funkcji w programie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53a61-122">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="53a61-123">Aby uzyskać więcej informacji, zobacz [dodatkowe zasoby](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="53a61-123">For more information, see [Additional resources](#additional-resources).</span></span>

<a name="get-post"></a>

<span data-ttu-id="53a61-124">Otwórz `Movies` kontroler i Przeanalizuj dwie `Edit` metody akcji.</span><span class="sxs-lookup"><span data-stu-id="53a61-124">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="53a61-125">Poniższy kod przedstawia `HTTP GET Edit` metodę, która pobiera film i wypełnia formularz edycji wygenerowany przez plik *Edit. cshtml* :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="53a61-125">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* :::no-loc(Razor)::: file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="53a61-126">Poniższy kod przedstawia `HTTP POST Edit` metodę, która przetwarza ogłoszone wartości filmu:</span><span class="sxs-lookup"><span data-stu-id="53a61-126">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="53a61-127">Poniższy kod przedstawia `HTTP POST Edit` metodę, która przetwarza ogłoszone wartości filmu:</span><span class="sxs-lookup"><span data-stu-id="53a61-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="53a61-128">Ten `[Bind]` atrybut jest jednym ze sposobów ochrony przed [nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="53a61-128">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="53a61-129">Należy uwzględnić tylko właściwości w `[Bind]` atrybucie, który ma zostać zmieniony.</span><span class="sxs-lookup"><span data-stu-id="53a61-129">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="53a61-130">Aby uzyskać więcej informacji, zobacz [Ochrona kontrolera przed nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="53a61-130">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="53a61-131">[Modele widoków](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) zapewniają alternatywne podejście do zapobiegania nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="53a61-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="53a61-132">Zwróć uwagę, że druga `Edit` Metoda działania jest poprzedzona `[HttpPost]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="53a61-132">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="53a61-133">`HttpPost`Ten atrybut określa, że ta `Edit` Metoda może być wywoływana *tylko* w przypadku `POST` żądań.</span><span class="sxs-lookup"><span data-stu-id="53a61-133">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="53a61-134">Można zastosować `[HttpGet]` atrybut do pierwszej metody edycji, ale nie jest to konieczne, ponieważ jest to `[HttpGet]` wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="53a61-134">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="53a61-135">Ten `ValidateAntiForgeryToken` atrybut służy do [zapobiegania fałszerstwu żądania](xref:security/anti-request-forgery) i jest sparowany z tokenem chroniącym przed fałszerstwem wygenerowanym w pliku widoku edycji ( *widoki/filmy/Edit. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="53a61-135">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file ( *Views/Movies/Edit.cshtml* ).</span></span> <span data-ttu-id="53a61-136">Plik widoku edycji generuje token chroniący przed fałszerstwem za pomocą [pomocnika tagu formularza](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="53a61-136">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="53a61-137">[Pomocnik tagu formularza](xref:mvc/views/working-with-forms) generuje ukryty token chroniący przed fałszerstwem, który musi być zgodny z `[ValidateAntiForgeryToken]` wygenerowanym tokenem chroniącym przed fałszerstwem w `Edit` metodzie kontrolera filmów.</span><span class="sxs-lookup"><span data-stu-id="53a61-137">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="53a61-138">Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="53a61-138">For more information, see <xref:security/anti-request-forgery>.</span></span>

<span data-ttu-id="53a61-139">`HttpGet Edit`Metoda przyjmuje `ID` parametr filmu, wyszukuje film przy użyciu metody Entity Framework `FindAsync` i zwraca wybrany film do widoku edycji.</span><span class="sxs-lookup"><span data-stu-id="53a61-139">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `FindAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="53a61-140">Jeśli nie można znaleźć filmu, `NotFound` zwracany jest (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="53a61-140">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="53a61-141">Gdy system szkieletu utworzył widok edycji, zbadał `Movie` klasę i utworzony kod w celu renderowania `<label>` i `<input>` elementów dla każdej właściwości klasy.</span><span class="sxs-lookup"><span data-stu-id="53a61-141">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="53a61-142">W poniższym przykładzie przedstawiono widok edycji, który został wygenerowany przez system szkieletu programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="53a61-142">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="53a61-143">Zwróć uwagę, jak szablon widoku zawiera `@model MvcMovie.Models.Movie` instrukcję w górnej części pliku.</span><span class="sxs-lookup"><span data-stu-id="53a61-143">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="53a61-144">`@model MvcMovie.Models.Movie` Określa, że widok oczekuje modelu dla szablonu widoku, który ma być typu `Movie` .</span><span class="sxs-lookup"><span data-stu-id="53a61-144">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="53a61-145">Kod szkieletowy używa kilku metod pomocnika tagów do uproszczenia znacznika HTML.</span><span class="sxs-lookup"><span data-stu-id="53a61-145">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="53a61-146">[Pomocnik tagu etykiety](xref:mvc/views/working-with-forms) wyświetla nazwę pola ("title", "ReleaseDate", "gatunek" lub "price").</span><span class="sxs-lookup"><span data-stu-id="53a61-146">The [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="53a61-147">[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) renderuje `<input>` element HTML.</span><span class="sxs-lookup"><span data-stu-id="53a61-147">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="53a61-148">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms) wyświetla wszystkie komunikaty weryfikacyjne skojarzone z tą właściwością.</span><span class="sxs-lookup"><span data-stu-id="53a61-148">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="53a61-149">Uruchom aplikację i przejdź do `/Movies` adresu URL.</span><span class="sxs-lookup"><span data-stu-id="53a61-149">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="53a61-150">Kliknij link **Edytuj** .</span><span class="sxs-lookup"><span data-stu-id="53a61-150">Click an **Edit** link.</span></span> <span data-ttu-id="53a61-151">Sprawdź Źródło strony w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="53a61-151">In the browser, view the source for the page.</span></span> <span data-ttu-id="53a61-152">Wygenerowany kod HTML dla `<form>` elementu jest przedstawiony poniżej.</span><span class="sxs-lookup"><span data-stu-id="53a61-152">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="53a61-153">`<input>`Elementy znajdują się w `HTML <form>` elemencie, którego `action` atrybut ma ustawioną wartość post na `/Movies/Edit/id` adres URL.</span><span class="sxs-lookup"><span data-stu-id="53a61-153">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="53a61-154">Dane formularza zostaną opublikowane na serwerze po `Save` kliknięciu przycisku.</span><span class="sxs-lookup"><span data-stu-id="53a61-154">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="53a61-155">Ostatni wiersz przed zamykającym `</form>` elementu pokazuje ukryty token [XSRF](xref:security/anti-request-forgery) wygenerowany przez [pomocnika tagów formularza](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="53a61-155">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="53a61-156">Przetwarzanie żądania POST</span><span class="sxs-lookup"><span data-stu-id="53a61-156">Processing the POST Request</span></span>

<span data-ttu-id="53a61-157">Na poniższej liście przedstawiono `[HttpPost]` wersję `Edit` metody akcji.</span><span class="sxs-lookup"><span data-stu-id="53a61-157">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="53a61-158">Ten `[ValidateAntiForgeryToken]` atrybut sprawdza poprawność ukrytego tokenu [XSRF](xref:security/anti-request-forgery) wygenerowanego przez generatora tokenów chroniących przed fałszowaniem w [Pomocniku tagów formularza](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="53a61-158">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="53a61-159">System [powiązań modelu](xref:mvc/models/model-binding) przyjmuje wartości ogłoszonych formularzy i tworzy `Movie` obiekt, który jest przesyłany jako `movie` parametr.</span><span class="sxs-lookup"><span data-stu-id="53a61-159">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="53a61-160">`ModelState.IsValid`Właściwość weryfikuje, że dane przesłane w formularzu mogą służyć do modyfikowania (edycji lub aktualizowania) `Movie` obiektu.</span><span class="sxs-lookup"><span data-stu-id="53a61-160">The `ModelState.IsValid` property verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="53a61-161">Jeśli dane są prawidłowe, zostaną zapisane.</span><span class="sxs-lookup"><span data-stu-id="53a61-161">If the data is valid, it's saved.</span></span> <span data-ttu-id="53a61-162">Zaktualizowane (edytowane) dane filmu są zapisywane w bazie danych przez wywołanie `SaveChangesAsync` metody kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53a61-162">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="53a61-163">Po zapisaniu danych kod przekierowuje użytkownika do `Index` metody akcji `MoviesController` klasy, która wyświetla kolekcję filmów, włącznie z wprowadzonymi zmianami.</span><span class="sxs-lookup"><span data-stu-id="53a61-163">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="53a61-164">Przed opublikowaniem formularza na serwerze sprawdzanie poprawności po stronie klienta sprawdza wszystkie reguły sprawdzania poprawności w polach.</span><span class="sxs-lookup"><span data-stu-id="53a61-164">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="53a61-165">Jeśli wystąpią jakieś błędy sprawdzania poprawności, zostanie wyświetlony komunikat o błędzie z informacją, że formularz nie zostanie opublikowany.</span><span class="sxs-lookup"><span data-stu-id="53a61-165">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="53a61-166">Jeśli język JavaScript jest wyłączony, nie będzie można sprawdzić poprawności po stronie klienta, ale serwer wykryje ogłoszone wartości, które nie są prawidłowe, a wartości formularza zostaną wyświetlone ponownie przy użyciu komunikatów o błędach.</span><span class="sxs-lookup"><span data-stu-id="53a61-166">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="53a61-167">W dalszej części samouczka sprawdzimy [Sprawdzanie poprawności modelu](xref:mvc/models/validation) w bardziej szczegółowy sposób.</span><span class="sxs-lookup"><span data-stu-id="53a61-167">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="53a61-168">[Pomocnik tagów walidacji](xref:mvc/views/working-with-forms) w szablonie *widoki/filmy/edytowanie. cshtml* ma zadbać o wyświetlenie odpowiednich komunikatów o błędach.</span><span class="sxs-lookup"><span data-stu-id="53a61-168">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Widok edycji: wyjątek dla nieprawidłowej wartości ceny ABC wskazuje, że cena pola musi być liczbą.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="53a61-171">Wszystkie `HttpGet` metody w kontrolerze filmu są zgodne z podobnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="53a61-171">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="53a61-172">Uzyskują one obiekt filmu (lub listę obiektów w przypadku `Index` ) i przekazują obiekt (model) do widoku.</span><span class="sxs-lookup"><span data-stu-id="53a61-172">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="53a61-173">`Create`Metoda przekazuje pusty obiekt filmu do `Create` widoku.</span><span class="sxs-lookup"><span data-stu-id="53a61-173">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="53a61-174">Wszystkie metody, które tworzą, edytują, usuwają lub w inny sposób modyfikują dane, to w ramach `[HttpPost]` przeciążenia metody.</span><span class="sxs-lookup"><span data-stu-id="53a61-174">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="53a61-175">Modyfikowanie danych w `HTTP GET` metodzie stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="53a61-175">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="53a61-176">Modyfikowanie danych w `HTTP GET` metodzie również narusza najlepsze rozwiązania protokołu HTTP i wzorzec [rest](http://rest.elkstein.org/) architektury, który określa, że żądania GET nie powinny zmieniać stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53a61-176">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="53a61-177">Innymi słowy wykonanie operacji GET powinno być operacją bezpieczną, która nie ma efektów ubocznych i nie modyfikuje utrwalonych danych.</span><span class="sxs-lookup"><span data-stu-id="53a61-177">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53a61-178">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="53a61-178">Additional resources</span></span>

* [<span data-ttu-id="53a61-179">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="53a61-179">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="53a61-180">Wprowadzenie do pomocy tagów</span><span class="sxs-lookup"><span data-stu-id="53a61-180">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="53a61-181">Autorzy tagów</span><span class="sxs-lookup"><span data-stu-id="53a61-181">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* <span data-ttu-id="53a61-182">Chroń kontroler przed [nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span><span class="sxs-lookup"><span data-stu-id="53a61-182">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="53a61-183">Modele widoków</span><span class="sxs-lookup"><span data-stu-id="53a61-183">ViewModels</span></span>](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="53a61-184">Pomocnik tagu formularza</span><span class="sxs-lookup"><span data-stu-id="53a61-184">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="53a61-185">Pomocnik tagu wejściowego</span><span class="sxs-lookup"><span data-stu-id="53a61-185">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="53a61-186">Pomocnik tagu etykiety</span><span class="sxs-lookup"><span data-stu-id="53a61-186">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="53a61-187">Pomocnik tagu wyboru</span><span class="sxs-lookup"><span data-stu-id="53a61-187">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="53a61-188">Pomocnik tagów walidacji</span><span class="sxs-lookup"><span data-stu-id="53a61-188">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="53a61-189">[Poprzedni](working-with-sql.md) 
>  [Dalej](search.md)</span><span class="sxs-lookup"><span data-stu-id="53a61-189">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
