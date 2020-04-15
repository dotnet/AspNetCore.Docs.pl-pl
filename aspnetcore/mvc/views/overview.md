---
title: Widoki w ASP.NET Core MVC
author: ardalis
description: Dowiedz się, jak widoki obsługują prezentację danych aplikacji i interakcję użytkownika w ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: 70b8c2c01a28f99dd384351041a3b77d23f46a48
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384070"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="a4e5a-103">Widoki w ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a4e5a-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="a4e5a-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a4e5a-105">W tym dokumencie wyjaśniono widoki używane w ASP.NET podstawowych aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="a4e5a-106">Aby uzyskać informacje na temat stron Razor, zobacz [Wprowadzenie do stron Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="a4e5a-107">W wzorcu model-view-controller (MVC) *widok* obsługuje prezentacji danych aplikacji i interakcji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="a4e5a-108">Widok jest szablonem HTML z osadzonym [znacznikiem Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="a4e5a-109">Znacznik maszynki do golenia to kod, który współdziała z znacznikami HTML w celu stworzenia strony sieci Web, która jest wysyłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="a4e5a-110">W ASP.NET Core MVC widoki są plikami *cshtml,* które używają [języka programowania C#](/dotnet/csharp/) w znacznikach Razor.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="a4e5a-111">Zazwyczaj pliki widoku są pogrupowane w foldery nazwane dla każdego [z kontrolerów](xref:mvc/controllers/actions)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="a4e5a-112">Foldery są przechowywane w folderze *Widoki* w katalogu głównym aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Folder Widoki w Eksploratorze rozwiązań programu Visual Studio jest otwarty z otwartym folderem Strona główna, aby wyświetlić pliki About.cshtml, Contact.cshtml i Index.cshtml](overview/_static/views_solution_explorer.png)

<span data-ttu-id="a4e5a-114">Kontroler *macierzysty* jest reprezentowany przez folder *home* wewnątrz folderu *Widoki.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="a4e5a-115">Folder *Strona główna* zawiera widoki stron sieci Web *Informacje,* *Kontakt*i *Indeks* (strona główna).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="a4e5a-116">Gdy użytkownik żąda jednej z tych trzech stron sieci Web, akcje kontrolera w kontrolerze *macierzystym* określają, który z trzech widoków jest używany do tworzenia i zwracania strony sieci Web do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="a4e5a-117">Układy umożliwiają [spójne](xref:mvc/views/layout) sekcje stron sieci Web i zmniejszają powtarzanie kodu.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="a4e5a-118">Układy często zawierają elementy nagłówka, nawigacji i menu oraz stopkę.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="a4e5a-119">Nagłówek i stopka zwykle zawierają znaczniki standardowego dla wielu elementów metadanych i łącza do zasobów skryptu i stylu.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="a4e5a-120">Układy pomagają uniknąć tego znaczników kotłowych w widokach.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="a4e5a-121">[Widoki częściowe](xref:mvc/views/partial) zmniejszają powielanie kodu przez zarządzanie częściami widoków wielokrotnegoużytnia.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="a4e5a-122">Na przykład widok częściowy jest przydatny w przypadku biografii autora w witrynie sieci Web bloga, która pojawia się w kilku widokach.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="a4e5a-123">Biografia autora jest zwykłą zawartością widoku i nie wymaga kodu do wykonania w celu wytworzenia zawartości dla strony internetowej.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="a4e5a-124">Zawartość biografii autora jest dostępna dla widoku przez sam powiązanie modelu, więc użycie widoku częściowego dla tego typu zawartości jest idealne.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="a4e5a-125">[Składniki widoku](xref:mvc/views/view-components) są podobne do widoków częściowych, ponieważ umożliwiają zmniejszenie powtarzającego się kodu, ale są odpowiednie dla zawartości widoku, która wymaga uruchomienia kodu na serwerze w celu renderowania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="a4e5a-126">Wyświetl składniki są przydatne, gdy renderowana zawartość wymaga interakcji z bazą danych, na przykład dla koszyka witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="a4e5a-127">Wyświetl składniki nie są ograniczone do powiązania modelu w celu uzyskania danych wyjściowych strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="a4e5a-128">Korzyści z korzystania z widoków</span><span class="sxs-lookup"><span data-stu-id="a4e5a-128">Benefits of using views</span></span>

