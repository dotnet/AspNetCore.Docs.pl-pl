---
title: Filtry w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak działają filtry i jak korzystać z nich w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 407583533939ec1077af8e1a1511ed187ef9de69
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103005"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="d26c3-103">Filtry w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d26c3-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d26c3-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d26c3-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d26c3-105">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="d26c3-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="d26c3-106">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="d26c3-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="d26c3-107">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="d26c3-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="d26c3-108">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="d26c3-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="d26c3-109">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="d26c3-110">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="d26c3-111">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="d26c3-112">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="d26c3-113">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="d26c3-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="d26c3-114">Filtry nie działają bezpośrednio ze [ Razor składnikami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="d26c3-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="d26c3-115">Filtr może mieć tylko pośredni wpływ na składnik, gdy:</span><span class="sxs-lookup"><span data-stu-id="d26c3-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="d26c3-116">Składnik jest osadzony na stronie lub widoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="d26c3-117">Strona lub kontroler/widok używają filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="d26c3-118">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d26c3-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="d26c3-119">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="d26c3-119">How filters work</span></span>

<span data-ttu-id="d26c3-120">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="d26c3-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="d26c3-121">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok akcji wywołania.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="d26c3-124">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="d26c3-124">Filter types</span></span>

<span data-ttu-id="d26c3-125">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="d26c3-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="d26c3-126">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="d26c3-127">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie nie jest autoryzowane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="d26c3-128">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="d26c3-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="d26c3-129">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-129">Run after authorization.</span></span>  
  * <span data-ttu-id="d26c3-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>uruchamia kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="d26c3-131">Na przykład `OnResourceExecuting` uruchamia kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="d26c3-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>uruchamia kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="d26c3-133">[Filtry akcji](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="d26c3-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="d26c3-134">Uruchom kod bezpośrednio przed i po wywołaniu metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="d26c3-135">Może zmienić argumenty przekazane do akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="d26c3-136">Można zmienić wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="d26c3-137">**Nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="d26c3-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="d26c3-138">[Filtry wyjątków](#exception-filters) stosują zasady globalne do nieobsłużonych wyjątków, które występują przed zapisaniem treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d26c3-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="d26c3-139">[Filtry wyników](#result-filters) uruchamiają kod bezpośrednio przed i po wykonaniu wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="d26c3-140">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="d26c3-141">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="d26c3-142">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="d26c3-145">Implementacja</span><span class="sxs-lookup"><span data-stu-id="d26c3-145">Implementation</span></span>

<span data-ttu-id="d26c3-146">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="d26c3-147">Filtry synchroniczne uruchamiają kod przed i po fazie potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="d26c3-148">Na przykład, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="d26c3-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d26c3-150">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="d26c3-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="d26c3-151">Na przykład <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="d26c3-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="d26c3-152">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="d26c3-153">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="d26c3-153">Multiple filter stages</span></span>

<span data-ttu-id="d26c3-154">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="d26c3-155">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje:</span><span class="sxs-lookup"><span data-stu-id="d26c3-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="d26c3-156">Synchroniczne: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> i<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="d26c3-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="d26c3-157">Asynchroniczny: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> i<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d26c3-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="d26c3-158">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d26c3-159">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="d26c3-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d26c3-160">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d26c3-161">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="d26c3-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="d26c3-162">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , Zastąp tylko metody synchroniczne lub metodę asynchroniczną dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="d26c3-163">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-163">Built-in filter attributes</span></span>

<span data-ttu-id="d26c3-164">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="d26c3-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="d26c3-165">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="d26c3-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-166">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="d26c3-167">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="d26c3-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="d26c3-168">Użyj narzędzia, takiego jak [Narzędzia deweloperskie przeglądarki](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="d26c3-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="d26c3-169">W obszarze **nagłówki odpowiedzi** `author: Rick Anderson` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="d26c3-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="d26c3-170">Poniższy kod implementuje `ActionFilterAttribute` , że:</span><span class="sxs-lookup"><span data-stu-id="d26c3-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="d26c3-171">Odczytuje tytuł i nazwę z systemu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="d26c3-172">W przeciwieństwie do poprzedniego przykładu, poniższy kod nie wymaga dodania parametrów filtru do kodu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="d26c3-173">Dodaje tytuł i nazwę do nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d26c3-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-174">Opcje konfiguracji są dostępne z [systemu konfiguracji](xref:fundamentals/configuration/index) przy użyciu [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d26c3-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d26c3-175">Na przykład, z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="d26c3-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="d26c3-176">W `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="d26c3-177">`PositionOptions`Klasa jest dodawana do kontenera usługi z `"Position"` obszarem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="d26c3-178">`MyActionFilterAttribute`Zostanie dodany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="d26c3-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="d26c3-179">Poniższy kod przedstawia `PositionOptions` klasę:</span><span class="sxs-lookup"><span data-stu-id="d26c3-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="d26c3-180">Poniższy kod stosuje `MyActionFilterAttribute` `Index2` metodę do:</span><span class="sxs-lookup"><span data-stu-id="d26c3-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="d26c3-181">W obszarze **nagłówki odpowiedzi**, `author: Rick Anderson` i `Editor: Joe Smith` jest wyświetlana po `Sample/Index2` wywołaniu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="d26c3-182">Poniższy kod stosuje `MyActionFilterAttribute` `AddHeaderAttribute` stronę i do Razor strony:</span><span class="sxs-lookup"><span data-stu-id="d26c3-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d26c3-183">Nie można zastosować filtrów do Razor metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="d26c3-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="d26c3-184">Mogą być stosowane do Razor modelu strony lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="d26c3-185">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="d26c3-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="d26c3-186">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="d26c3-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="d26c3-187">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="d26c3-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="d26c3-188">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="d26c3-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="d26c3-189">Użycie atrybutu w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="d26c3-190">Atrybutów filtru nie można stosować do Razor metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="d26c3-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="d26c3-191">Użycie atrybutu na kontrolerze lub Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="d26c3-192">Globalnie dla wszystkich kontrolerów, akcji i Razor stron, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="d26c3-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="d26c3-193">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="d26c3-193">Default order of execution</span></span>

<span data-ttu-id="d26c3-194">Jeśli istnieje wiele filtrów dla określonego etapu potoku, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="d26c3-195">Filtry globalne Otocz filtry klas, które z kolei filtrują metody przestrzenne.</span><span class="sxs-lookup"><span data-stu-id="d26c3-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="d26c3-196">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="d26c3-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="d26c3-197">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="d26c3-197">The filter sequence:</span></span>

* <span data-ttu-id="d26c3-198">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="d26c3-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="d26c3-199">*Przed* kodem i Razor filtrami stron.</span><span class="sxs-lookup"><span data-stu-id="d26c3-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="d26c3-200">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="d26c3-201">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="d26c3-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="d26c3-202">*Po* kodzie kontrolera i Razor filtrów strony.</span><span class="sxs-lookup"><span data-stu-id="d26c3-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="d26c3-203">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="d26c3-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="d26c3-204">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="d26c3-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="d26c3-205">Sequence</span></span> | <span data-ttu-id="d26c3-206">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-206">Filter scope</span></span> | <span data-ttu-id="d26c3-207">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="d26c3-208">1</span><span class="sxs-lookup"><span data-stu-id="d26c3-208">1</span></span> | <span data-ttu-id="d26c3-209">Globalnie</span><span class="sxs-lookup"><span data-stu-id="d26c3-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d26c3-210">2</span><span class="sxs-lookup"><span data-stu-id="d26c3-210">2</span></span> | <span data-ttu-id="d26c3-211">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="d26c3-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="d26c3-212">3</span><span class="sxs-lookup"><span data-stu-id="d26c3-212">3</span></span> | <span data-ttu-id="d26c3-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d26c3-214">4</span><span class="sxs-lookup"><span data-stu-id="d26c3-214">4</span></span> | <span data-ttu-id="d26c3-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d26c3-216">5</span><span class="sxs-lookup"><span data-stu-id="d26c3-216">5</span></span> | <span data-ttu-id="d26c3-217">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="d26c3-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d26c3-218">6</span><span class="sxs-lookup"><span data-stu-id="d26c3-218">6</span></span> | <span data-ttu-id="d26c3-219">Globalnie</span><span class="sxs-lookup"><span data-stu-id="d26c3-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="d26c3-220">Filtry na poziomie kontrolera</span><span class="sxs-lookup"><span data-stu-id="d26c3-220">Controller level filters</span></span>

<span data-ttu-id="d26c3-221">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="d26c3-222">Te metody:</span><span class="sxs-lookup"><span data-stu-id="d26c3-222">These methods:</span></span>

* <span data-ttu-id="d26c3-223">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="d26c3-224">`OnActionExecuting`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="d26c3-225">`OnActionExecuted`jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="d26c3-226">`OnActionExecutionAsync`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="d26c3-227">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="d26c3-228">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="d26c3-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="d26c3-229">The `TestController`:</span></span>

* <span data-ttu-id="d26c3-230">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="d26c3-231">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="d26c3-232">Nawigowanie w celu `https://localhost:5001/Test2/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="d26c3-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="d26c3-233">Filtry na poziomie kontrolera ustawiają Właściwość [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="d26c3-234">Filtrów na poziomie kontrolera **nie** można ustawić do uruchomienia po zastosowaniu filtrów do metod.</span><span class="sxs-lookup"><span data-stu-id="d26c3-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="d26c3-235">Klauzula Order została omówiona w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-235">Order is explained in the next section.</span></span>

<span data-ttu-id="d26c3-236">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="d26c3-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="d26c3-237">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="d26c3-237">Overriding the default order</span></span>

<span data-ttu-id="d26c3-238">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="d26c3-239">`IOrderedFilter`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="d26c3-240">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="d26c3-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="d26c3-241">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="d26c3-242">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="d26c3-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="d26c3-243">`Order`Właściwość jest ustawiona z parametrem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="d26c3-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="d26c3-244">Należy wziąć pod uwagę dwa filtry akcji na następującym kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="d26c3-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="d26c3-245">Filtr globalny zostanie dodany w `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="d26c3-246">3 filtry działają w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="d26c3-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="d26c3-247">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="d26c3-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="d26c3-248">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="d26c3-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="d26c3-249">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="d26c3-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="d26c3-250">Jak wspomniano wcześniej, filtry na poziomie kontrolera [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ustawiają Właściwość Order `int.MinValue` dla filtrów wbudowanych, zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="d26c3-251">W poprzednim kodzie `MySampleActionFilter` ma zakres globalny, dlatego działa wcześniej `MyAction2FilterAttribute` , który ma zakres kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="d26c3-252">Aby `MyAction2FilterAttribute` najpierw wykonać przebieg, ustaw kolejność na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="d26c3-253">Aby najpierw uruchomić filtr globalny `MySampleActionFilter` , ustaw opcję `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="d26c3-254">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="d26c3-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="d26c3-255">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="d26c3-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="d26c3-256">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="d26c3-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-257">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="d26c3-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d26c3-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="d26c3-259">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="d26c3-260">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="d26c3-261">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="d26c3-262">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="d26c3-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="d26c3-263">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="d26c3-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="d26c3-264">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="d26c3-264">Dependency injection</span></span>

<span data-ttu-id="d26c3-265">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d26c3-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="d26c3-266">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="d26c3-267">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="d26c3-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="d26c3-268">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="d26c3-268">A type-activated filter means:</span></span>

* <span data-ttu-id="d26c3-269">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-269">An instance is created for each request.</span></span>
* <span data-ttu-id="d26c3-270">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="d26c3-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d26c3-271">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="d26c3-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d26c3-272">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="d26c3-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="d26c3-273">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="d26c3-274">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="d26c3-275">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="d26c3-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="d26c3-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="d26c3-277">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="d26c3-278">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-278">Loggers are available from DI.</span></span> <span data-ttu-id="d26c3-279">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="d26c3-280">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="d26c3-281">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-281">Logging added to filters:</span></span>

* <span data-ttu-id="d26c3-282">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="d26c3-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="d26c3-283">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="d26c3-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="d26c3-284">Wbudowane filtry rejestrują akcje i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="d26c3-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="d26c3-285">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="d26c3-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="d26c3-286">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="d26c3-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="d26c3-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="d26c3-288">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d26c3-289">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="d26c3-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="d26c3-290">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="d26c3-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="d26c3-291">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d26c3-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="d26c3-292">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="d26c3-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d26c3-293">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="d26c3-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="d26c3-294">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="d26c3-295">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="d26c3-296">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="d26c3-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="d26c3-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d26c3-298">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d26c3-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d26c3-299">`CreateInstance`Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="d26c3-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d26c3-300">TypeFilterAttribute</span></span>

<span data-ttu-id="d26c3-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="d26c3-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="d26c3-302">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="d26c3-303">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="d26c3-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="d26c3-304">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="d26c3-305">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="d26c3-306">`TypeFilterAttribute`Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="d26c3-307">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d26c3-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="d26c3-308">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="d26c3-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d26c3-309">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="d26c3-310">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="d26c3-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d26c3-311">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="d26c3-312">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="d26c3-312">Authorization filters</span></span>

<span data-ttu-id="d26c3-313">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-313">Authorization filters:</span></span>

* <span data-ttu-id="d26c3-314">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="d26c3-315">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-315">Control access to action methods.</span></span>
* <span data-ttu-id="d26c3-316">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="d26c3-317">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="d26c3-318">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="d26c3-319">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="d26c3-320">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-320">Calls the authorization system.</span></span>
* <span data-ttu-id="d26c3-321">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-321">Does not authorize requests.</span></span>

<span data-ttu-id="d26c3-322">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="d26c3-323">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="d26c3-323">The exception will not be handled.</span></span>
* <span data-ttu-id="d26c3-324">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="d26c3-325">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="d26c3-326">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="d26c3-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="d26c3-327">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="d26c3-327">Resource filters</span></span>

<span data-ttu-id="d26c3-328">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-328">Resource filters:</span></span>

* <span data-ttu-id="d26c3-329">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="d26c3-330">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="d26c3-331">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="d26c3-332">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="d26c3-333">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="d26c3-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="d26c3-334">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-334">Resource filter examples:</span></span>

* <span data-ttu-id="d26c3-335">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="d26c3-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="d26c3-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="d26c3-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="d26c3-337">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="d26c3-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="d26c3-338">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="d26c3-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="d26c3-339">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="d26c3-339">Action filters</span></span>

<span data-ttu-id="d26c3-340">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="d26c3-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="d26c3-341">Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="d26c3-342">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d26c3-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="d26c3-343">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-343">Action filters:</span></span>

* <span data-ttu-id="d26c3-344">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="d26c3-345">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="d26c3-346">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d26c3-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="d26c3-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="d26c3-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>— umożliwia odczytywanie danych wejściowych do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="d26c3-349"><xref:Microsoft.AspNetCore.Mvc.Controller>-Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="d26c3-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="d26c3-351">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="d26c3-352">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="d26c3-353">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="d26c3-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d26c3-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="d26c3-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="d26c3-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="d26c3-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d26c3-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="d26c3-357">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="d26c3-357">Setting this property to null:</span></span>

  * <span data-ttu-id="d26c3-358">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="d26c3-359">`Result`jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="d26c3-360">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="d26c3-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="d26c3-361">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="d26c3-362">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="d26c3-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="d26c3-363">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="d26c3-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="d26c3-364">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="d26c3-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="d26c3-365">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="d26c3-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="d26c3-366">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-366">Validate model state.</span></span>
* <span data-ttu-id="d26c3-367">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="d26c3-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-368">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="d26c3-369">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="d26c3-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="d26c3-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d26c3-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="d26c3-372">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="d26c3-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="d26c3-373">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="d26c3-374">`ActionExecutedContext.Result`jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="d26c3-375">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="d26c3-375">Exception filters</span></span>

<span data-ttu-id="d26c3-376">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="d26c3-376">Exception filters:</span></span>

* <span data-ttu-id="d26c3-377">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="d26c3-378">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="d26c3-379">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="d26c3-380">Następujący kod testuje filtr wyjątku:</span><span class="sxs-lookup"><span data-stu-id="d26c3-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="d26c3-381">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="d26c3-381">Exception filters:</span></span>

* <span data-ttu-id="d26c3-382">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="d26c3-382">Don't have before and after events.</span></span>
* <span data-ttu-id="d26c3-383">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="d26c3-384">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="d26c3-385">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="d26c3-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="d26c3-386">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="d26c3-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="d26c3-387">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-387">This stops propagation of the exception.</span></span> <span data-ttu-id="d26c3-388">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="d26c3-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="d26c3-389">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-389">Only an action filter can do that.</span></span>

<span data-ttu-id="d26c3-390">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="d26c3-390">Exception filters:</span></span>

* <span data-ttu-id="d26c3-391">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="d26c3-392">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="d26c3-393">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="d26c3-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="d26c3-394">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="d26c3-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="d26c3-395">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="d26c3-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="d26c3-396">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="d26c3-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="d26c3-397">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="d26c3-397">Result filters</span></span>

<span data-ttu-id="d26c3-398">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="d26c3-398">Result filters:</span></span>

* <span data-ttu-id="d26c3-399">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="d26c3-399">Implement an interface:</span></span>
  * <span data-ttu-id="d26c3-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d26c3-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="d26c3-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="d26c3-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="d26c3-402">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="d26c3-403">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="d26c3-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="d26c3-404">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="d26c3-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d26c3-405">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="d26c3-406">Akcja zwracająca widok obejmuje wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanej operacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="d26c3-407">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="d26c3-408">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="d26c3-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="d26c3-409">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="d26c3-410">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="d26c3-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="d26c3-411">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="d26c3-412">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="d26c3-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="d26c3-414">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d26c3-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="d26c3-415">Zgłaszanie wyjątku w `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="d26c3-416">Zapobiega wykonywaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="d26c3-417">Jest traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d26c3-418">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź prawdopodobnie została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="d26c3-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="d26c3-419">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="d26c3-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="d26c3-420">`ResultExecutedContext.Canceled`jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="d26c3-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="d26c3-421">`ResultExecutedContext.Exception`ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="d26c3-422">Ustawienie `Exception` wartości null skutecznie obsługuje wyjątek i uniemożliwia ponowne zgłoszenie wyjątku w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="d26c3-423">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="d26c3-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="d26c3-424">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="d26c3-425">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="d26c3-426">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="d26c3-427">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="d26c3-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="d26c3-428">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="d26c3-429">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="d26c3-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="d26c3-430"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="d26c3-431">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="d26c3-431">This includes action results produced by:</span></span>

* <span data-ttu-id="d26c3-432">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="d26c3-433">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="d26c3-433">Exception filters.</span></span>

<span data-ttu-id="d26c3-434">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="d26c3-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="d26c3-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d26c3-435">IFilterFactory</span></span>

<span data-ttu-id="d26c3-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="d26c3-437">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="d26c3-438">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="d26c3-439">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="d26c3-440">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="d26c3-441">`IFilterFactory`można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="d26c3-442">Filtr jest stosowany w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="d26c3-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="d26c3-443">Przetestuj poprzedni kod, uruchamiając [przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="d26c3-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="d26c3-444">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="d26c3-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="d26c3-445">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="d26c3-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="d26c3-446">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="d26c3-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="d26c3-447">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="d26c3-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="d26c3-448">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="d26c3-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="d26c3-449">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="d26c3-449">**internal:** `My header`</span></span>

<span data-ttu-id="d26c3-450">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="d26c3-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="d26c3-451">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="d26c3-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="d26c3-452">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="d26c3-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="d26c3-453">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="d26c3-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="d26c3-454">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="d26c3-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d26c3-456">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d26c3-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d26c3-457">`CreateInstance`Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="d26c3-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="d26c3-458">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="d26c3-459">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="d26c3-460">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="d26c3-461">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="d26c3-462">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego, co oznacza, że ma dostęp do kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="d26c3-463">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="d26c3-464">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="d26c3-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="d26c3-465">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="d26c3-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="d26c3-466">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="d26c3-467">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="d26c3-467">Next actions</span></span>

* <span data-ttu-id="d26c3-468">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d26c3-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="d26c3-469">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="d26c3-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d26c3-470">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d26c3-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d26c3-471">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="d26c3-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="d26c3-472">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="d26c3-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="d26c3-473">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="d26c3-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="d26c3-474">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="d26c3-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="d26c3-475">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="d26c3-476">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="d26c3-477">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="d26c3-478">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="d26c3-479">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="d26c3-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="d26c3-480">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d26c3-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="d26c3-481">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="d26c3-481">How filters work</span></span>

<span data-ttu-id="d26c3-482">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="d26c3-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="d26c3-483">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok wywołania akcji ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="d26c3-486">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="d26c3-486">Filter types</span></span>

<span data-ttu-id="d26c3-487">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="d26c3-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="d26c3-488">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="d26c3-489">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie jest nieautoryzowane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="d26c3-490">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="d26c3-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="d26c3-491">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-491">Run after authorization.</span></span>  
  * <span data-ttu-id="d26c3-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>można uruchomić kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="d26c3-493">Na przykład program `OnResourceExecuting` może uruchomić kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="d26c3-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>można uruchomić kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="d26c3-495">[Filtry akcji](#action-filters) mogą uruchomić kod bezpośrednio przed i po wywołaniu pojedynczej metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="d26c3-496">Mogą one służyć do manipulowania argumentami przekazaną do akcji i wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="d26c3-497">Filtry akcji **nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="d26c3-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="d26c3-498">[Filtry wyjątków](#exception-filters) są używane do stosowania zasad globalnych do nieobsłużonych wyjątków, które wystąpiły przed zapisem w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d26c3-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="d26c3-499">[Filtry wynikowe](#result-filters) mogą uruchamiać kod bezpośrednio przed i po wykonaniu poszczególnych wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="d26c3-500">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="d26c3-501">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="d26c3-502">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="d26c3-505">Implementacja</span><span class="sxs-lookup"><span data-stu-id="d26c3-505">Implementation</span></span>

<span data-ttu-id="d26c3-506">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="d26c3-507">Filtry synchroniczne mogą uruchamiać kod przed ( `On-Stage-Executing` ) i po nim ( `On-Stage-Executed` ) ich etap potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="d26c3-508">Na przykład, `OnActionExecuting` jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="d26c3-509">`OnActionExecuted`jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d26c3-510">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę:</span><span class="sxs-lookup"><span data-stu-id="d26c3-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="d26c3-511">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="d26c3-512">Każda z tych `On-Stage-ExecutionAsync` metod przyjmuje `FilterType-ExecutionDelegate` etap potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="d26c3-513">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="d26c3-513">Multiple filter stages</span></span>

<span data-ttu-id="d26c3-514">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="d26c3-515">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje `IActionFilter` , `IResultFilter` i ich odpowiedniki asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="d26c3-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="d26c3-516">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d26c3-517">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="d26c3-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d26c3-518">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d26c3-519">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="d26c3-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="d26c3-520">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> zastąpienie tylko metod synchronicznych lub metodę asynchroniczną dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="d26c3-521">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-521">Built-in filter attributes</span></span>

<span data-ttu-id="d26c3-522">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="d26c3-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="d26c3-523">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="d26c3-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-524">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="d26c3-525">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="d26c3-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="d26c3-526">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="d26c3-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="d26c3-527">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="d26c3-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="d26c3-528">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="d26c3-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="d26c3-529">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="d26c3-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="d26c3-530">Użycie atrybutu w akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="d26c3-531">Używanie atrybutu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="d26c3-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="d26c3-532">Globalnie dla wszystkich kontrolerów i akcji, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="d26c3-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d26c3-533">Poprzedni kod dodaje trzy filtry globalnie przy użyciu kolekcji [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="d26c3-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="d26c3-534">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="d26c3-534">Default order of execution</span></span>

<span data-ttu-id="d26c3-535">Jeśli istnieje wiele filtrów tego *samego typu*, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="d26c3-536">Filtry globalne Otocz filtry klas.</span><span class="sxs-lookup"><span data-stu-id="d26c3-536">Global filters surround class filters.</span></span> <span data-ttu-id="d26c3-537">Filtry klas Otocz filtry metod.</span><span class="sxs-lookup"><span data-stu-id="d26c3-537">Class filters surround method filters.</span></span>

<span data-ttu-id="d26c3-538">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="d26c3-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="d26c3-539">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="d26c3-539">The filter sequence:</span></span>

* <span data-ttu-id="d26c3-540">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="d26c3-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="d26c3-541">*Przed* kodem filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="d26c3-542">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="d26c3-543">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="d26c3-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="d26c3-544">*Po* kodzie filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="d26c3-545">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="d26c3-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="d26c3-546">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="d26c3-547">Sequence</span><span class="sxs-lookup"><span data-stu-id="d26c3-547">Sequence</span></span> | <span data-ttu-id="d26c3-548">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-548">Filter scope</span></span> | <span data-ttu-id="d26c3-549">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="d26c3-550">1</span><span class="sxs-lookup"><span data-stu-id="d26c3-550">1</span></span> | <span data-ttu-id="d26c3-551">Globalnie</span><span class="sxs-lookup"><span data-stu-id="d26c3-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d26c3-552">2</span><span class="sxs-lookup"><span data-stu-id="d26c3-552">2</span></span> | <span data-ttu-id="d26c3-553">Kontroler</span><span class="sxs-lookup"><span data-stu-id="d26c3-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d26c3-554">3</span><span class="sxs-lookup"><span data-stu-id="d26c3-554">3</span></span> | <span data-ttu-id="d26c3-555">Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d26c3-556">4</span><span class="sxs-lookup"><span data-stu-id="d26c3-556">4</span></span> | <span data-ttu-id="d26c3-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d26c3-558">5</span><span class="sxs-lookup"><span data-stu-id="d26c3-558">5</span></span> | <span data-ttu-id="d26c3-559">Kontroler</span><span class="sxs-lookup"><span data-stu-id="d26c3-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d26c3-560">6</span><span class="sxs-lookup"><span data-stu-id="d26c3-560">6</span></span> | <span data-ttu-id="d26c3-561">Globalnie</span><span class="sxs-lookup"><span data-stu-id="d26c3-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="d26c3-562">Ta sekwencja pokazuje:</span><span class="sxs-lookup"><span data-stu-id="d26c3-562">This sequence shows:</span></span>

* <span data-ttu-id="d26c3-563">Filtr metody jest zagnieżdżony w obrębie filtru kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="d26c3-564">Filtr kontrolera jest zagnieżdżony w obrębie filtru globalnego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="d26c3-565">Filtry na Razor poziomie kontrolera i strony</span><span class="sxs-lookup"><span data-stu-id="d26c3-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="d26c3-566">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="d26c3-567">Te metody:</span><span class="sxs-lookup"><span data-stu-id="d26c3-567">These methods:</span></span>

* <span data-ttu-id="d26c3-568">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="d26c3-569">`OnActionExecuting`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="d26c3-570">`OnActionExecuted`jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="d26c3-571">`OnActionExecutionAsync`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="d26c3-572">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="d26c3-573">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="d26c3-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="d26c3-574">The `TestController`:</span></span>

* <span data-ttu-id="d26c3-575">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="d26c3-576">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="d26c3-577">Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="d26c3-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="d26c3-578">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="d26c3-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="d26c3-579">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="d26c3-579">Overriding the default order</span></span>

<span data-ttu-id="d26c3-580">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="d26c3-581">`IOrderedFilter`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="d26c3-582">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="d26c3-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="d26c3-583">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="d26c3-584">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="d26c3-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="d26c3-585">`Order`Właściwość można ustawić przy użyciu parametru konstruktora:</span><span class="sxs-lookup"><span data-stu-id="d26c3-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="d26c3-586">Należy wziąć pod uwagę te same 3 filtry akcji, które przedstawiono w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="d26c3-587">Jeśli `Order` Właściwość kontrolera i filtry globalne zostały odpowiednio ustawione na 1 i 2, kolejność wykonywania zostanie odwrócona.</span><span class="sxs-lookup"><span data-stu-id="d26c3-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="d26c3-588">Sequence</span><span class="sxs-lookup"><span data-stu-id="d26c3-588">Sequence</span></span> | <span data-ttu-id="d26c3-589">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-589">Filter scope</span></span> | <span data-ttu-id="d26c3-590">`Order`wartość</span><span class="sxs-lookup"><span data-stu-id="d26c3-590">`Order` property</span></span> | <span data-ttu-id="d26c3-591">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="d26c3-592">1</span><span class="sxs-lookup"><span data-stu-id="d26c3-592">1</span></span> | <span data-ttu-id="d26c3-593">Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-593">Method</span></span> | <span data-ttu-id="d26c3-594">0</span><span class="sxs-lookup"><span data-stu-id="d26c3-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d26c3-595">2</span><span class="sxs-lookup"><span data-stu-id="d26c3-595">2</span></span> | <span data-ttu-id="d26c3-596">Kontroler</span><span class="sxs-lookup"><span data-stu-id="d26c3-596">Controller</span></span> | <span data-ttu-id="d26c3-597">1</span><span class="sxs-lookup"><span data-stu-id="d26c3-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="d26c3-598">3</span><span class="sxs-lookup"><span data-stu-id="d26c3-598">3</span></span> | <span data-ttu-id="d26c3-599">Globalnie</span><span class="sxs-lookup"><span data-stu-id="d26c3-599">Global</span></span> | <span data-ttu-id="d26c3-600">2</span><span class="sxs-lookup"><span data-stu-id="d26c3-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="d26c3-601">4</span><span class="sxs-lookup"><span data-stu-id="d26c3-601">4</span></span> | <span data-ttu-id="d26c3-602">Globalnie</span><span class="sxs-lookup"><span data-stu-id="d26c3-602">Global</span></span> | <span data-ttu-id="d26c3-603">2</span><span class="sxs-lookup"><span data-stu-id="d26c3-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="d26c3-604">5</span><span class="sxs-lookup"><span data-stu-id="d26c3-604">5</span></span> | <span data-ttu-id="d26c3-605">Kontroler</span><span class="sxs-lookup"><span data-stu-id="d26c3-605">Controller</span></span> | <span data-ttu-id="d26c3-606">1</span><span class="sxs-lookup"><span data-stu-id="d26c3-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="d26c3-607">6</span><span class="sxs-lookup"><span data-stu-id="d26c3-607">6</span></span> | <span data-ttu-id="d26c3-608">Metoda</span><span class="sxs-lookup"><span data-stu-id="d26c3-608">Method</span></span> | <span data-ttu-id="d26c3-609">0</span><span class="sxs-lookup"><span data-stu-id="d26c3-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="d26c3-610">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="d26c3-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="d26c3-611">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="d26c3-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="d26c3-612">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="d26c3-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="d26c3-613">W przypadku filtrów wbudowanych zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="d26c3-614">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="d26c3-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="d26c3-615">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="d26c3-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="d26c3-616">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="d26c3-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-617">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="d26c3-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d26c3-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="d26c3-619">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="d26c3-620">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="d26c3-621">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="d26c3-622">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="d26c3-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="d26c3-623">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="d26c3-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="d26c3-624">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="d26c3-624">Dependency injection</span></span>

<span data-ttu-id="d26c3-625">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d26c3-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="d26c3-626">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="d26c3-627">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="d26c3-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="d26c3-628">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="d26c3-628">A type-activated filter means:</span></span>

* <span data-ttu-id="d26c3-629">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-629">An instance is created for each request.</span></span>
* <span data-ttu-id="d26c3-630">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="d26c3-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d26c3-631">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="d26c3-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d26c3-632">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="d26c3-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="d26c3-633">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="d26c3-634">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="d26c3-635">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="d26c3-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="d26c3-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="d26c3-637">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="d26c3-638">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-638">Loggers are available from DI.</span></span> <span data-ttu-id="d26c3-639">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="d26c3-640">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="d26c3-641">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-641">Logging added to filters:</span></span>

* <span data-ttu-id="d26c3-642">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="d26c3-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="d26c3-643">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="d26c3-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="d26c3-644">Wbudowane filtry akcje dziennika i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="d26c3-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="d26c3-645">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="d26c3-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="d26c3-646">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="d26c3-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="d26c3-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="d26c3-648">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d26c3-649">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="d26c3-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d26c3-650">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="d26c3-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="d26c3-651">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d26c3-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="d26c3-652">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="d26c3-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d26c3-653">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="d26c3-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="d26c3-654">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="d26c3-655">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="d26c3-656">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="d26c3-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="d26c3-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d26c3-658">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d26c3-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d26c3-659">`CreateInstance`Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="d26c3-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d26c3-660">TypeFilterAttribute</span></span>

<span data-ttu-id="d26c3-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="d26c3-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="d26c3-662">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="d26c3-663">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="d26c3-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="d26c3-664">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="d26c3-665">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="d26c3-666">`TypeFilterAttribute`Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="d26c3-667">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d26c3-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="d26c3-668">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="d26c3-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d26c3-669">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="d26c3-670">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="d26c3-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d26c3-671">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="d26c3-672">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="d26c3-672">Authorization filters</span></span>

<span data-ttu-id="d26c3-673">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-673">Authorization filters:</span></span>

* <span data-ttu-id="d26c3-674">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="d26c3-675">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-675">Control access to action methods.</span></span>
* <span data-ttu-id="d26c3-676">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="d26c3-677">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="d26c3-678">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="d26c3-679">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="d26c3-680">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-680">Calls the authorization system.</span></span>
* <span data-ttu-id="d26c3-681">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-681">Does not authorize requests.</span></span>

<span data-ttu-id="d26c3-682">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="d26c3-683">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="d26c3-683">The exception will not be handled.</span></span>
* <span data-ttu-id="d26c3-684">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="d26c3-685">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="d26c3-686">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="d26c3-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="d26c3-687">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="d26c3-687">Resource filters</span></span>

<span data-ttu-id="d26c3-688">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-688">Resource filters:</span></span>

* <span data-ttu-id="d26c3-689">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="d26c3-690">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="d26c3-691">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="d26c3-692">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="d26c3-693">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="d26c3-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="d26c3-694">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-694">Resource filter examples:</span></span>

* <span data-ttu-id="d26c3-695">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="d26c3-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="d26c3-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="d26c3-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="d26c3-697">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="d26c3-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="d26c3-698">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="d26c3-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="d26c3-699">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="d26c3-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d26c3-700">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="d26c3-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="d26c3-701">Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="d26c3-702">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d26c3-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="d26c3-703">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-703">Action filters:</span></span>

* <span data-ttu-id="d26c3-704">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="d26c3-705">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="d26c3-706">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d26c3-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="d26c3-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="d26c3-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-umożliwia odczytywanie danych wejściowych metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="d26c3-709"><xref:Microsoft.AspNetCore.Mvc.Controller>-Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d26c3-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="d26c3-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="d26c3-711">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="d26c3-712">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="d26c3-713">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="d26c3-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d26c3-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="d26c3-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="d26c3-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="d26c3-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d26c3-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="d26c3-717">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="d26c3-717">Setting this property to null:</span></span>

  * <span data-ttu-id="d26c3-718">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="d26c3-719">`Result`jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="d26c3-720">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="d26c3-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="d26c3-721">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="d26c3-722">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="d26c3-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="d26c3-723">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="d26c3-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="d26c3-724">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="d26c3-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="d26c3-725">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="d26c3-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="d26c3-726">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-726">Validate model state.</span></span>
* <span data-ttu-id="d26c3-727">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="d26c3-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="d26c3-728">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="d26c3-729">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="d26c3-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="d26c3-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d26c3-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="d26c3-732">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="d26c3-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="d26c3-733">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="d26c3-734">`ActionExecutedContext.Result`jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="d26c3-735">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="d26c3-735">Exception filters</span></span>

<span data-ttu-id="d26c3-736">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="d26c3-736">Exception filters:</span></span>

* <span data-ttu-id="d26c3-737">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="d26c3-738">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="d26c3-739">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d26c3-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="d26c3-740">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="d26c3-740">Exception filters:</span></span>

* <span data-ttu-id="d26c3-741">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="d26c3-741">Don't have before and after events.</span></span>
* <span data-ttu-id="d26c3-742">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="d26c3-743">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="d26c3-744">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="d26c3-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="d26c3-745">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="d26c3-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="d26c3-746">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-746">This stops propagation of the exception.</span></span> <span data-ttu-id="d26c3-747">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="d26c3-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="d26c3-748">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-748">Only an action filter can do that.</span></span>

<span data-ttu-id="d26c3-749">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="d26c3-749">Exception filters:</span></span>

* <span data-ttu-id="d26c3-750">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="d26c3-751">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="d26c3-752">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="d26c3-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="d26c3-753">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="d26c3-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="d26c3-754">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="d26c3-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="d26c3-755">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="d26c3-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="d26c3-756">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="d26c3-756">Result filters</span></span>

<span data-ttu-id="d26c3-757">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="d26c3-757">Result filters:</span></span>

* <span data-ttu-id="d26c3-758">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="d26c3-758">Implement an interface:</span></span>
  * <span data-ttu-id="d26c3-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d26c3-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="d26c3-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="d26c3-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="d26c3-761">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="d26c3-762">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="d26c3-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="d26c3-763">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="d26c3-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d26c3-764">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="d26c3-765">Akcja zwracająca widok będzie obejmować wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanych operacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="d26c3-766">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="d26c3-767">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="d26c3-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="d26c3-768">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="d26c3-769">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="d26c3-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="d26c3-770">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="d26c3-771">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="d26c3-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="d26c3-773">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d26c3-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="d26c3-774">Zgłaszanie wyjątku w programie `IResultFilter.OnResultExecuting` spowoduje:</span><span class="sxs-lookup"><span data-stu-id="d26c3-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="d26c3-775">Zapobiegaj wykonaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="d26c3-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="d26c3-776">Być traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="d26c3-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d26c3-777">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="d26c3-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="d26c3-778">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="d26c3-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="d26c3-779">`ResultExecutedContext.Canceled`jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="d26c3-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="d26c3-780">`ResultExecutedContext.Exception`ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d26c3-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="d26c3-781">Ustawienie `Exception` wartości null powoduje skuteczną obsługę wyjątku i uniemożliwia ponowne zgłoszenie wyjątku przez ASP.NET Core w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="d26c3-782">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="d26c3-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="d26c3-783">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="d26c3-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="d26c3-784">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="d26c3-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="d26c3-785">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="d26c3-786">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="d26c3-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="d26c3-787">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="d26c3-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="d26c3-788">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="d26c3-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="d26c3-789"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="d26c3-790">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="d26c3-790">This includes action results produced by:</span></span>

* <span data-ttu-id="d26c3-791">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="d26c3-792">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="d26c3-792">Exception filters.</span></span>

<span data-ttu-id="d26c3-793">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="d26c3-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="d26c3-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d26c3-794">IFilterFactory</span></span>

<span data-ttu-id="d26c3-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="d26c3-796">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="d26c3-797">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="d26c3-798">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="d26c3-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="d26c3-799">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="d26c3-800">`IFilterFactory`można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="d26c3-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="d26c3-801">Poprzedni kod może być testowany przez uruchomienie [przykładu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="d26c3-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="d26c3-802">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="d26c3-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="d26c3-803">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="d26c3-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="d26c3-804">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="d26c3-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="d26c3-805">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="d26c3-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="d26c3-806">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="d26c3-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="d26c3-807">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="d26c3-807">**internal:** `My header`</span></span>

<span data-ttu-id="d26c3-808">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="d26c3-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="d26c3-809">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="d26c3-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="d26c3-810">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="d26c3-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="d26c3-811">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="d26c3-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="d26c3-812">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="d26c3-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="d26c3-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="d26c3-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d26c3-814">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d26c3-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d26c3-815">`CreateInstance`Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="d26c3-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="d26c3-816">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="d26c3-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="d26c3-817">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="d26c3-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="d26c3-818">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="d26c3-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="d26c3-819">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="d26c3-820">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego ASP.NET Core, co oznacza, że ma dostęp do ASP.NET Core kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="d26c3-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="d26c3-821">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="d26c3-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="d26c3-822">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="d26c3-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="d26c3-823">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="d26c3-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="d26c3-824">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="d26c3-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="d26c3-825">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="d26c3-825">Next actions</span></span>

* <span data-ttu-id="d26c3-826">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d26c3-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="d26c3-827">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="d26c3-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
