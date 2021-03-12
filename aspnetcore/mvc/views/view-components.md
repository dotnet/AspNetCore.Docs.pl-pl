---
title: Wyświetl składniki w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak składniki widoku są używane w ASP.NET Core i jak dodawać je do aplikacji.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: 1d0e0da3a5d047d7457e7ca7587c81029e33cb69
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586010"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="0e16e-103">Wyświetl składniki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e16e-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="0e16e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e16e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0e16e-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/view-components/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e16e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="0e16e-106">Składniki widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-106">View components</span></span>

<span data-ttu-id="0e16e-107">Składniki widoku są podobne do widoków częściowych, ale są znacznie bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="0e16e-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="0e16e-108">Składniki widoku nie używają powiązania modelu i są zależne od danych, które są dostępne podczas wywoływania.</span><span class="sxs-lookup"><span data-stu-id="0e16e-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="0e16e-109">Ten artykuł został zapisany przy użyciu kontrolerów i widoków, ale składniki widoku również współpracują ze Razor stronami.</span><span class="sxs-lookup"><span data-stu-id="0e16e-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="0e16e-110">Składnik widoku:</span><span class="sxs-lookup"><span data-stu-id="0e16e-110">A view component:</span></span>

* <span data-ttu-id="0e16e-111">Renderuje fragment, a nie całą odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="0e16e-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="0e16e-112">Obejmuje te same rozbarwienia i korzyści z zakresu możliwości testowania, które znajdują się między kontrolerem i widokiem.</span><span class="sxs-lookup"><span data-stu-id="0e16e-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="0e16e-113">Może mieć parametry i logikę biznesową.</span><span class="sxs-lookup"><span data-stu-id="0e16e-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="0e16e-114">Jest zazwyczaj wywoływany ze strony układu.</span><span class="sxs-lookup"><span data-stu-id="0e16e-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="0e16e-115">Składniki widoku są zamierzone wszędzie tam, gdzie można ponownie używać logiki renderowania, która jest zbyt złożona dla widoku częściowego, na przykład:</span><span class="sxs-lookup"><span data-stu-id="0e16e-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="0e16e-116">Dynamiczne menu nawigacji</span><span class="sxs-lookup"><span data-stu-id="0e16e-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="0e16e-117">Tag Cloud (gdzie wysyła zapytanie do bazy danych)</span><span class="sxs-lookup"><span data-stu-id="0e16e-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="0e16e-118">Panel logowania</span><span class="sxs-lookup"><span data-stu-id="0e16e-118">Login panel</span></span>
* <span data-ttu-id="0e16e-119">Koszyk</span><span class="sxs-lookup"><span data-stu-id="0e16e-119">Shopping cart</span></span>
* <span data-ttu-id="0e16e-120">Ostatnio opublikowane artykuły</span><span class="sxs-lookup"><span data-stu-id="0e16e-120">Recently published articles</span></span>
* <span data-ttu-id="0e16e-121">Zawartość paska bocznego w typowym blogu</span><span class="sxs-lookup"><span data-stu-id="0e16e-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="0e16e-122">Panel logowania, który będzie renderowany na każdej stronie i pokazuje linki do wylogowania lub zalogowania, w zależności od stanu logowania użytkownika</span><span class="sxs-lookup"><span data-stu-id="0e16e-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="0e16e-123">Składnik widoku składa się z dwóch części: klasy (zwykle pochodnej od [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) i wynik zwracanego (zazwyczaj widok).</span><span class="sxs-lookup"><span data-stu-id="0e16e-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="0e16e-124">Podobnie jak kontrolery, składnik widoku może być POCO, ale większość deweloperów chce skorzystać z metod i właściwości dostępnych w wyniku `ViewComponent` .</span><span class="sxs-lookup"><span data-stu-id="0e16e-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="0e16e-125">Biorąc pod uwagę, czy składniki widoku są zgodne ze specyfikacjami aplikacji, Razor zamiast tego Rozważ użycie składników.</span><span class="sxs-lookup"><span data-stu-id="0e16e-125">When considering if view components meet an app's specifications, consider using Razor components instead.</span></span> <span data-ttu-id="0e16e-126">Razor składniki umożliwiają również łączenie znaczników z kodem C# w celu tworzenia jednostek interfejsu użytkownika wielokrotnego użytku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-126">Razor components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="0e16e-127">Razor składniki są przeznaczone do produktywności deweloperów podczas udostępniania logiki interfejsu użytkownika po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="0e16e-127">Razor components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="0e16e-128">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index>.</span><span class="sxs-lookup"><span data-stu-id="0e16e-128">For more information, see <xref:blazor/components/index>.</span></span> <span data-ttu-id="0e16e-129">Aby dowiedzieć się, jak dołączyć Razor składniki do aplikacji MVC lub Razor Pages, zobacz <xref:blazor/components/prerendering-and-integration?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="0e16e-129">For information on how to incorporate Razor components into an MVC or Razor Pages app, see <xref:blazor/components/prerendering-and-integration?pivots=server>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="0e16e-130">Tworzenie składnika widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-130">Creating a view component</span></span>

<span data-ttu-id="0e16e-131">Ta sekcja zawiera wymagania wysokiego poziomu dotyczące tworzenia składnika widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-131">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="0e16e-132">W dalszej części tego artykułu sprawdzimy szczegółowo poszczególne kroki i utworzysz składnik widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-132">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="0e16e-133">Klasa widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-133">The view component class</span></span>

<span data-ttu-id="0e16e-134">Klasę składnika widoku można utworzyć przy użyciu dowolnego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="0e16e-134">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="0e16e-135">Wyprowadzanie z *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="0e16e-135">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="0e16e-136">Dekorowania nazwy klasę z `[ViewComponent]` atrybutem lub pochodną klasy z `[ViewComponent]` atrybutem</span><span class="sxs-lookup"><span data-stu-id="0e16e-136">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="0e16e-137">Tworzenie klasy, w której następuje nazwa z sufiksem *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="0e16e-137">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="0e16e-138">Podobnie jak kontrolery, składniki widoku muszą być publiczne, niezagnieżdżone i nieabstrakcyjne.</span><span class="sxs-lookup"><span data-stu-id="0e16e-138">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="0e16e-139">Nazwa składnika widoku to nazwa klasy z usuniętym sufiksem "ViewComponent".</span><span class="sxs-lookup"><span data-stu-id="0e16e-139">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="0e16e-140">Można go również jawnie określić przy użyciu `ViewComponentAttribute.Name` właściwości.</span><span class="sxs-lookup"><span data-stu-id="0e16e-140">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="0e16e-141">Klasa składnika widoku:</span><span class="sxs-lookup"><span data-stu-id="0e16e-141">A view component class:</span></span>

* <span data-ttu-id="0e16e-142">W pełni obsługuje [iniekcję zależności](../../fundamentals/dependency-injection.md) konstruktora</span><span class="sxs-lookup"><span data-stu-id="0e16e-142">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="0e16e-143">Nie uczestniczy w cyklu życia kontrolera, co oznacza, że nie można używać [filtrów](../controllers/filters.md) w składniku widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-143">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="0e16e-144">Wyświetlanie metod składników</span><span class="sxs-lookup"><span data-stu-id="0e16e-144">View component methods</span></span>

<span data-ttu-id="0e16e-145">Składnik widoku definiuje swoją logikę w `InvokeAsync` metodzie, która zwraca `Task<IViewComponentResult>` lub w `Invoke` metodzie synchronicznej, która zwraca wartość `IViewComponentResult` .</span><span class="sxs-lookup"><span data-stu-id="0e16e-145">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="0e16e-146">Parametry pochodzą bezpośrednio z wywołania składnika widoku, a nie z powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="0e16e-146">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="0e16e-147">Składnik widoku nigdy nie obsługuje bezpośrednio żądania.</span><span class="sxs-lookup"><span data-stu-id="0e16e-147">A view component never directly handles a request.</span></span> <span data-ttu-id="0e16e-148">Zazwyczaj składnik widoku inicjuje model i przekazuje go do widoku przez wywołanie `View` metody.</span><span class="sxs-lookup"><span data-stu-id="0e16e-148">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="0e16e-149">Podsumowując, Wyświetl metody składników:</span><span class="sxs-lookup"><span data-stu-id="0e16e-149">In summary, view component methods:</span></span>

* <span data-ttu-id="0e16e-150">Zdefiniuj `InvokeAsync` metodę zwracającą `Task<IViewComponentResult>` lub metodę synchroniczną `Invoke` , która zwraca obiekt `IViewComponentResult` .</span><span class="sxs-lookup"><span data-stu-id="0e16e-150">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="0e16e-151">Zazwyczaj inicjuje model i przekazuje go do widoku przez wywołanie `ViewComponent` `View` metody.</span><span class="sxs-lookup"><span data-stu-id="0e16e-151">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="0e16e-152">Parametry pochodzą z metody wywołującej, a nie HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e16e-152">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="0e16e-153">Brak powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="0e16e-153">There's no model binding.</span></span>
* <span data-ttu-id="0e16e-154">Nie są dostępne bezpośrednio jako punkt końcowy HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e16e-154">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="0e16e-155">Są wywoływane z kodu (zazwyczaj w widoku).</span><span class="sxs-lookup"><span data-stu-id="0e16e-155">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="0e16e-156">Składnik widoku nigdy nie obsługuje żądania.</span><span class="sxs-lookup"><span data-stu-id="0e16e-156">A view component never handles a request.</span></span>
* <span data-ttu-id="0e16e-157">Są przeciążone w sygnaturze, a nie żadne szczegóły z bieżącego żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e16e-157">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="0e16e-158">Wyświetl ścieżkę wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0e16e-158">View search path</span></span>

<span data-ttu-id="0e16e-159">Środowisko uruchomieniowe wyszukuje widok w następujących ścieżkach:</span><span class="sxs-lookup"><span data-stu-id="0e16e-159">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="0e16e-160">Nazwa/Views/{Controller}/Components/{View nazwa składnika}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="0e16e-160">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="0e16e-161">Nazwa składnika/Views/Shared/Components/{View}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="0e16e-161">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="0e16e-162">Nazwa składnika/Pages/Shared/Components/{View}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="0e16e-162">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="0e16e-163">Ścieżka wyszukiwania ma zastosowanie do projektów korzystających z kontrolerów + widoków i Razor stron.</span><span class="sxs-lookup"><span data-stu-id="0e16e-163">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="0e16e-164">Domyślna nazwa widoku dla składnika widoku jest *Domyślna*, co oznacza, że plik widoku będzie zazwyczaj nazwany *default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e16e-164">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="0e16e-165">Możesz określić inną nazwę widoku podczas tworzenia wyniku składnika widoku lub podczas wywoływania `View` metody.</span><span class="sxs-lookup"><span data-stu-id="0e16e-165">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="0e16e-166">Zalecamy, aby nazwa pliku widoku *default. cshtml* i użyć ścieżki *views/Shared/Components/{View nazwa składnika}/{View Name}* .</span><span class="sxs-lookup"><span data-stu-id="0e16e-166">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="0e16e-167">`PriorityList`Składnik widoku używany w tym przykładzie używa *widoków/Shared/Components/PriorityList/default. cshtml* dla widoku składnika widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-167">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="0e16e-168">Dostosowywanie ścieżki wyszukiwania widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-168">Customize the view search path</span></span>

<span data-ttu-id="0e16e-169">Aby dostosować ścieżkę wyszukiwania widoku, zmodyfikuj Razor <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> kolekcję.</span><span class="sxs-lookup"><span data-stu-id="0e16e-169">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="0e16e-170">Aby na przykład wyszukać widoki w ścieżce "/Components/{View Component Name}/{View Name}", Dodaj nowy element do kolekcji:</span><span class="sxs-lookup"><span data-stu-id="0e16e-170">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="0e16e-171">W powyższym kodzie symbol zastępczy " {0} " reprezentuje ścieżkę "Components/{View Name}/{View Name}".</span><span class="sxs-lookup"><span data-stu-id="0e16e-171">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="0e16e-172">Wywoływanie składnika widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-172">Invoking a view component</span></span>

<span data-ttu-id="0e16e-173">Aby użyć składnika widoku, wywołaj następujące elementy w widoku:</span><span class="sxs-lookup"><span data-stu-id="0e16e-173">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="0e16e-174">Parametry zostaną przesłane do `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="0e16e-174">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="0e16e-175">`PriorityList`Składnik widoku utworzony w artykule jest wywoływany z pliku widoku *widoków/zadania/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0e16e-175">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="0e16e-176">W poniższej tabeli `InvokeAsync` Metoda jest wywoływana z dwoma parametrami:</span><span class="sxs-lookup"><span data-stu-id="0e16e-176">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="0e16e-177">Wywoływanie składnika widoku jako pomocnika tagów</span><span class="sxs-lookup"><span data-stu-id="0e16e-177">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="0e16e-178">W przypadku ASP.NET Core 1,1 i wyższych można wywołać składnik widoku jako [pomocnika tagów](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="0e16e-178">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="0e16e-179">Klasy z wielkością liter w języku Pascal i parametry metody dla pomocników tagów są tłumaczone na ich [Kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="0e16e-179">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="0e16e-180">Pomocnik tagu do wywołania składnika widoku używa `<vc></vc>` elementu.</span><span class="sxs-lookup"><span data-stu-id="0e16e-180">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="0e16e-181">Składnik widoku jest określony w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0e16e-181">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="0e16e-182">Aby użyć składnika widoku jako pomocnika tagów, zarejestruj zestaw zawierający składnik widoku za pomocą `@addTagHelper` dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="0e16e-182">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="0e16e-183">Jeśli składnik widoku znajduje się w zestawie o nazwie `MyWebApp` , Dodaj następującą dyrektywę do pliku *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0e16e-183">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="0e16e-184">Składnik widoku można zarejestrować jako pomocnika tagów do każdego pliku, który odwołuje się do składnika widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-184">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="0e16e-185">Zobacz temat [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) , aby uzyskać więcej informacji na temat rejestrowania pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="0e16e-185">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="0e16e-186">`InvokeAsync`Metoda używana w tym samouczku:</span><span class="sxs-lookup"><span data-stu-id="0e16e-186">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="0e16e-187">Znaczniki pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="0e16e-187">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="0e16e-188">W powyższym przykładzie `PriorityList` składnik widoku zmieni się `priority-list` .</span><span class="sxs-lookup"><span data-stu-id="0e16e-188">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="0e16e-189">Parametry składnika widoku są przenoszone jako atrybuty w przypadku Kebab.</span><span class="sxs-lookup"><span data-stu-id="0e16e-189">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="0e16e-190">Wywoływanie składnika widoku bezpośrednio z kontrolera</span><span class="sxs-lookup"><span data-stu-id="0e16e-190">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="0e16e-191">Składniki widoku są zwykle wywoływane z widoku, ale można je wywołać bezpośrednio z metody kontrolera.</span><span class="sxs-lookup"><span data-stu-id="0e16e-191">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="0e16e-192">Chociaż składniki widoku nie definiują punktów końcowych, takich jak kontrolery, można łatwo zaimplementować akcję kontrolera, która zwraca zawartość `ViewComponentResult` .</span><span class="sxs-lookup"><span data-stu-id="0e16e-192">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="0e16e-193">W tym przykładzie składnik widoku jest wywoływany bezpośrednio z kontrolera:</span><span class="sxs-lookup"><span data-stu-id="0e16e-193">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="0e16e-194">Przewodnik: Tworzenie składnika widoku prostego</span><span class="sxs-lookup"><span data-stu-id="0e16e-194">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="0e16e-195">[Pobierz](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/view-components/sample), skompiluj i przetestuj kod startowy.</span><span class="sxs-lookup"><span data-stu-id="0e16e-195">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="0e16e-196">Jest to prosty projekt z `ToDo` kontrolerem, który wyświetla listę elementów do  wykonania.</span><span class="sxs-lookup"><span data-stu-id="0e16e-196">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Lista zadań do wykonania](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="0e16e-198">Dodaj klasę ViewComponent</span><span class="sxs-lookup"><span data-stu-id="0e16e-198">Add a ViewComponent class</span></span>

<span data-ttu-id="0e16e-199">Utwórz folder *ViewComponents* i Dodaj następującą `PriorityListViewComponent` klasę:</span><span class="sxs-lookup"><span data-stu-id="0e16e-199">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="0e16e-200">Uwagi dotyczące kodu:</span><span class="sxs-lookup"><span data-stu-id="0e16e-200">Notes on the code:</span></span>

* <span data-ttu-id="0e16e-201">Klasy składników widoku mogą być zawarte w **dowolnym** folderze w projekcie.</span><span class="sxs-lookup"><span data-stu-id="0e16e-201">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="0e16e-202">Ponieważ nazwa klasy PriorityList **ViewComponent** kończąca się sufiksem **ViewComponent**, środowisko uruchomieniowe będzie używać ciągu "PriorityList" podczas odwoływania się do składnika klasy z widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-202">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="0e16e-203">Wyjaśnimy, że w dalszej części bardziej szczegółowo.</span><span class="sxs-lookup"><span data-stu-id="0e16e-203">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="0e16e-204">Ten `[ViewComponent]` atrybut może zmienić nazwę używaną do odwoływania się do składnika widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-204">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="0e16e-205">Można na przykład nazwać klasę `XYZ` i zastosować `ViewComponent` atrybut:</span><span class="sxs-lookup"><span data-stu-id="0e16e-205">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="0e16e-206">W `[ViewComponent]` powyższym atrybucie nakazuje selektorowi składnika widoku użycie nazwy `PriorityList` podczas wyszukiwania widoków skojarzonych ze składnikiem i użycie ciągu "PriorityList" podczas odwoływania się do składnika klasy z widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-206">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="0e16e-207">Wyjaśnimy, że w dalszej części bardziej szczegółowo.</span><span class="sxs-lookup"><span data-stu-id="0e16e-207">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="0e16e-208">Składnik używa [iniekcji zależności](../../fundamentals/dependency-injection.md) , aby udostępnić kontekst danych.</span><span class="sxs-lookup"><span data-stu-id="0e16e-208">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="0e16e-209">`InvokeAsync` uwidacznia metodę, która może być wywoływana z widoku i może przyjmować dowolną liczbę argumentów.</span><span class="sxs-lookup"><span data-stu-id="0e16e-209">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="0e16e-210">`InvokeAsync`Metoda zwraca zestaw `ToDo` elementów, które spełniają `isDone` `maxPriority` Parametry i.</span><span class="sxs-lookup"><span data-stu-id="0e16e-210">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="0e16e-211">Tworzenie widoku składnika widoku Razor</span><span class="sxs-lookup"><span data-stu-id="0e16e-211">Create the view component Razor view</span></span>

* <span data-ttu-id="0e16e-212">Utwórz folder *widoki/udostępnione/składniki* .</span><span class="sxs-lookup"><span data-stu-id="0e16e-212">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="0e16e-213">Ten folder **musi** być nazwanymi *składnikami*.</span><span class="sxs-lookup"><span data-stu-id="0e16e-213">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="0e16e-214">Utwórz folder *widoki/udostępnione/składniki/PriorityList* .</span><span class="sxs-lookup"><span data-stu-id="0e16e-214">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="0e16e-215">Nazwa folderu musi być zgodna z nazwą klasy składnika widoku lub nazwą klasy pomniejszonej o sufiks (jeśli została postosowana Konwencja i użyto sufiksu *ViewComponent* w nazwie klasy).</span><span class="sxs-lookup"><span data-stu-id="0e16e-215">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="0e16e-216">Jeśli użyto `ViewComponent` atrybutu, nazwa klasy musi być zgodna z oznaczeniem atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0e16e-216">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="0e16e-217">Utwórz widok *widoki/Shared/Components/PriorityList/default. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="0e16e-217">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="0e16e-218">RazorWidok pobiera listę `TodoItem` i wyświetla je.</span><span class="sxs-lookup"><span data-stu-id="0e16e-218">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="0e16e-219">Jeśli metoda widoku składnika `InvokeAsync` nie przekaże nazwy widoku (jak w naszym przykładzie), *Domyślnie* jest używana jako nazwa widoku według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="0e16e-219">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="0e16e-220">W dalszej części tego samouczka pokażę, jak przekazać nazwę widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-220">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="0e16e-221">Aby zastąpić domyślne style dla określonego kontrolera, Dodaj widok do folderu widoku określonego dla kontrolera (na przykład *widoki/zadania/składniki/PriorityList/default. cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="0e16e-221">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="0e16e-222">Jeśli składnik widoku jest specyficzny dla kontrolera, można go dodać do folderu właściwego dla kontrolera (*widoki/zadania/składniki/PriorityList/default. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="0e16e-222">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="0e16e-223">Dodaj element `div` zawierający wywołanie do składnika listy priorytetu w dolnej części pliku *views/do zrobienia/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0e16e-223">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="0e16e-224">Znacznik `@await Component.InvokeAsync` pokazuje składnię dla wywoływanych składników widoku.</span><span class="sxs-lookup"><span data-stu-id="0e16e-224">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="0e16e-225">Pierwszy argument jest nazwą składnika, który ma zostać wywołany lub wywołany.</span><span class="sxs-lookup"><span data-stu-id="0e16e-225">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="0e16e-226">Kolejne parametry są przesyłane do składnika.</span><span class="sxs-lookup"><span data-stu-id="0e16e-226">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="0e16e-227">`InvokeAsync` może przyjmować dowolną liczbę argumentów.</span><span class="sxs-lookup"><span data-stu-id="0e16e-227">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="0e16e-228">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0e16e-228">Test the app.</span></span> <span data-ttu-id="0e16e-229">Na poniższej ilustracji przedstawiono listę zadań do wykonania i priorytet:</span><span class="sxs-lookup"><span data-stu-id="0e16e-229">The following image shows the ToDo list and the priority items:</span></span>

![Lista zadań do wykonania i priorytetowe elementy](view-components/_static/pi.png)

<span data-ttu-id="0e16e-231">Możesz również wywołać składnik widoku bezpośrednio z poziomu kontrolera:</span><span class="sxs-lookup"><span data-stu-id="0e16e-231">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![priorytet elementów z akcji IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="0e16e-233">Określanie nazwy widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-233">Specifying a view name</span></span>

<span data-ttu-id="0e16e-234">Składnik widoku złożonego może wymagać określenia widoku innego niż domyślny w pewnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="0e16e-234">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="0e16e-235">Poniższy kod ilustruje sposób określania widoku "PVC" z `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="0e16e-235">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="0e16e-236">Zaktualizuj `InvokeAsync` metodę w `PriorityListViewComponent` klasie.</span><span class="sxs-lookup"><span data-stu-id="0e16e-236">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="0e16e-237">Skopiuj plik *views/Shared/Components/PriorityList/default. cshtml* do widoku o nazwie *views/Shared/Components/PriorityList/PVC. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e16e-237">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="0e16e-238">Dodaj nagłówek, aby wskazać, że widok obwodu PVC jest używany.</span><span class="sxs-lookup"><span data-stu-id="0e16e-238">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="0e16e-239">Aktualizowanie *widoków/do zrobienia/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0e16e-239">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="0e16e-240">Uruchom aplikację i sprawdź Widok obwodu PVC.</span><span class="sxs-lookup"><span data-stu-id="0e16e-240">Run the app and verify PVC view.</span></span>

![Składnik widoku priorytetu](view-components/_static/pvc.png)

<span data-ttu-id="0e16e-242">Jeśli widok obwodu PVC nie jest renderowany, sprawdź, czy wywoływany jest składnik widoku o priorytecie 4 lub wyższym.</span><span class="sxs-lookup"><span data-stu-id="0e16e-242">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="0e16e-243">Sprawdzanie ścieżki widoku</span><span class="sxs-lookup"><span data-stu-id="0e16e-243">Examine the view path</span></span>

* <span data-ttu-id="0e16e-244">Zmień wartość parametru Priority na trzy lub mniej, aby widok priorytet nie został zwrócony.</span><span class="sxs-lookup"><span data-stu-id="0e16e-244">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="0e16e-245">Tymczasowe zmiany nazwy *widoków/zadania/składniki/PriorityList/default. cshtml* na *1Default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e16e-245">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="0e16e-246">Przetestuj aplikację, zostanie wyświetlony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="0e16e-246">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="0e16e-247">Kopiuj *widoki/zadania/składniki/PriorityList/1Default. cshtml* do *widoków/Shared/Components/PriorityList/default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e16e-247">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="0e16e-248">Dodaj adiustację do widoku *udostępnionego* składnika widoku do wykonania, aby wskazać, że widok pochodzi z folderu *udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="0e16e-248">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="0e16e-249">Przetestuj widok składnika **współużytkowanego** .</span><span class="sxs-lookup"><span data-stu-id="0e16e-249">Test the **Shared** component view.</span></span>

![Dane wyjściowe zadania z widokiem udostępnionego składnika](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="0e16e-251">Unikanie stałych zakodowanych ciągów</span><span class="sxs-lookup"><span data-stu-id="0e16e-251">Avoiding hard-coded strings</span></span>

<span data-ttu-id="0e16e-252">Jeśli chcesz uzyskać bezpieczeństwo czasu kompilowania, możesz zastąpić ustaloną nazwę składnika widoku nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="0e16e-252">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="0e16e-253">Utwórz składnik widoku bez sufiksu "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="0e16e-253">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="0e16e-254">Dodaj `using` instrukcję do Razor pliku widoku i Użyj `nameof` operatora:</span><span class="sxs-lookup"><span data-stu-id="0e16e-254">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="0e16e-255">Wykonywanie synchronicznej pracy</span><span class="sxs-lookup"><span data-stu-id="0e16e-255">Perform synchronous work</span></span>

<span data-ttu-id="0e16e-256">Struktura obsługuje wywoływanie metody synchronicznej, `Invoke` Jeśli nie trzeba wykonywać operacji asynchronicznej.</span><span class="sxs-lookup"><span data-stu-id="0e16e-256">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="0e16e-257">W poniższej metodzie jest tworzony `Invoke` składnik widoku synchronicznego:</span><span class="sxs-lookup"><span data-stu-id="0e16e-257">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="0e16e-258">Plik składnika widoku Razor zawiera listę ciągów przekazaną do `Invoke` metody (*przegląda/Home/Components/PriorityList/default. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="0e16e-258">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="0e16e-259">Składnik widoku jest wywoływany w Razor pliku (na przykład *widoki/Home/index. cshtml*) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0e16e-259">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="0e16e-260">Pomocnik tagów</span><span class="sxs-lookup"><span data-stu-id="0e16e-260">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="0e16e-261">Aby użyć <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> podejścia, wywołaj `Component.InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="0e16e-261">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="0e16e-262">Składnik widoku jest wywoływany w Razor pliku (na przykład *przeglądas/Home/index. cshtml*) z <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="0e16e-262">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="0e16e-263">Wywołanie `Component.InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="0e16e-263">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="0e16e-264">Aby użyć pomocnika tagów, zarejestruj zestaw zawierający składnik widoku przy użyciu `@addTagHelper` dyrektywy (składnik widoku znajduje się w zestawie o nazwie `MyWebApp` ):</span><span class="sxs-lookup"><span data-stu-id="0e16e-264">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="0e16e-265">Użyj pomocnika tagu składnika w Razor pliku znaczników:</span><span class="sxs-lookup"><span data-stu-id="0e16e-265">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="0e16e-266">Sygnatura metody `PriorityList.Invoke` jest synchroniczna, ale Razor znajduje i wywołuje metodę z `Component.InvokeAsync` w pliku znaczników.</span><span class="sxs-lookup"><span data-stu-id="0e16e-266">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="0e16e-267">Wszystkie parametry składnika widoku są wymagane</span><span class="sxs-lookup"><span data-stu-id="0e16e-267">All view component parameters are required</span></span>

<span data-ttu-id="0e16e-268">Każdy parametr w składniku widoku jest atrybutem wymaganym.</span><span class="sxs-lookup"><span data-stu-id="0e16e-268">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="0e16e-269">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5011)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="0e16e-269">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="0e16e-270">Jeśli dowolny parametr zostanie pominięty:</span><span class="sxs-lookup"><span data-stu-id="0e16e-270">If any  parameter is omitted:</span></span>

* <span data-ttu-id="0e16e-271">`InvokeAsync`Sygnatura metody nie jest zgodna, dlatego metoda nie zostanie wykonana.</span><span class="sxs-lookup"><span data-stu-id="0e16e-271">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="0e16e-272">ViewComponent nie renderuje żadnych znaczników.</span><span class="sxs-lookup"><span data-stu-id="0e16e-272">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="0e16e-273">Nie zostaną zgłoszone żadne błędy.</span><span class="sxs-lookup"><span data-stu-id="0e16e-273">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e16e-274">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0e16e-274">Additional resources</span></span>

* [<span data-ttu-id="0e16e-275">Wstrzykiwanie zależności do widoków</span><span class="sxs-lookup"><span data-stu-id="0e16e-275">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