<span data-ttu-id="a4e5a-129">Widoki pomagają ustalić [oddzielenie problemów](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) w aplikacji MVC, oddzielając znaczniki interfejsu użytkownika od innych części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="a4e5a-130">Zgodnie z projektem SoC sprawia, że aplikacja jest modułowa, co zapewnia kilka korzyści:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="a4e5a-131">Aplikacja jest łatwiejsza w utrzymaniu, ponieważ jest lepiej zorganizowana.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="a4e5a-132">Widoki są zazwyczaj pogrupowane według funkcji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="a4e5a-133">Ułatwia to znajdowanie powiązanych widoków podczas pracy nad funkcją.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="a4e5a-134">Części aplikacji są luźno sprzężone.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="a4e5a-135">Widoki aplikacji można tworzyć i aktualizować niezależnie od składników logiki biznesowej i dostępu do danych.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="a4e5a-136">Można modyfikować widoki aplikacji bez konieczności aktualizowania innych części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="a4e5a-137">Łatwiej jest przetestować części interfejsu użytkownika aplikacji, ponieważ widoki są oddzielne jednostki.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="a4e5a-138">Ze względu na lepszą organizację jest mniej prawdopodobne, że przypadkowo powtórzysz sekcje interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="a4e5a-139">Tworzenie widoku</span><span class="sxs-lookup"><span data-stu-id="a4e5a-139">Creating a view</span></span>

<span data-ttu-id="a4e5a-140">Widoki specyficzne dla kontrolera są tworzone w folderze *Widoki/[Nazwa kontrolera].*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="a4e5a-141">Widoki współużytkowane przez kontrolery są umieszczane w folderze *Widoki/Udostępnione.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="a4e5a-142">Aby utworzyć widok, dodaj nowy plik i nadaj mu taką samą nazwę jak skojarzona z nim akcja kontrolera z rozszerzeniem pliku *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="a4e5a-143">Aby utworzyć widok odpowiadający akcji *Informacje* na kontrolerze *macierzystym,* utwórz plik *About.cshtml* w folderze *Widoki/Strona główna:*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="a4e5a-144">*Znacznik brzytwy* zaczyna `@` się od symbolu.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="a4e5a-145">Uruchom instrukcje C#, umieszczając kod C# w [blokach kodu Razor](xref:mvc/views/razor#razor-code-blocks) ustawionych przez nawiasy klamrowe (`{ ... }`).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="a4e5a-146">Na przykład zobacz przypisanie "Informacje", aby `ViewData["Title"]` pokazano powyżej.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="a4e5a-147">Wartości w formacie HTML można wyświetlać, `@` po prostu odwołując się do wartości za pomocą symbolu.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="a4e5a-148">Zobacz zawartość `<h2>` i `<h3>` elementy powyżej.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="a4e5a-149">Zawartość widoku pokazana powyżej jest tylko częścią całej strony sieci Web, która jest renderowana dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="a4e5a-150">Pozostałe elementy układu strony i inne typowe aspekty widoku są określone w innych plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="a4e5a-151">Aby dowiedzieć się więcej, zobacz [temat Układ](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="a4e5a-152">Jak kontrolerzy określają widoki</span><span class="sxs-lookup"><span data-stu-id="a4e5a-152">How controllers specify views</span></span>

