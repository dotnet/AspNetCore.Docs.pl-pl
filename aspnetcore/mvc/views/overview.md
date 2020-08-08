---
title: Widoki w ASP.NET Core MVC
author: ardalis
description: Dowiedz się, w jaki sposób widoki obsługują prezentację danych aplikacji i interakcję użytkownika w ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020876"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="4cb43-103">Widoki w ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4cb43-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="4cb43-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4cb43-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4cb43-105">W tym dokumencie objaśniono widoki używane w aplikacjach ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4cb43-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="4cb43-106">Aby uzyskać informacje na temat Razor stron, zobacz [wprowadzenie do Razor stron](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="4cb43-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="4cb43-107">W wzorcu Model-View-Controller (MVC) *Widok* obsługuje prezentację danych aplikacji i interakcję użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="4cb43-108">Widok to szablon HTML z osadzonym [ Razor znacznikiem](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="4cb43-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="4cb43-109">RazorZnaczniki to kod, który współdziała ze znacznikiem HTML, aby utworzyć stronę sieci Web, która jest wysyłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="4cb43-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="4cb43-110">W ASP.NET Core MVC, widoki są plikami *. cshtml* , które używają [języka programowania C#](/dotnet/csharp/) w Razor znacznikach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="4cb43-111">Zazwyczaj pliki widoku są pogrupowane w foldery o nazwie dla każdej z [kontrolerów](xref:mvc/controllers/actions)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="4cb43-112">Foldery są przechowywane w folderze *widoki* w katalogu głównym aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4cb43-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Folder widoki w Eksplorator rozwiązań programu Visual Studio jest otwarty z folderem macierzystym otwartym, aby pokazać informacje o plikach. cshtml, Contact. cshtml i index. cshtml](overview/_static/views_solution_explorer.png)

<span data-ttu-id="4cb43-114">Kontroler *główny* jest reprezentowany przez folder *macierzysty* w folderze *widoki* .</span><span class="sxs-lookup"><span data-stu-id="4cb43-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="4cb43-115">Folder *macierzysty* zawiera widoki stron sieci Web *Informacje o*programie, *kontakt*i *indeks* (Strona główna).</span><span class="sxs-lookup"><span data-stu-id="4cb43-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="4cb43-116">Gdy użytkownik zażąda jednej z tych trzech stron sieci Web, akcje kontrolera w kontrolerze *głównym* określają, które z tych trzech widoków są używane do kompilowania i zwracania strony internetowej do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="4cb43-117">Użyj [układów](xref:mvc/views/layout) , aby zapewnić spójne sekcje stron i ograniczyć powtarzanie kodu.</span><span class="sxs-lookup"><span data-stu-id="4cb43-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="4cb43-118">Układy często zawierają nagłówek, nawigację i elementy menu oraz stopkę.</span><span class="sxs-lookup"><span data-stu-id="4cb43-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="4cb43-119">Nagłówek i stopka zazwyczaj zawierają standardowe znaczniki dla wielu elementów metadanych i linki do zasobów skryptu i stylu.</span><span class="sxs-lookup"><span data-stu-id="4cb43-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="4cb43-120">Układy umożliwiają uniknięcie tego standardowego znacznika w widokach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="4cb43-121">[Częściowe widoki](xref:mvc/views/partial) redukują duplikowanie kodu przez zarządzanie częścią widoków wielokrotnego użytku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="4cb43-122">Na przykład widok częściowy jest przydatny w przypadku życiorysu autora w witrynie sieci Web blogu, która pojawia się w kilku widokach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="4cb43-123">Biografia autora to zwykła zawartość widoku i nie wymaga wykonania kodu w celu utworzenia zawartości dla strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4cb43-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="4cb43-124">Zawartość biografii autora jest dostępna dla samego powiązania widoku przez model, więc użycie widoku częściowego dla tego typu zawartości jest idealne.</span><span class="sxs-lookup"><span data-stu-id="4cb43-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="4cb43-125">[Składniki widoku](xref:mvc/views/view-components) są podobne do widoków częściowych, które umożliwiają zredukowanie powtarzalnego kodu, ale są odpowiednie do wyświetlania zawartości, która wymaga uruchomienia kodu na serwerze w celu renderowania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4cb43-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="4cb43-126">Składniki widoku są przydatne, gdy renderowana zawartość wymaga interakcji z bazą danych, na przykład w przypadku koszyka w witrynie sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4cb43-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="4cb43-127">Składniki widoku nie są ograniczone do powiązania modelu, aby można było utworzyć dane wyjściowe strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4cb43-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="4cb43-128">Zalety korzystania z widoków</span><span class="sxs-lookup"><span data-stu-id="4cb43-128">Benefits of using views</span></span>

<span data-ttu-id="4cb43-129">Wyświetla pomoc w celu ustalenia [rozdzielenia problemów](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) w aplikacji MVC przez oddzielenie znaczników interfejsu użytkownika od innych części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="4cb43-130">Następujący projekt SoC umożliwia modularną aplikację, która zapewnia kilka korzyści:</span><span class="sxs-lookup"><span data-stu-id="4cb43-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="4cb43-131">Aplikacja jest łatwiejsza w obsłudze, ponieważ jest lepiej zorganizowana.</span><span class="sxs-lookup"><span data-stu-id="4cb43-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="4cb43-132">Widoki są zazwyczaj pogrupowane według funkcji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="4cb43-133">Ułatwia to Znajdowanie powiązanych widoków podczas pracy nad funkcją.</span><span class="sxs-lookup"><span data-stu-id="4cb43-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="4cb43-134">Części aplikacji są luźno powiązane.</span><span class="sxs-lookup"><span data-stu-id="4cb43-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="4cb43-135">Widoki aplikacji można kompilować i aktualizować niezależnie od składników logiki biznesowej i dostępu do danych.</span><span class="sxs-lookup"><span data-stu-id="4cb43-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="4cb43-136">Widoki aplikacji można modyfikować bez konieczności aktualizowania innych części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="4cb43-137">Łatwiejsze jest przetestowanie części interfejsu użytkownika aplikacji, ponieważ widoki są osobnymi jednostkami.</span><span class="sxs-lookup"><span data-stu-id="4cb43-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="4cb43-138">Ze względu na lepszą organizację nie można przypadkowo powtarzać sekcji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="4cb43-139">Tworzenie widoku</span><span class="sxs-lookup"><span data-stu-id="4cb43-139">Creating a view</span></span>

<span data-ttu-id="4cb43-140">Widoki, które są specyficzne dla kontrolera, są tworzone w folderze *widoki/[ControllerName]* .</span><span class="sxs-lookup"><span data-stu-id="4cb43-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="4cb43-141">Widoki, które są współużytkowane przez kontrolery, są umieszczane w folderze *widoki/udostępnione* .</span><span class="sxs-lookup"><span data-stu-id="4cb43-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="4cb43-142">Aby utworzyć widok, Dodaj nowy plik i nadaj mu taką samą nazwę jak skojarzona z nim akcja kontrolera z rozszerzeniem *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4cb43-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="4cb43-143">Aby utworzyć widok, który odpowiada akcji *about* w kontrolerze *głównym* , Utwórz plik *about. cshtml* w folderze *widoki/Home* :</span><span class="sxs-lookup"><span data-stu-id="4cb43-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="4cb43-144">*Razor* znaczniki zaczynają się od `@` symbolu.</span><span class="sxs-lookup"><span data-stu-id="4cb43-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="4cb43-145">Uruchom instrukcje języka C#, umieszczając kod w języku C# w [ Razor blokach kodu](xref:mvc/views/razor#razor-code-blocks) ustawionych przez nawiasy klamrowe ( `{ ... }` ).</span><span class="sxs-lookup"><span data-stu-id="4cb43-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="4cb43-146">Na przykład zapoznaj się z tematem przypisywanie elementu "informacje" `ViewData["Title"]` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="4cb43-147">Możesz wyświetlić wartości w kodzie HTML, po prostu przywołując wartość `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="4cb43-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="4cb43-148">Zapoznaj się z zawartością `<h2>` `<h3>` elementów i powyżej.</span><span class="sxs-lookup"><span data-stu-id="4cb43-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="4cb43-149">Zawartość widoku pokazana powyżej jest tylko częścią całej strony sieci Web, która jest renderowana dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="4cb43-150">Pozostała część układu strony i inne typowe aspekty widoku są określone w innych plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="4cb43-151">Aby dowiedzieć się więcej, zobacz [temat układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="4cb43-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="4cb43-152">Jak kontrolery określają widoki</span><span class="sxs-lookup"><span data-stu-id="4cb43-152">How controllers specify views</span></span>

<span data-ttu-id="4cb43-153">Widoki są zwykle zwracane z akcji jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), które są typu [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="4cb43-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="4cb43-154">Metoda działania może tworzyć i zwracać `ViewResult` bezpośrednio, ale nie jest to powszechnie wykonywane.</span><span class="sxs-lookup"><span data-stu-id="4cb43-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="4cb43-155">Ponieważ większość kontrolerów dziedziczy po [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller), wystarczy użyć `View` metody pomocnika do zwrócenia `ViewResult` :</span><span class="sxs-lookup"><span data-stu-id="4cb43-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="4cb43-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="4cb43-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="4cb43-157">Po powrocie tej akcji widok *Informacje o. cshtml* widoczny w ostatniej sekcji jest renderowany jako następująca strona sieci Web:</span><span class="sxs-lookup"><span data-stu-id="4cb43-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Informacje o stronie renderowanej w przeglądarce Edge](overview/_static/about-page.png)

<span data-ttu-id="4cb43-159">`View`Metoda pomocnika ma kilka przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="4cb43-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="4cb43-160">Opcjonalnie można określić:</span><span class="sxs-lookup"><span data-stu-id="4cb43-160">You can optionally specify:</span></span>

* <span data-ttu-id="4cb43-161">Jawny widok do zwrócenia:</span><span class="sxs-lookup"><span data-stu-id="4cb43-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="4cb43-162">[Model](xref:mvc/models/model-binding) do przekazania do widoku:</span><span class="sxs-lookup"><span data-stu-id="4cb43-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="4cb43-163">Zarówno widok, jak i model:</span><span class="sxs-lookup"><span data-stu-id="4cb43-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="4cb43-164">Wyświetlanie odnajdywania</span><span class="sxs-lookup"><span data-stu-id="4cb43-164">View discovery</span></span>

<span data-ttu-id="4cb43-165">Gdy akcja zwraca widok, odbywa się proces o nazwie *odnajdywanie widoku* .</span><span class="sxs-lookup"><span data-stu-id="4cb43-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="4cb43-166">Ten proces określa, który plik widoku jest używany na podstawie nazwy widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="4cb43-167">Domyślne zachowanie `View` metody ( `return View();` ) ma zwrócić widok o tej samej nazwie co Metoda akcji, z której jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="4cb43-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="4cb43-168">Na przykład nazwa metody *informacji* `ActionResult` kontrolera służy do wyszukiwania pliku widoku o nazwie *about. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4cb43-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="4cb43-169">Najpierw środowisko uruchomieniowe przeszukuje folder *widoki/[ControllerName]* dla widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="4cb43-170">Jeśli w tym miejscu nie zostanie znaleziony pasujący widok, przeszukiwany jest folder *udostępniony* dla tego widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="4cb43-171">Nie ma znaczenia, czy niejawnie zwracają `ViewResult` `return View();` nazwę widoku do `View` metody z `return View("<ViewName>");` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="4cb43-172">W obu przypadkach należy wyświetlić wyszukiwanie w poszukiwaniu zgodnego pliku widoku w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="4cb43-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="4cb43-173">*Widoki/ \[ kontrolername]/ \[ viewName]. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4cb43-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="4cb43-174">*Widoki/udostępnione/ \[ viewName]. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4cb43-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="4cb43-175">Zamiast nazwy widoku można podać ścieżkę pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="4cb43-176">Jeśli używana jest ścieżka bezwzględna rozpoczynająca się od elementu głównego aplikacji (opcjonalnie rozpoczynając od "/" lub "~/"), należy określić rozszerzenie *cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4cb43-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="4cb43-177">Możesz również użyć ścieżki względnej, aby określić widoki w różnych katalogach bez rozszerzenia *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4cb43-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="4cb43-178">Wewnątrz `HomeController` można zwrócić widok *indeksu* widoków *Zarządzanie* ze ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="4cb43-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="4cb43-179">Analogicznie, można wskazać bieżący katalog specyficzny dla kontrolera z prefiksem "./":</span><span class="sxs-lookup"><span data-stu-id="4cb43-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="4cb43-180">[Widoki częściowe](xref:mvc/views/partial) i [składniki widoku](xref:mvc/views/view-components) używają podobnych mechanizmów odnajdywania (ale nie identycznych).</span><span class="sxs-lookup"><span data-stu-id="4cb43-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="4cb43-181">Można dostosować domyślną Konwencję dotyczącą sposobu, w jaki widoki znajdują się w aplikacji, przy użyciu niestandardowego [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span><span class="sxs-lookup"><span data-stu-id="4cb43-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="4cb43-182">Widok odnajdywania polega na znalezieniu plików widoku według nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="4cb43-183">Jeśli w podstawowym systemie plików jest rozróżniana wielkość liter, nazwy widoków są prawdopodobnie rozróżniane.</span><span class="sxs-lookup"><span data-stu-id="4cb43-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="4cb43-184">Aby zapewnić zgodność między systemami operacyjnymi, Uwzględnij wielkość liter między nazwami kontrolerów i akcji oraz skojarzonymi folderami widoków i nazwami plików.</span><span class="sxs-lookup"><span data-stu-id="4cb43-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="4cb43-185">Jeśli wystąpi błąd, że nie można odnaleźć pliku widoku podczas pracy z systemem plików z uwzględnieniem wielkości liter, upewnij się, że wielkość liter jest zgodna między żądanym plikiem widoku a rzeczywistą nazwą pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="4cb43-186">Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi organizowania struktury plików dla widoków, aby odzwierciedlały relacje między kontrolerami, działaniami i widokami w celu utrzymania i przejrzystości.</span><span class="sxs-lookup"><span data-stu-id="4cb43-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="4cb43-187">Przekazywanie danych do widoków</span><span class="sxs-lookup"><span data-stu-id="4cb43-187">Passing data to views</span></span>

<span data-ttu-id="4cb43-188">Przekazywanie danych do widoków przy użyciu kilku metod:</span><span class="sxs-lookup"><span data-stu-id="4cb43-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="4cb43-189">Dane silnie wpisane: ViewModel</span><span class="sxs-lookup"><span data-stu-id="4cb43-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="4cb43-190">Dane niejednoznacznie wpisane</span><span class="sxs-lookup"><span data-stu-id="4cb43-190">Weakly typed data</span></span>
  * <span data-ttu-id="4cb43-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="4cb43-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="4cb43-192">Dane silnie wpisane (ViewModel)</span><span class="sxs-lookup"><span data-stu-id="4cb43-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="4cb43-193">Najbardziej niezawodne podejście polega na określeniu typu [modelu](xref:mvc/models/model-binding) w widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="4cb43-194">Ten model jest często określany jako *ViewModel*.</span><span class="sxs-lookup"><span data-stu-id="4cb43-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="4cb43-195">Wystąpienie typu ViewModel można przekazać do widoku z akcji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="4cb43-196">Użycie ViewModel do przekazywania danych do widoku pozwala widokowi korzystać z funkcji sprawdzania *silnych* typów.</span><span class="sxs-lookup"><span data-stu-id="4cb43-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="4cb43-197">*Silne wpisywanie* (lub *silnie wpisane*) oznacza, że każda zmienna i stała ma jawnie zdefiniowany typ (na przykład,, `string` `int` lub `DateTime` ).</span><span class="sxs-lookup"><span data-stu-id="4cb43-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="4cb43-198">Ważność typów używanych w widoku jest sprawdzana w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="4cb43-199">[Program Visual Studio](https://visualstudio.microsoft.com) i lista [Visual Studio Code](https://code.visualstudio.com/) grupy o jednoznacznie określonym typie przy użyciu funkcji o nazwie [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="4cb43-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="4cb43-200">Aby wyświetlić właściwości ViewModel, wpisz nazwę zmiennej dla ViewModel, a następnie kropkę ( `.` ).</span><span class="sxs-lookup"><span data-stu-id="4cb43-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="4cb43-201">Dzięki temu można szybciej napisać kod z mniejszą liczbą błędów.</span><span class="sxs-lookup"><span data-stu-id="4cb43-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="4cb43-202">Określ model przy użyciu `@model` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="4cb43-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="4cb43-203">Użyj modelu z `@Model` :</span><span class="sxs-lookup"><span data-stu-id="4cb43-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="4cb43-204">Aby zapewnić modelowi widok, kontroler przekazuje go jako parametr:</span><span class="sxs-lookup"><span data-stu-id="4cb43-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="4cb43-205">Nie ma żadnych ograniczeń dotyczących typów modelu, które można dostarczyć do widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="4cb43-206">Zalecamy używanie zwykłego starego obiektu CLR (POCO) modele widoków z niewielkimi lub żadnymi zachowaniami (metodami).</span><span class="sxs-lookup"><span data-stu-id="4cb43-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="4cb43-207">Zazwyczaj klasy ViewModel są przechowywane w folderze *models* lub w oddzielnym folderze *modele widoków* w katalogu głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="4cb43-208">*Adres* ViewModel używany w powyższym przykładzie to poco ViewModel przechowywany w pliku o nazwie *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="4cb43-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="4cb43-209">Nic nie pozwala na korzystanie z tych samych klas zarówno dla typów ViewModel, jak i typów modelu biznesowego.</span><span class="sxs-lookup"><span data-stu-id="4cb43-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="4cb43-210">Korzystanie z oddzielnych modeli pozwala jednak na różne widoki niezależnie od logiki biznesowej i elementów dostępu do danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4cb43-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="4cb43-211">Rozdzielenie modeli i modele widoków zapewnia także korzyści z używania [modelu powiązania](xref:mvc/models/model-binding) i [walidacji](xref:mvc/models/validation) danych wysyłanych do aplikacji przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="4cb43-212">Dane słabo wpisane (ViewData, ViewData Attribute i ViewBag)</span><span class="sxs-lookup"><span data-stu-id="4cb43-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="4cb43-213">`ViewBag`*nie jest dostępny w Razor Strony.*</span><span class="sxs-lookup"><span data-stu-id="4cb43-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="4cb43-214">W przypadku widoków o jednoznacznie określonym typie widoki mają dostęp do jednoznacznie *wpisanej* kolekcji (nazywanej również *luźno wpisaną*) kolekcją danych.</span><span class="sxs-lookup"><span data-stu-id="4cb43-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="4cb43-215">W przeciwieństwie do mocnych typów, *słabych typów* (lub *luźnych typów*) oznacza, że nie deklaruje jawnie typu danych, z których korzystasz.</span><span class="sxs-lookup"><span data-stu-id="4cb43-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="4cb43-216">Możesz użyć kolekcji nieokreślonych danych do przekazywania małych ilości danych do i z kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="4cb43-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="4cb43-217">Przekazywanie danych między...</span><span class="sxs-lookup"><span data-stu-id="4cb43-217">Passing data between a ...</span></span>                        | <span data-ttu-id="4cb43-218">Przykład</span><span class="sxs-lookup"><span data-stu-id="4cb43-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="4cb43-219">Kontroler i widok</span><span class="sxs-lookup"><span data-stu-id="4cb43-219">Controller and a view</span></span>                             | <span data-ttu-id="4cb43-220">Wypełnianie listy rozwijanej danymi.</span><span class="sxs-lookup"><span data-stu-id="4cb43-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="4cb43-221">Widok i widok [układu](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="4cb43-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="4cb43-222">Ustawianie **\<title>** zawartości elementu w widoku układu z pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="4cb43-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="4cb43-223">[Widok częściowy](xref:mvc/views/partial) i widok</span><span class="sxs-lookup"><span data-stu-id="4cb43-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="4cb43-224">Widżet wyświetlający dane na podstawie strony sieci Web, której zażądał użytkownik.</span><span class="sxs-lookup"><span data-stu-id="4cb43-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="4cb43-225">Do tej kolekcji można odwoływać się za `ViewData` pomocą `ViewBag` właściwości lub na kontrolerach i w widokach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="4cb43-226">`ViewData`Właściwość jest słownikiem obiektów o nieprawidłowym typie.</span><span class="sxs-lookup"><span data-stu-id="4cb43-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="4cb43-227">`ViewBag`Właściwość to otoka `ViewData` , która udostępnia właściwości dynamiczne dla kolekcji źródłowej `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="4cb43-228">Uwaga: w przypadku wyszukiwania kluczy nie jest rozróżniana wielkość liter w obu `ViewData` i `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="4cb43-229">`ViewData`i `ViewBag` są dynamicznie rozwiązywane w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4cb43-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="4cb43-230">Ponieważ nie oferują sprawdzania typu w czasie kompilacji, obie są zwykle bardziej podatne na błędy niż przy użyciu ViewModel.</span><span class="sxs-lookup"><span data-stu-id="4cb43-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="4cb43-231">Z tego powodu niektórzy deweloperzy wolą do minimalnej lub nigdy nie używać `ViewData` i `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="4cb43-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="4cb43-232">**ViewData**</span></span>

<span data-ttu-id="4cb43-233">`ViewData`jest obiektem [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) dostępnym za pomocą `string` kluczy.</span><span class="sxs-lookup"><span data-stu-id="4cb43-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="4cb43-234">Dane ciągu mogą być przechowywane i używane bezpośrednio bez konieczności rzutowania, ale należy rzutować inne `ViewData` wartości obiektów na określone typy podczas ich wyodrębniania.</span><span class="sxs-lookup"><span data-stu-id="4cb43-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="4cb43-235">Można użyć `ViewData` do przekazywania danych z kontrolerów do widoków i w widokach, w tym [częściowych widoków](xref:mvc/views/partial) i [układów](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="4cb43-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="4cb43-236">Poniżej znajduje się przykład, który ustawia wartości dla powitania i adresu przy użyciu `ViewData` w akcji:</span><span class="sxs-lookup"><span data-stu-id="4cb43-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="4cb43-237">Pracuj z danymi w widoku:</span><span class="sxs-lookup"><span data-stu-id="4cb43-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4cb43-238">**ViewData — atrybut**</span><span class="sxs-lookup"><span data-stu-id="4cb43-238">**ViewData attribute**</span></span>

<span data-ttu-id="4cb43-239">Innym podejściem korzystającym z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) jest [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="4cb43-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="4cb43-240">Właściwości na kontrolerach lub Razor modelach stron oznaczonych `[ViewData]` atrybutem są przechowywane i ładowane ze słownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="4cb43-241">W poniższym przykładzie kontroler Home zawiera `Title` Właściwość oznaczoną przy użyciu `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="4cb43-242">`About`Metoda ustawia tytuł dla widoku informacje:</span><span class="sxs-lookup"><span data-stu-id="4cb43-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="4cb43-243">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="4cb43-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="4cb43-244">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="4cb43-244">**ViewBag**</span></span>

<span data-ttu-id="4cb43-245">`ViewBag`*nie jest dostępny w Razor Strony.*</span><span class="sxs-lookup"><span data-stu-id="4cb43-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="4cb43-246">`ViewBag`jest obiektem [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) , który zapewnia dynamiczny dostęp do obiektów przechowywanych w `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="4cb43-247">`ViewBag`może być wygodniejszy do pracy z, ponieważ nie wymaga rzutowania.</span><span class="sxs-lookup"><span data-stu-id="4cb43-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="4cb43-248">Poniższy przykład pokazuje, jak używać tego `ViewBag` samego wyniku, jak w przypadku użycia `ViewData` powyżej:</span><span class="sxs-lookup"><span data-stu-id="4cb43-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="4cb43-249">**Jednoczesne korzystanie z ViewData i ViewBag**</span><span class="sxs-lookup"><span data-stu-id="4cb43-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="4cb43-250">`ViewBag`*nie jest dostępny w Razor Strony.*</span><span class="sxs-lookup"><span data-stu-id="4cb43-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="4cb43-251">Ponieważ `ViewData` i `ViewBag` zapoznaj się z tą samą `ViewData` kolekcją podstawową, można użyć obu `ViewData` i `ViewBag` i kombinacji między nimi podczas odczytywania i zapisywania wartości.</span><span class="sxs-lookup"><span data-stu-id="4cb43-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="4cb43-252">Ustaw tytuł przy użyciu `ViewBag` i opis przy użyciu `ViewData` w górnej części widoku *Informacje o. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4cb43-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="4cb43-253">Odczytaj właściwości, ale Cofnij użycie `ViewData` i `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="4cb43-254">W pliku *_Layout. cshtml* Uzyskaj tytuł przy użyciu `ViewData` i uzyskaj opis przy użyciu `ViewBag` :</span><span class="sxs-lookup"><span data-stu-id="4cb43-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="4cb43-255">Należy pamiętać, że ciągi nie wymagają rzutowania dla `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="4cb43-256">Można używać `@ViewData["Title"]` bez rzutowania.</span><span class="sxs-lookup"><span data-stu-id="4cb43-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="4cb43-257">Użycie jednocześnie `ViewData` i `ViewBag` w tym samym czasie działa tak, jak mieszanie i odczytywanie właściwości.</span><span class="sxs-lookup"><span data-stu-id="4cb43-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="4cb43-258">Renderowane są następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="4cb43-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="4cb43-259">**Podsumowanie różnic między ViewData i ViewBag**</span><span class="sxs-lookup"><span data-stu-id="4cb43-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="4cb43-260">`ViewBag`nie jest dostępny na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="4cb43-261">Pochodzi z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), więc ma właściwości słownika, które mogą być przydatne, takie jak `ContainsKey` , `Add` , `Remove` , i `Clear` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="4cb43-262">Klucze w słowniku są ciągami, więc odstępy są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="4cb43-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="4cb43-263">Przykład: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="4cb43-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="4cb43-264">Każdy typ inny niż element `string` musi być rzutowany w widoku, który ma być używany `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="4cb43-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="4cb43-265">Pochodzi z [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), dzięki czemu umożliwia tworzenie właściwości dynamicznych przy użyciu notacji kropek ( `@ViewBag.SomeKey = <value or object>` ), a rzutowanie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="4cb43-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="4cb43-266">Składnia `ViewBag` umożliwia szybsze Dodawanie do kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="4cb43-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="4cb43-267">Łatwiejszy do sprawdzenia pod kątem wartości null.</span><span class="sxs-lookup"><span data-stu-id="4cb43-267">Simpler to check for null values.</span></span> <span data-ttu-id="4cb43-268">Przykład: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="4cb43-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="4cb43-269">**Kiedy używać ViewData lub ViewBag**</span><span class="sxs-lookup"><span data-stu-id="4cb43-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="4cb43-270">Obie `ViewData` i `ViewBag` są równie ważnym podejściem do przekazywania małych ilości danych między kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="4cb43-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="4cb43-271">Wybór, który ma być używany, jest oparty na preferencjach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="4cb43-272">Możliwe jest mieszanie i dopasowywanie `ViewData` `ViewBag` obiektów oraz obiekty, jednak kod jest łatwiejszy do odczytania i utrzymania przy użyciu jednego podejścia stosowanego spójnie.</span><span class="sxs-lookup"><span data-stu-id="4cb43-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="4cb43-273">Oba podejścia są dynamicznie rozwiązywane w czasie wykonywania i w ten sposób podatne na błędy środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="4cb43-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="4cb43-274">Niektóre zespoły programistyczne ich nie mają.</span><span class="sxs-lookup"><span data-stu-id="4cb43-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="4cb43-275">Widoki dynamiczne</span><span class="sxs-lookup"><span data-stu-id="4cb43-275">Dynamic views</span></span>

<span data-ttu-id="4cb43-276">Widoki, które nie deklarują typu modelu przy użyciu `@model` , ale mają do nich przekazanie wystąpienie modelu (na przykład `return View(Address);` ), mogą dynamicznie odwoływać się do właściwości wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="4cb43-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="4cb43-277">Ta funkcja oferuje elastyczność, ale nie oferuje funkcji ochrony kompilacji ani technologii IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="4cb43-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="4cb43-278">Jeśli właściwość nie istnieje, generowanie strony sieci Web kończy się niepowodzeniem w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4cb43-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="4cb43-279">Więcej funkcji widoku</span><span class="sxs-lookup"><span data-stu-id="4cb43-279">More view features</span></span>

<span data-ttu-id="4cb43-280">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) ułatwiają dodawanie zachowań po stronie serwera do istniejących tagów HTML.</span><span class="sxs-lookup"><span data-stu-id="4cb43-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="4cb43-281">Korzystanie z pomocników tagów pozwala uniknąć konieczności pisania niestandardowego kodu lub pomocników w widokach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="4cb43-282">Pomocnicy tagów są stosowane jako atrybuty do elementów HTML i są ignorowane przez edytory, które nie mogą ich przetworzyć.</span><span class="sxs-lookup"><span data-stu-id="4cb43-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="4cb43-283">Dzięki temu można edytować i renderować znaczniki widoku w różnych narzędziach.</span><span class="sxs-lookup"><span data-stu-id="4cb43-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="4cb43-284">Generowanie niestandardowego znacznika HTML można osiągnąć za pomocą wielu wbudowanych pomocników HTML.</span><span class="sxs-lookup"><span data-stu-id="4cb43-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="4cb43-285">Bardziej złożona logika interfejsu użytkownika może być obsługiwana przez [składniki widoku](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="4cb43-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="4cb43-286">Składniki widoku zapewniają ten sam SoC, który oferuje kontrolery i widoki.</span><span class="sxs-lookup"><span data-stu-id="4cb43-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="4cb43-287">Mogą wyeliminować potrzebę wykonywania działań i widoków, które zajmują się danymi używanymi przez wspólne elementy interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4cb43-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="4cb43-288">Podobnie jak w przypadku wielu innych aspektów ASP.NET Core, widoki obsługują [iniekcję zależności](xref:fundamentals/dependency-injection), umożliwiając dodawanie usług do [widoków](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4cb43-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