<span data-ttu-id="a4e5a-153">Widoki są zazwyczaj zwracane z akcji jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), który jest typem [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="a4e5a-154">Metoda akcji można utworzyć `ViewResult` i zwrócić bezpośrednio, ale to nie jest powszechnie wykonywane.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="a4e5a-155">Ponieważ większość kontrolerów dziedziczy po [kontrolerze,](/dotnet/api/microsoft.aspnetcore.mvc.controller)wystarczy użyć metody `View` pomocnika, aby zwrócić `ViewResult`:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="a4e5a-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="a4e5a-157">Gdy ta akcja powróci, widok *About.cshtml* wyświetlany w ostatniej sekcji jest renderowany jako następująca strona sieci Web:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Strona wyrenderowana w przeglądarce Edge — informacje](overview/_static/about-page.png)

<span data-ttu-id="a4e5a-159">Metoda `View` pomocnika ma kilka przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="a4e5a-160">Opcjonalnie można określić:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-160">You can optionally specify:</span></span>

* <span data-ttu-id="a4e5a-161">Jawny widok do zwrócenia:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="a4e5a-162">[Model,](xref:mvc/models/model-binding) który ma przejść do widoku:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="a4e5a-163">Zarówno widok, jak i model:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="a4e5a-164">Wyświetlanie odnajdowania</span><span class="sxs-lookup"><span data-stu-id="a4e5a-164">View discovery</span></span>

<span data-ttu-id="a4e5a-165">Gdy akcja zwraca widok, odbywa się proces o nazwie *odnajdowanie widoku.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="a4e5a-166">Ten proces określa, który plik widoku jest używany na podstawie nazwy widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="a4e5a-167">Domyślnym zachowaniem `View` metody`return View();`( ) jest zwrócenie widoku o takiej samej nazwie jak metoda akcji, z której jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="a4e5a-168">Na przykład nazwa metody *Informacje* `ActionResult` kontrolera służy do wyszukiwania pliku widoku o nazwie *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="a4e5a-169">Najpierw środowisko wykonawcze szuka w *views/[ControllerName]* folderu dla widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="a4e5a-170">Jeśli nie znajdzie tam pasującego widoku, przeszukuje widok w folderze *Udostępniony.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="a4e5a-171">Nie ma znaczenia, czy niejawnie `ViewResult` zwracasz nazwę widoku `return View();` z `View` lub `return View("<ViewName>");`jawnie przekazujesz do metody za pomocą .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="a4e5a-172">W obu przypadkach widok odnajdywania wyszukuje pasujący plik widoku w tej kolejności:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="a4e5a-173">*Widoki/\[Nazwa kontrolera]/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="a4e5a-174">*Widoki/Udostępnione/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="a4e5a-175">Zamiast nazwy widoku można podać ścieżkę pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="a4e5a-176">Jeśli używasz ścieżki bezwzględnej rozpoczynającej się w katalogu głównym aplikacji (opcjonalnie zaczynając od "/" lub "~/"), należy określić rozszerzenie *.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="a4e5a-177">Można również użyć ścieżki względnej, aby określić widoki w różnych katalogach bez rozszerzenia *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="a4e5a-178">Wewnątrz `HomeController`widoku , można zwrócić widok *indeksu* widoków *zarządzania* ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="a4e5a-179">Podobnie można wskazać bieżący katalog specyficzny dla kontrolera za pomocą prefiksu "./":</span><span class="sxs-lookup"><span data-stu-id="a4e5a-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="a4e5a-180">[Widoki częściowe](xref:mvc/views/partial) i [komponenty widoku](xref:mvc/views/view-components) używają podobnych (ale nie identycznych) mechanizmów odnajdowania.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="a4e5a-181">Domyślną konwencję można dostosować do sposobu, w jaki widoki znajdują się w aplikacji, korzystając z niestandardowego [programu IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="a4e5a-182">Odnajdowanie widoku zależy od znajdowania plików widoku według nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="a4e5a-183">Jeśli w podstawowym systemie plików rozróżniana jest wielkość liter, w nazwach widoku prawdopodobnie rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="a4e5a-184">Aby uzyskać zgodność między systemami operacyjnymi, dopasuj przypadek między nazwami kontrolerów i akcji oraz skojarzonymi folderami widoku i nazwami plików.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="a4e5a-185">Jeśli wystąpi błąd, że plik widoku nie można znaleźć podczas pracy z systemem plików z uwzględnieniem wielkości liter, upewnij się, że wielkość liter jest zgodna między żądanym plikiem widoku a rzeczywistą nazwą pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="a4e5a-186">Postępuj zgodnie z najlepszymi praktykami organizowania struktury plików dla widoków, aby odzwierciedlić relacje między kontrolerami, akcjami i widokami, aby zapewnić łatwość konserwacji i przejrzystość.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="a4e5a-187">Przekazywanie danych do widoków</span><span class="sxs-lookup"><span data-stu-id="a4e5a-187">Passing data to views</span></span>

<span data-ttu-id="a4e5a-188">Przekazywanie danych do widoków przy użyciu kilku metod:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="a4e5a-189">Silnie wpisane dane: viewmodel</span><span class="sxs-lookup"><span data-stu-id="a4e5a-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="a4e5a-190">Słabo wpisane dane</span><span class="sxs-lookup"><span data-stu-id="a4e5a-190">Weakly typed data</span></span>
  * <span data-ttu-id="a4e5a-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="a4e5a-192">Silnie wpisane dane (viewmodel)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="a4e5a-193">Najbardziej niezawodne podejście jest określenie typu [modelu](xref:mvc/models/model-binding) w widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="a4e5a-194">Ten model jest powszechnie określany jako *model widoku*.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="a4e5a-195">Można przekazać wystąpienie typu viewmodel do widoku z akcji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="a4e5a-196">Za pomocą modelu widoku do przekazywania danych do widoku umożliwia widok, aby skorzystać z *silnego* sprawdzania typu.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="a4e5a-197">*Silne wpisywanie* (lub *silnie wpisane)* oznacza, że każda zmienna `string`i `int`stała `DateTime`ma wyraźnie zdefiniowany typ (na przykład , , lub ).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="a4e5a-198">Ważność typów używanych w widoku jest sprawdzana w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="a4e5a-199">[Visual Studio](https://visualstudio.microsoft.com) i [Visual Studio Code](https://code.visualstudio.com/) listy silnie wpisanych członków klasy za pomocą funkcji o nazwie [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="a4e5a-200">Aby wyświetlić właściwości systemu viewmodel, wpisz nazwę zmiennej dla asetu,`.`po którym następuje kropka ( ).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="a4e5a-201">Pomaga to pisać kod szybciej przy mniejszej liczbie błędów.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="a4e5a-202">Określ model `@model` przy użyciu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="a4e5a-203">Użyj modelu `@Model`z:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="a4e5a-204">Aby udostępnić model do widoku, kontroler przekazuje go jako parametr:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

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

<span data-ttu-id="a4e5a-205">Nie ma żadnych ograniczeń dotyczących typów modeli, które można podać do widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="a4e5a-206">Zaleca się używanie zwykłych starych obiektów CLR (POCO) z małym lub żadnym zachowaniem (metody) zdefiniowane.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="a4e5a-207">Zazwyczaj viewmodel klasy są przechowywane w *models* folderu lub oddzielny *viewmodels* folderu w katalogu głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="a4e5a-208">System viewmodel *adresu* używany w powyższym przykładzie to program widoku POCO przechowywany w pliku o nazwie *Address.cs:*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

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

<span data-ttu-id="a4e5a-209">Nic nie stoi na przeszkodzie, aby używać tych samych klas zarówno dla typów modelu widoku, jak i typów modeli biznesowych.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="a4e5a-210">Jednak przy użyciu oddzielnych modeli umożliwia widoki różnią się niezależnie od logiki biznesowej i części dostępu do danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="a4e5a-211">Separacja modeli i viewmodels oferuje również korzyści zabezpieczeń, gdy modele używają [powiązania modelu](xref:mvc/models/model-binding) i [sprawdzania poprawności](xref:mvc/models/validation) danych wysyłanych do aplikacji przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="a4e5a-212">Słabo wpisane dane (ViewData, ViewData i ViewBag)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="a4e5a-213">`ViewBag`*nie jest dostępna w aplikacji Razor Pages.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="a4e5a-214">Oprócz widoków silnie typizowanych widoki mają dostęp do *słabo wpisanego* (nazywanego również *luźno wpisanym)* zbioru danych.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="a4e5a-215">W przeciwieństwie do typów *silnych, słabe typy* (lub *typy luźne)* oznacza, że nie jawnie zadeklarować typ danych, których używasz.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="a4e5a-216">Można użyć kolekcji słabo wpisanych danych do przekazywania niewielkich ilości danych do i z kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="a4e5a-217">Przekazywanie danych między ...</span><span class="sxs-lookup"><span data-stu-id="a4e5a-217">Passing data between a ...</span></span>                        | <span data-ttu-id="a4e5a-218">Przykład</span><span class="sxs-lookup"><span data-stu-id="a4e5a-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="a4e5a-219">Kontroler i widok</span><span class="sxs-lookup"><span data-stu-id="a4e5a-219">Controller and a view</span></span>                             | <span data-ttu-id="a4e5a-220">Wypełnianie listy rozwijanej danymi.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="a4e5a-221">Widok i [widok układu](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="a4e5a-222">Ustawienie \*\* \<tytułu>\*\* zawartości elementu w widoku układu z pliku widoku.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="a4e5a-223">[Widok częściowy](xref:mvc/views/partial) i widok</span><span class="sxs-lookup"><span data-stu-id="a4e5a-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="a4e5a-224">Widżet wyświetlany na podstawie strony sieci Web żądanej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="a4e5a-225">Do tej kolekcji można odwoływać się za pośrednictwem `ViewData` właściwości lub `ViewBag` na kontrolerach i widokach.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="a4e5a-226">Właściwość `ViewData` jest słownikiem słabo wpisanych obiektów.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="a4e5a-227">Właściwość `ViewBag` jest otoka `ViewData` wokół, która zapewnia `ViewData` właściwości dynamiczne dla podstawowej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="a4e5a-228">Uwaga: Wyszukiwania klawiszy są niewrażliwe na `ViewData` te `ViewBag`argumenty zarówno dla i .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="a4e5a-229">`ViewData`i `ViewBag` są dynamicznie rozwiązywane w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="a4e5a-230">Ponieważ nie oferują one sprawdzanie typu kompilacji czasu, oba są na ogół bardziej podatne na błędy niż przy użyciu viewmodel.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="a4e5a-231">Z tego powodu niektórzy deweloperzy wolą `ViewData` minimalnie lub nigdy nie używać i `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="a4e5a-232">**ViewData (Wyświetl dane)**</span><span class="sxs-lookup"><span data-stu-id="a4e5a-232">**ViewData**</span></span>

<span data-ttu-id="a4e5a-233">`ViewData`jest [obiektem ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) `string` dostępnym za pośrednictwem kluczy.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="a4e5a-234">Dane ciągu mogą być przechowywane i używane bezpośrednio bez konieczności rzutnia, ale należy rzutować inne `ViewData` wartości obiektów do określonych typów podczas ich wyodrębniania.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="a4e5a-235">Za pomocą `ViewData` funkcji przekazywania danych z kontrolerów do widoków i widoków, w tym [widoków częściowych](xref:mvc/views/partial) i [układów.](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="a4e5a-236">Oto przykład, który ustawia wartości powitania i `ViewData` adresu przy użyciu w akcji:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

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

<span data-ttu-id="a4e5a-237">Praca z danymi w widoku:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-237">Work with the data in a view:</span></span>

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

<span data-ttu-id="a4e5a-238">**Atrybut ViewData**</span><span class="sxs-lookup"><span data-stu-id="a4e5a-238">**ViewData attribute**</span></span>

<span data-ttu-id="a4e5a-239">Innym podejściem, które używa [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) jest [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="a4e5a-240">Właściwości na kontrolerach lub razor Page `[ViewData]` modele oznaczone atrybutem mają swoje wartości przechowywane i ładowane ze słownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="a4e5a-241">W poniższym przykładzie kontroler `Title` home zawiera `[ViewData]`właściwość oznaczoną symbolem .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="a4e5a-242">Metoda `About` ustawia tytuł dla widoku Informacje:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-242">The `About` method sets the title for the About view:</span></span>

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

<span data-ttu-id="a4e5a-243">W układzie tytuł jest odczytywany ze słownika ViewData:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="a4e5a-244">**Torba ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a4e5a-244">**ViewBag**</span></span>

<span data-ttu-id="a4e5a-245">`ViewBag`*nie jest dostępna w aplikacji Razor Pages.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="a4e5a-246">`ViewBag`jest obiektem [DynamicViewData,](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) który zapewnia dynamiczny `ViewData`dostęp do obiektów przechowywanych w programie .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="a4e5a-247">`ViewBag`może być wygodniejszy w pracy, ponieważ nie wymaga odlewania.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="a4e5a-248">W poniższym przykładzie `ViewBag` pokazano, jak `ViewData` używać z tym samym wynikiem, co przy użyciu powyżej:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

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

<span data-ttu-id="a4e5a-249">**Jednoczesne korzystanie z viewdata i ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a4e5a-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="a4e5a-250">`ViewBag`*nie jest dostępna w aplikacji Razor Pages.*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="a4e5a-251">Ponieważ `ViewData` `ViewBag` i odnoszą się `ViewData` do tej samej `ViewBag` kolekcji podstawowej, można użyć i `ViewData` mieszać i dopasowywać między nimi podczas odczytu i pisania wartości.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="a4e5a-252">Ustaw użycie `ViewBag` tytułu i `ViewData` opisu w górnej części widoku *About.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a4e5a-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="a4e5a-253">Odczytuj właściwości, ale `ViewData` odwróć użycie i `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="a4e5a-254">W pliku *_Layout.cshtml,* uzyskać tytuł `ViewData` za pomocą i `ViewBag`uzyskać opis za pomocą :</span><span class="sxs-lookup"><span data-stu-id="a4e5a-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="a4e5a-255">Pamiętaj, że ciągi nie wymagają `ViewData`rzutu dla .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="a4e5a-256">Można używać `@ViewData["Title"]` bez odlewania.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="a4e5a-257">Korzystanie `ViewData` zarówno `ViewBag` i w tym samym czasie działa, podobnie jak mieszanie i dopasowywanie odczytu i pisania właściwości.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="a4e5a-258">Renderowane są następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="a4e5a-259">**Podsumowanie różnic między ViewData i ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a4e5a-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="a4e5a-260">`ViewBag`nie jest dostępna na stronach Razor.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="a4e5a-261">Pochodzi z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), więc ma właściwości słownika, które `ContainsKey` `Add`mogą `Remove`być `Clear`przydatne, takie jak , , i .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="a4e5a-262">Klucze w słowniku są ciągami znaków, więc odstępy są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="a4e5a-263">Przykład: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="a4e5a-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="a4e5a-264">Każdy typ inny `string` niż a musi być `ViewData`rzutowy w widoku, aby użyć .</span><span class="sxs-lookup"><span data-stu-id="a4e5a-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="a4e5a-265">Wywodzi się z [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), dzięki czemu umożliwia tworzenie`@ViewBag.SomeKey = <value or object>`właściwości dynamicznych przy użyciu notacji kropkowej ( ), a rzutowanie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="a4e5a-266">Składnia `ViewBag` sprawia, że szybciej dodać do kontrolerów i widoków.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="a4e5a-267">Prostsze, aby sprawdzić wartości null.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-267">Simpler to check for null values.</span></span> <span data-ttu-id="a4e5a-268">Przykład: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="a4e5a-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="a4e5a-269">**Kiedy używać ViewData lub ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a4e5a-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="a4e5a-270">Oba `ViewData` `ViewBag` i są równie prawidłowe podejścia do przekazywania niewielkich ilości danych między kontrolerami i widoków.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="a4e5a-271">Wybór, którego z nich użyć, zależy od preferencji.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="a4e5a-272">Można mieszać `ViewData` i `ViewBag` dopasowywać i obiekty, jednak kod jest łatwiejsze do odczytania i utrzymania z jednego podejścia używane konsekwentnie.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="a4e5a-273">Oba podejścia są dynamicznie rozpoznawane w czasie wykonywania, a tym samym podatne na wywoływanie błędów środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="a4e5a-274">Niektóre zespoły programistyczne ich unikają.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="a4e5a-275">Widoki dynamiczne</span><span class="sxs-lookup"><span data-stu-id="a4e5a-275">Dynamic views</span></span>

<span data-ttu-id="a4e5a-276">Widoki, które nie deklarują typu `@model` modelu przy użyciu, ale które mają `return View(Address);`wystąpienie modelu przekazywane do nich (na przykład ) można odwoływać się do właściwości wystąpienia dynamicznie:</span><span class="sxs-lookup"><span data-stu-id="a4e5a-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="a4e5a-277">Ta funkcja oferuje elastyczność, ale nie oferuje ochrony kompilacji ani IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="a4e5a-278">Jeśli właściwość nie istnieje, generowanie strony sieci Web kończy się niepowodzeniem w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="a4e5a-279">Więcej funkcji widoku</span><span class="sxs-lookup"><span data-stu-id="a4e5a-279">More view features</span></span>

<span data-ttu-id="a4e5a-280">[Pomocnik tagów](xref:mvc/views/tag-helpers/intro) ułatwia dodawanie zachowania po stronie serwera do istniejących tagów HTML.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="a4e5a-281">Korzystanie z Pomocników tagów pozwala uniknąć konieczności pisania niestandardowego kodu lub pomocników w widokach.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="a4e5a-282">Pomocników tagów są stosowane jako atrybuty do elementów HTML i są ignorowane przez edytory, które nie mogą ich przetworzyć.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="a4e5a-283">Dzięki temu można edytować i renderować znaczniki widoku w różnych narzędziach.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="a4e5a-284">Generowanie niestandardowych znaczników HTML można osiągnąć za pomocą wielu wbudowanych pomocników HTML.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="a4e5a-285">Bardziej złożona logika interfejsu użytkownika może być obsługiwana przez [składniki widoku](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="a4e5a-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="a4e5a-286">Składniki widoku zapewniają ten sam SoC, który oferują kontrolery i widoki.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="a4e5a-287">Mogą one wyeliminować potrzebę akcji i widoków, które dotyczą danych używanych przez typowe elementy interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4e5a-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="a4e5a-288">Podobnie jak wiele innych aspektów ASP.NET Core, widoki obsługują [iniekcję zależności,](xref:fundamentals/dependency-injection)umożliwiając usługi, które mają być [wstrzykiwane do widoków.](xref:mvc/views/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a4e5a-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
