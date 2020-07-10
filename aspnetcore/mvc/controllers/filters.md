---
title: Filtry w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak działają filtry i jak korzystać z nich w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 0141ad2df5216183424980a6ca50bf6bcd64ade5
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213062"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="8bb99-103">Filtry w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bb99-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bb99-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8bb99-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8bb99-105">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="8bb99-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8bb99-106">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="8bb99-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8bb99-107">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="8bb99-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8bb99-108">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="8bb99-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8bb99-109">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8bb99-110">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8bb99-111">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="8bb99-112">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8bb99-113">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="8bb99-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="8bb99-114">Filtry nie działają bezpośrednio ze [ Razor składnikami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="8bb99-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="8bb99-115">Filtr może mieć tylko pośredni wpływ na składnik, gdy:</span><span class="sxs-lookup"><span data-stu-id="8bb99-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="8bb99-116">Składnik jest osadzony na stronie lub widoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="8bb99-117">Strona lub kontroler/widok używają filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="8bb99-118">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8bb99-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8bb99-119">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="8bb99-119">How filters work</span></span>

<span data-ttu-id="8bb99-120">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="8bb99-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="8bb99-121">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok akcji wywołania.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8bb99-124">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="8bb99-124">Filter types</span></span>

<span data-ttu-id="8bb99-125">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="8bb99-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8bb99-126">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8bb99-127">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie nie jest autoryzowane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="8bb99-128">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8bb99-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8bb99-129">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-129">Run after authorization.</span></span>  
  * <span data-ttu-id="8bb99-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>uruchamia kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8bb99-131">Na przykład `OnResourceExecuting` uruchamia kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="8bb99-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>uruchamia kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8bb99-133">[Filtry akcji](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="8bb99-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="8bb99-134">Uruchom kod bezpośrednio przed i po wywołaniu metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="8bb99-135">Może zmienić argumenty przekazane do akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="8bb99-136">Można zmienić wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="8bb99-137">**Nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="8bb99-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8bb99-138">[Filtry wyjątków](#exception-filters) stosują zasady globalne do nieobsłużonych wyjątków, które występują przed zapisaniem treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8bb99-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="8bb99-139">[Filtry wyników](#result-filters) uruchamiają kod bezpośrednio przed i po wykonaniu wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="8bb99-140">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8bb99-141">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8bb99-142">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8bb99-145">Implementacja</span><span class="sxs-lookup"><span data-stu-id="8bb99-145">Implementation</span></span>

<span data-ttu-id="8bb99-146">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8bb99-147">Filtry synchroniczne uruchamiają kod przed i po fazie potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="8bb99-148">Na przykład, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="8bb99-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8bb99-150">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="8bb99-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="8bb99-151">Na przykład <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="8bb99-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8bb99-152">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8bb99-153">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="8bb99-153">Multiple filter stages</span></span>

<span data-ttu-id="8bb99-154">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8bb99-155">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje:</span><span class="sxs-lookup"><span data-stu-id="8bb99-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="8bb99-156">Synchroniczne: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> i<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="8bb99-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="8bb99-157">Asynchroniczny: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> i<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8bb99-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="8bb99-158">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8bb99-159">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="8bb99-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8bb99-160">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8bb99-161">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="8bb99-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8bb99-162">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , Zastąp tylko metody synchroniczne lub metodę asynchroniczną dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8bb99-163">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-163">Built-in filter attributes</span></span>

<span data-ttu-id="8bb99-164">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="8bb99-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8bb99-165">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="8bb99-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8bb99-166">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8bb99-167">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="8bb99-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="8bb99-168">Użyj narzędzia, takiego jak [Narzędzia deweloperskie przeglądarki](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="8bb99-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="8bb99-169">W obszarze **nagłówki odpowiedzi** `author: Rick Anderson` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="8bb99-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="8bb99-170">Poniższy kod implementuje `ActionFilterAttribute` , że:</span><span class="sxs-lookup"><span data-stu-id="8bb99-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="8bb99-171">Odczytuje tytuł i nazwę z systemu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="8bb99-172">W przeciwieństwie do poprzedniego przykładu, poniższy kod nie wymaga dodania parametrów filtru do kodu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="8bb99-173">Dodaje tytuł i nazwę do nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8bb99-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8bb99-174">Opcje konfiguracji są dostępne z [systemu konfiguracji](xref:fundamentals/configuration/index) przy użyciu [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8bb99-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8bb99-175">Na przykład, z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="8bb99-175">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="8bb99-176">W `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="8bb99-177">`PositionOptions`Klasa jest dodawana do kontenera usługi z `"Position"` obszarem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="8bb99-178">`MyActionFilterAttribute`Zostanie dodany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="8bb99-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="8bb99-179">Poniższy kod przedstawia `PositionOptions` klasę:</span><span class="sxs-lookup"><span data-stu-id="8bb99-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8bb99-180">Poniższy kod stosuje `MyActionFilterAttribute` `Index2` metodę do:</span><span class="sxs-lookup"><span data-stu-id="8bb99-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="8bb99-181">W obszarze **nagłówki odpowiedzi**, `author: Rick Anderson` i `Editor: Joe Smith` jest wyświetlana po `Sample/Index2` wywołaniu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="8bb99-182">Poniższy kod stosuje `MyActionFilterAttribute` `AddHeaderAttribute` stronę i do Razor strony:</span><span class="sxs-lookup"><span data-stu-id="8bb99-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8bb99-183">Nie można zastosować filtrów do Razor metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="8bb99-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="8bb99-184">Mogą być stosowane do Razor modelu strony lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="8bb99-185">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="8bb99-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8bb99-186">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="8bb99-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8bb99-187">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="8bb99-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="8bb99-188">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="8bb99-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8bb99-189">Użycie atrybutu w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="8bb99-190">Atrybutów filtru nie można stosować do Razor metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="8bb99-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="8bb99-191">Użycie atrybutu na kontrolerze lub Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="8bb99-192">Globalnie dla wszystkich kontrolerów, akcji i Razor stron, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="8bb99-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="8bb99-193">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="8bb99-193">Default order of execution</span></span>

<span data-ttu-id="8bb99-194">Jeśli istnieje wiele filtrów dla określonego etapu potoku, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8bb99-195">Filtry globalne Otocz filtry klas, które z kolei filtrują metody przestrzenne.</span><span class="sxs-lookup"><span data-stu-id="8bb99-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="8bb99-196">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="8bb99-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8bb99-197">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="8bb99-197">The filter sequence:</span></span>

* <span data-ttu-id="8bb99-198">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="8bb99-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8bb99-199">*Przed* kodem i Razor filtrami stron.</span><span class="sxs-lookup"><span data-stu-id="8bb99-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="8bb99-200">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8bb99-201">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="8bb99-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8bb99-202">*Po* kodzie kontrolera i Razor filtrów strony.</span><span class="sxs-lookup"><span data-stu-id="8bb99-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="8bb99-203">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="8bb99-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8bb99-204">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8bb99-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="8bb99-205">Sequence</span></span> | <span data-ttu-id="8bb99-206">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-206">Filter scope</span></span> | <span data-ttu-id="8bb99-207">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8bb99-208">1</span><span class="sxs-lookup"><span data-stu-id="8bb99-208">1</span></span> | <span data-ttu-id="8bb99-209">Globalny</span><span class="sxs-lookup"><span data-stu-id="8bb99-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8bb99-210">2</span><span class="sxs-lookup"><span data-stu-id="8bb99-210">2</span></span> | <span data-ttu-id="8bb99-211">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="8bb99-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="8bb99-212">3</span><span class="sxs-lookup"><span data-stu-id="8bb99-212">3</span></span> | <span data-ttu-id="8bb99-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8bb99-214">4</span><span class="sxs-lookup"><span data-stu-id="8bb99-214">4</span></span> | <span data-ttu-id="8bb99-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8bb99-216">5</span><span class="sxs-lookup"><span data-stu-id="8bb99-216">5</span></span> | <span data-ttu-id="8bb99-217">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="8bb99-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8bb99-218">6</span><span class="sxs-lookup"><span data-stu-id="8bb99-218">6</span></span> | <span data-ttu-id="8bb99-219">Globalny</span><span class="sxs-lookup"><span data-stu-id="8bb99-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="8bb99-220">Filtry na poziomie kontrolera</span><span class="sxs-lookup"><span data-stu-id="8bb99-220">Controller level filters</span></span>

<span data-ttu-id="8bb99-221">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8bb99-222">Te metody:</span><span class="sxs-lookup"><span data-stu-id="8bb99-222">These methods:</span></span>

* <span data-ttu-id="8bb99-223">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8bb99-224">`OnActionExecuting`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8bb99-225">`OnActionExecuted`jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8bb99-226">`OnActionExecutionAsync`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8bb99-227">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8bb99-228">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8bb99-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="8bb99-229">The `TestController`:</span></span>

* <span data-ttu-id="8bb99-230">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8bb99-231">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="8bb99-232">Nawigowanie w celu `https://localhost:5001/Test2/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8bb99-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8bb99-233">Filtry na poziomie kontrolera ustawiają Właściwość [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="8bb99-234">Filtrów na poziomie kontrolera **nie** można ustawić do uruchomienia po zastosowaniu filtrów do metod.</span><span class="sxs-lookup"><span data-stu-id="8bb99-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="8bb99-235">Klauzula Order została omówiona w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-235">Order is explained in the next section.</span></span>

<span data-ttu-id="8bb99-236">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="8bb99-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8bb99-237">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="8bb99-237">Overriding the default order</span></span>

<span data-ttu-id="8bb99-238">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8bb99-239">`IOrderedFilter`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8bb99-240">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="8bb99-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8bb99-241">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8bb99-242">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="8bb99-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8bb99-243">`Order`Właściwość jest ustawiona z parametrem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="8bb99-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="8bb99-244">Należy wziąć pod uwagę dwa filtry akcji na następującym kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="8bb99-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="8bb99-245">Filtr globalny zostanie dodany w `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="8bb99-246">3 filtry działają w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="8bb99-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="8bb99-247">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="8bb99-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8bb99-248">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="8bb99-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8bb99-249">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="8bb99-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8bb99-250">Jak wspomniano wcześniej, filtry na poziomie kontrolera [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ustawiają Właściwość Order `int.MinValue` dla filtrów wbudowanych, zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="8bb99-251">W poprzednim kodzie `MySampleActionFilter` ma zakres globalny, dlatego działa wcześniej `MyAction2FilterAttribute` , który ma zakres kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="8bb99-252">Aby `MyAction2FilterAttribute` najpierw wykonać przebieg, ustaw kolejność na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="8bb99-253">Aby najpierw uruchomić filtr globalny `MySampleActionFilter` , ustaw opcję `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8bb99-254">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="8bb99-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8bb99-255">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="8bb99-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8bb99-256">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="8bb99-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8bb99-257">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8bb99-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8bb99-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8bb99-259">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8bb99-260">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8bb99-261">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8bb99-262">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="8bb99-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8bb99-263">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="8bb99-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="8bb99-264">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="8bb99-264">Dependency injection</span></span>

<span data-ttu-id="8bb99-265">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8bb99-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8bb99-266">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8bb99-267">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="8bb99-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8bb99-268">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="8bb99-268">A type-activated filter means:</span></span>

* <span data-ttu-id="8bb99-269">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-269">An instance is created for each request.</span></span>
* <span data-ttu-id="8bb99-270">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="8bb99-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8bb99-271">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="8bb99-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8bb99-272">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="8bb99-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8bb99-273">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8bb99-274">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8bb99-275">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="8bb99-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8bb99-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8bb99-277">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8bb99-278">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-278">Loggers are available from DI.</span></span> <span data-ttu-id="8bb99-279">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8bb99-280">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8bb99-281">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-281">Logging added to filters:</span></span>

* <span data-ttu-id="8bb99-282">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="8bb99-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8bb99-283">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="8bb99-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8bb99-284">Wbudowane filtry rejestrują akcje i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="8bb99-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8bb99-285">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="8bb99-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="8bb99-286">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8bb99-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="8bb99-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8bb99-288">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8bb99-289">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="8bb99-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="8bb99-290">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="8bb99-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8bb99-291">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8bb99-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8bb99-292">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="8bb99-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8bb99-293">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="8bb99-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8bb99-294">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8bb99-295">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8bb99-296">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="8bb99-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8bb99-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8bb99-298">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8bb99-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8bb99-299">`CreateInstance`Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8bb99-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8bb99-300">TypeFilterAttribute</span></span>

<span data-ttu-id="8bb99-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="8bb99-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8bb99-302">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8bb99-303">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="8bb99-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8bb99-304">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8bb99-305">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8bb99-306">`TypeFilterAttribute`Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8bb99-307">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8bb99-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8bb99-308">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="8bb99-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8bb99-309">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8bb99-310">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="8bb99-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8bb99-311">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8bb99-312">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="8bb99-312">Authorization filters</span></span>

<span data-ttu-id="8bb99-313">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-313">Authorization filters:</span></span>

* <span data-ttu-id="8bb99-314">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8bb99-315">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-315">Control access to action methods.</span></span>
* <span data-ttu-id="8bb99-316">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="8bb99-317">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8bb99-318">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8bb99-319">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="8bb99-320">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-320">Calls the authorization system.</span></span>
* <span data-ttu-id="8bb99-321">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-321">Does not authorize requests.</span></span>

<span data-ttu-id="8bb99-322">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8bb99-323">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="8bb99-323">The exception will not be handled.</span></span>
* <span data-ttu-id="8bb99-324">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8bb99-325">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8bb99-326">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="8bb99-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8bb99-327">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="8bb99-327">Resource filters</span></span>

<span data-ttu-id="8bb99-328">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-328">Resource filters:</span></span>

* <span data-ttu-id="8bb99-329">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8bb99-330">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8bb99-331">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8bb99-332">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8bb99-333">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="8bb99-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8bb99-334">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-334">Resource filter examples:</span></span>

* <span data-ttu-id="8bb99-335">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="8bb99-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8bb99-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8bb99-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8bb99-337">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="8bb99-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8bb99-338">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="8bb99-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8bb99-339">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="8bb99-339">Action filters</span></span>

<span data-ttu-id="8bb99-340">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="8bb99-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="8bb99-341">Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8bb99-342">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8bb99-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8bb99-343">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-343">Action filters:</span></span>

* <span data-ttu-id="8bb99-344">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8bb99-345">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8bb99-346">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8bb99-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="8bb99-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8bb99-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>— umożliwia odczytywanie danych wejściowych do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="8bb99-349"><xref:Microsoft.AspNetCore.Mvc.Controller>-Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8bb99-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8bb99-351">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8bb99-352">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8bb99-353">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="8bb99-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8bb99-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="8bb99-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8bb99-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="8bb99-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8bb99-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8bb99-357">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="8bb99-357">Setting this property to null:</span></span>

  * <span data-ttu-id="8bb99-358">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8bb99-359">`Result`jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8bb99-360">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="8bb99-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8bb99-361">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8bb99-362">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="8bb99-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8bb99-363">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="8bb99-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8bb99-364">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="8bb99-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8bb99-365">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="8bb99-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8bb99-366">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-366">Validate model state.</span></span>
* <span data-ttu-id="8bb99-367">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="8bb99-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="8bb99-368">Kontrolery z adnotacją z `[ApiController]` atrybutem automatycznie weryfikują stan modelu i zwracają odpowiedź 400.</span><span class="sxs-lookup"><span data-stu-id="8bb99-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="8bb99-369">Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="8bb99-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="8bb99-370">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8bb99-371">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8bb99-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="8bb99-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8bb99-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8bb99-374">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="8bb99-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8bb99-375">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8bb99-376">`ActionExecutedContext.Result`jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8bb99-377">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="8bb99-377">Exception filters</span></span>

<span data-ttu-id="8bb99-378">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8bb99-378">Exception filters:</span></span>

* <span data-ttu-id="8bb99-379">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="8bb99-380">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8bb99-381">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8bb99-382">Następujący kod testuje filtr wyjątku:</span><span class="sxs-lookup"><span data-stu-id="8bb99-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="8bb99-383">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8bb99-383">Exception filters:</span></span>

* <span data-ttu-id="8bb99-384">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="8bb99-384">Don't have before and after events.</span></span>
* <span data-ttu-id="8bb99-385">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8bb99-386">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8bb99-387">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="8bb99-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8bb99-388">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="8bb99-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8bb99-389">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-389">This stops propagation of the exception.</span></span> <span data-ttu-id="8bb99-390">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="8bb99-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8bb99-391">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-391">Only an action filter can do that.</span></span>

<span data-ttu-id="8bb99-392">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8bb99-392">Exception filters:</span></span>

* <span data-ttu-id="8bb99-393">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8bb99-394">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8bb99-395">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="8bb99-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8bb99-396">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8bb99-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8bb99-397">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="8bb99-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8bb99-398">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="8bb99-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8bb99-399">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="8bb99-399">Result filters</span></span>

<span data-ttu-id="8bb99-400">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="8bb99-400">Result filters:</span></span>

* <span data-ttu-id="8bb99-401">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="8bb99-401">Implement an interface:</span></span>
  * <span data-ttu-id="8bb99-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8bb99-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8bb99-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8bb99-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8bb99-404">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8bb99-405">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8bb99-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8bb99-406">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="8bb99-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8bb99-407">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8bb99-408">Akcja zwracająca widok obejmuje wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanej operacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8bb99-409">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8bb99-410">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="8bb99-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8bb99-411">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8bb99-412">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="8bb99-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="8bb99-413">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8bb99-414">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8bb99-415"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8bb99-416">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8bb99-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8bb99-417">Zgłaszanie wyjątku w `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="8bb99-418">Zapobiega wykonywaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8bb99-419">Jest traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8bb99-420">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź prawdopodobnie została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="8bb99-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="8bb99-421">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="8bb99-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="8bb99-422">`ResultExecutedContext.Canceled`jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="8bb99-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8bb99-423">`ResultExecutedContext.Exception`ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8bb99-424">Ustawienie `Exception` wartości null skutecznie obsługuje wyjątek i uniemożliwia ponowne zgłoszenie wyjątku w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="8bb99-425">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="8bb99-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8bb99-426">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8bb99-427">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8bb99-428">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8bb99-429">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="8bb99-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8bb99-430">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8bb99-431">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8bb99-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8bb99-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8bb99-433">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="8bb99-433">This includes action results produced by:</span></span>

* <span data-ttu-id="8bb99-434">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8bb99-435">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="8bb99-435">Exception filters.</span></span>

<span data-ttu-id="8bb99-436">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="8bb99-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8bb99-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8bb99-437">IFilterFactory</span></span>

<span data-ttu-id="8bb99-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8bb99-439">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8bb99-440">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8bb99-441">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8bb99-442">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8bb99-443">`IFilterFactory`można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8bb99-444">Filtr jest stosowany w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="8bb99-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="8bb99-445">Przetestuj poprzedni kod, uruchamiając [przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="8bb99-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="8bb99-446">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="8bb99-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8bb99-447">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="8bb99-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8bb99-448">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="8bb99-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8bb99-449">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="8bb99-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="8bb99-450">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8bb99-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8bb99-451">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8bb99-451">**internal:** `My header`</span></span>

<span data-ttu-id="8bb99-452">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="8bb99-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8bb99-453">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="8bb99-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8bb99-454">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="8bb99-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8bb99-455">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="8bb99-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="8bb99-456">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8bb99-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8bb99-458">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8bb99-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8bb99-459">`CreateInstance`Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="8bb99-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8bb99-460">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8bb99-461">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8bb99-462">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8bb99-463">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8bb99-464">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego, co oznacza, że ma dostęp do kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="8bb99-465">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8bb99-466">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="8bb99-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8bb99-467">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="8bb99-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8bb99-468">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8bb99-469">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="8bb99-469">Next actions</span></span>

* <span data-ttu-id="8bb99-470">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8bb99-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8bb99-471">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="8bb99-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bb99-472">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8bb99-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8bb99-473">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="8bb99-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8bb99-474">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="8bb99-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8bb99-475">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="8bb99-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8bb99-476">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="8bb99-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8bb99-477">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8bb99-478">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8bb99-479">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="8bb99-480">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8bb99-481">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="8bb99-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="8bb99-482">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8bb99-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8bb99-483">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="8bb99-483">How filters work</span></span>

<span data-ttu-id="8bb99-484">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="8bb99-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="8bb99-485">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok wywołania akcji ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8bb99-488">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="8bb99-488">Filter types</span></span>

<span data-ttu-id="8bb99-489">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="8bb99-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8bb99-490">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8bb99-491">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie jest nieautoryzowane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="8bb99-492">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8bb99-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8bb99-493">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-493">Run after authorization.</span></span>  
  * <span data-ttu-id="8bb99-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>można uruchomić kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8bb99-495">Na przykład program `OnResourceExecuting` może uruchomić kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="8bb99-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>można uruchomić kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8bb99-497">[Filtry akcji](#action-filters) mogą uruchomić kod bezpośrednio przed i po wywołaniu pojedynczej metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="8bb99-498">Mogą one służyć do manipulowania argumentami przekazaną do akcji i wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="8bb99-499">Filtry akcji **nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="8bb99-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8bb99-500">[Filtry wyjątków](#exception-filters) są używane do stosowania zasad globalnych do nieobsłużonych wyjątków, które wystąpiły przed zapisem w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8bb99-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="8bb99-501">[Filtry wynikowe](#result-filters) mogą uruchamiać kod bezpośrednio przed i po wykonaniu poszczególnych wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="8bb99-502">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8bb99-503">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8bb99-504">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8bb99-507">Implementacja</span><span class="sxs-lookup"><span data-stu-id="8bb99-507">Implementation</span></span>

<span data-ttu-id="8bb99-508">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8bb99-509">Filtry synchroniczne mogą uruchamiać kod przed ( `On-Stage-Executing` ) i po nim ( `On-Stage-Executed` ) ich etap potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="8bb99-510">Na przykład, `OnActionExecuting` jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="8bb99-511">`OnActionExecuted`jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8bb99-512">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę:</span><span class="sxs-lookup"><span data-stu-id="8bb99-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8bb99-513">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="8bb99-514">Każda z tych `On-Stage-ExecutionAsync` metod przyjmuje `FilterType-ExecutionDelegate` etap potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8bb99-515">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="8bb99-515">Multiple filter stages</span></span>

<span data-ttu-id="8bb99-516">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8bb99-517">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje `IActionFilter` , `IResultFilter` i ich odpowiedniki asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="8bb99-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="8bb99-518">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8bb99-519">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="8bb99-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8bb99-520">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8bb99-521">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="8bb99-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8bb99-522">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> zastąpienie tylko metod synchronicznych lub metodę asynchroniczną dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8bb99-523">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-523">Built-in filter attributes</span></span>

<span data-ttu-id="8bb99-524">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="8bb99-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8bb99-525">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="8bb99-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8bb99-526">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8bb99-527">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="8bb99-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="8bb99-528">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="8bb99-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8bb99-529">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="8bb99-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8bb99-530">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="8bb99-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="8bb99-531">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="8bb99-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8bb99-532">Użycie atrybutu w akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="8bb99-533">Używanie atrybutu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="8bb99-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="8bb99-534">Globalnie dla wszystkich kontrolerów i akcji, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="8bb99-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8bb99-535">Poprzedni kod dodaje trzy filtry globalnie przy użyciu kolekcji [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="8bb99-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="8bb99-536">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="8bb99-536">Default order of execution</span></span>

<span data-ttu-id="8bb99-537">Jeśli istnieje wiele filtrów tego *samego typu*, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8bb99-538">Filtry globalne Otocz filtry klas.</span><span class="sxs-lookup"><span data-stu-id="8bb99-538">Global filters surround class filters.</span></span> <span data-ttu-id="8bb99-539">Filtry klas Otocz filtry metod.</span><span class="sxs-lookup"><span data-stu-id="8bb99-539">Class filters surround method filters.</span></span>

<span data-ttu-id="8bb99-540">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="8bb99-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8bb99-541">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="8bb99-541">The filter sequence:</span></span>

* <span data-ttu-id="8bb99-542">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="8bb99-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8bb99-543">*Przed* kodem filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="8bb99-544">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8bb99-545">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="8bb99-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8bb99-546">*Po* kodzie filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="8bb99-547">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="8bb99-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8bb99-548">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8bb99-549">Sequence</span><span class="sxs-lookup"><span data-stu-id="8bb99-549">Sequence</span></span> | <span data-ttu-id="8bb99-550">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-550">Filter scope</span></span> | <span data-ttu-id="8bb99-551">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8bb99-552">1</span><span class="sxs-lookup"><span data-stu-id="8bb99-552">1</span></span> | <span data-ttu-id="8bb99-553">Globalny</span><span class="sxs-lookup"><span data-stu-id="8bb99-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8bb99-554">2</span><span class="sxs-lookup"><span data-stu-id="8bb99-554">2</span></span> | <span data-ttu-id="8bb99-555">Kontroler</span><span class="sxs-lookup"><span data-stu-id="8bb99-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8bb99-556">3</span><span class="sxs-lookup"><span data-stu-id="8bb99-556">3</span></span> | <span data-ttu-id="8bb99-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8bb99-558">4</span><span class="sxs-lookup"><span data-stu-id="8bb99-558">4</span></span> | <span data-ttu-id="8bb99-559">Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8bb99-560">5</span><span class="sxs-lookup"><span data-stu-id="8bb99-560">5</span></span> | <span data-ttu-id="8bb99-561">Kontroler</span><span class="sxs-lookup"><span data-stu-id="8bb99-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8bb99-562">6</span><span class="sxs-lookup"><span data-stu-id="8bb99-562">6</span></span> | <span data-ttu-id="8bb99-563">Globalny</span><span class="sxs-lookup"><span data-stu-id="8bb99-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="8bb99-564">Ta sekwencja pokazuje:</span><span class="sxs-lookup"><span data-stu-id="8bb99-564">This sequence shows:</span></span>

* <span data-ttu-id="8bb99-565">Filtr metody jest zagnieżdżony w obrębie filtru kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="8bb99-566">Filtr kontrolera jest zagnieżdżony w obrębie filtru globalnego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="8bb99-567">Filtry na Razor poziomie kontrolera i strony</span><span class="sxs-lookup"><span data-stu-id="8bb99-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="8bb99-568">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8bb99-569">Te metody:</span><span class="sxs-lookup"><span data-stu-id="8bb99-569">These methods:</span></span>

* <span data-ttu-id="8bb99-570">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8bb99-571">`OnActionExecuting`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8bb99-572">`OnActionExecuted`jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8bb99-573">`OnActionExecutionAsync`jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8bb99-574">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8bb99-575">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8bb99-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="8bb99-576">The `TestController`:</span></span>

* <span data-ttu-id="8bb99-577">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8bb99-578">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="8bb99-579">Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8bb99-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8bb99-580">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="8bb99-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8bb99-581">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="8bb99-581">Overriding the default order</span></span>

<span data-ttu-id="8bb99-582">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8bb99-583">`IOrderedFilter`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8bb99-584">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="8bb99-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8bb99-585">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8bb99-586">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="8bb99-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8bb99-587">`Order`Właściwość można ustawić przy użyciu parametru konstruktora:</span><span class="sxs-lookup"><span data-stu-id="8bb99-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="8bb99-588">Należy wziąć pod uwagę te same 3 filtry akcji, które przedstawiono w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="8bb99-589">Jeśli `Order` Właściwość kontrolera i filtry globalne zostały odpowiednio ustawione na 1 i 2, kolejność wykonywania zostanie odwrócona.</span><span class="sxs-lookup"><span data-stu-id="8bb99-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="8bb99-590">Sequence</span><span class="sxs-lookup"><span data-stu-id="8bb99-590">Sequence</span></span> | <span data-ttu-id="8bb99-591">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-591">Filter scope</span></span> | <span data-ttu-id="8bb99-592">`Order`wartość</span><span class="sxs-lookup"><span data-stu-id="8bb99-592">`Order` property</span></span> | <span data-ttu-id="8bb99-593">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="8bb99-594">1</span><span class="sxs-lookup"><span data-stu-id="8bb99-594">1</span></span> | <span data-ttu-id="8bb99-595">Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-595">Method</span></span> | <span data-ttu-id="8bb99-596">0</span><span class="sxs-lookup"><span data-stu-id="8bb99-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8bb99-597">2</span><span class="sxs-lookup"><span data-stu-id="8bb99-597">2</span></span> | <span data-ttu-id="8bb99-598">Kontroler</span><span class="sxs-lookup"><span data-stu-id="8bb99-598">Controller</span></span> | <span data-ttu-id="8bb99-599">1</span><span class="sxs-lookup"><span data-stu-id="8bb99-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8bb99-600">3</span><span class="sxs-lookup"><span data-stu-id="8bb99-600">3</span></span> | <span data-ttu-id="8bb99-601">Globalny</span><span class="sxs-lookup"><span data-stu-id="8bb99-601">Global</span></span> | <span data-ttu-id="8bb99-602">2</span><span class="sxs-lookup"><span data-stu-id="8bb99-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8bb99-603">4</span><span class="sxs-lookup"><span data-stu-id="8bb99-603">4</span></span> | <span data-ttu-id="8bb99-604">Globalny</span><span class="sxs-lookup"><span data-stu-id="8bb99-604">Global</span></span> | <span data-ttu-id="8bb99-605">2</span><span class="sxs-lookup"><span data-stu-id="8bb99-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8bb99-606">5</span><span class="sxs-lookup"><span data-stu-id="8bb99-606">5</span></span> | <span data-ttu-id="8bb99-607">Kontroler</span><span class="sxs-lookup"><span data-stu-id="8bb99-607">Controller</span></span> | <span data-ttu-id="8bb99-608">1</span><span class="sxs-lookup"><span data-stu-id="8bb99-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8bb99-609">6</span><span class="sxs-lookup"><span data-stu-id="8bb99-609">6</span></span> | <span data-ttu-id="8bb99-610">Metoda</span><span class="sxs-lookup"><span data-stu-id="8bb99-610">Method</span></span> | <span data-ttu-id="8bb99-611">0</span><span class="sxs-lookup"><span data-stu-id="8bb99-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="8bb99-612">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="8bb99-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8bb99-613">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="8bb99-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8bb99-614">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="8bb99-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8bb99-615">W przypadku filtrów wbudowanych zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8bb99-616">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="8bb99-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8bb99-617">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="8bb99-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8bb99-618">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="8bb99-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8bb99-619">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8bb99-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8bb99-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8bb99-621">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8bb99-622">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8bb99-623">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8bb99-624">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="8bb99-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8bb99-625">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="8bb99-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="8bb99-626">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="8bb99-626">Dependency injection</span></span>

<span data-ttu-id="8bb99-627">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8bb99-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8bb99-628">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8bb99-629">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="8bb99-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8bb99-630">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="8bb99-630">A type-activated filter means:</span></span>

* <span data-ttu-id="8bb99-631">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-631">An instance is created for each request.</span></span>
* <span data-ttu-id="8bb99-632">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="8bb99-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8bb99-633">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="8bb99-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8bb99-634">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="8bb99-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8bb99-635">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8bb99-636">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8bb99-637">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="8bb99-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8bb99-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8bb99-639">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8bb99-640">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-640">Loggers are available from DI.</span></span> <span data-ttu-id="8bb99-641">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8bb99-642">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8bb99-643">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-643">Logging added to filters:</span></span>

* <span data-ttu-id="8bb99-644">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="8bb99-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8bb99-645">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="8bb99-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8bb99-646">Wbudowane filtry akcje dziennika i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="8bb99-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8bb99-647">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="8bb99-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="8bb99-648">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8bb99-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="8bb99-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8bb99-650">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8bb99-651">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="8bb99-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="8bb99-652">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="8bb99-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8bb99-653">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8bb99-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8bb99-654">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="8bb99-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8bb99-655">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="8bb99-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8bb99-656">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8bb99-657">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8bb99-658">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="8bb99-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8bb99-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8bb99-660">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8bb99-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8bb99-661">`CreateInstance`Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8bb99-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8bb99-662">TypeFilterAttribute</span></span>

<span data-ttu-id="8bb99-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="8bb99-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8bb99-664">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8bb99-665">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="8bb99-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8bb99-666">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8bb99-667">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8bb99-668">`TypeFilterAttribute`Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8bb99-669">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8bb99-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8bb99-670">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="8bb99-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8bb99-671">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8bb99-672">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="8bb99-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8bb99-673">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8bb99-674">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="8bb99-674">Authorization filters</span></span>

<span data-ttu-id="8bb99-675">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-675">Authorization filters:</span></span>

* <span data-ttu-id="8bb99-676">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8bb99-677">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-677">Control access to action methods.</span></span>
* <span data-ttu-id="8bb99-678">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="8bb99-679">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8bb99-680">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8bb99-681">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="8bb99-682">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-682">Calls the authorization system.</span></span>
* <span data-ttu-id="8bb99-683">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-683">Does not authorize requests.</span></span>

<span data-ttu-id="8bb99-684">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8bb99-685">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="8bb99-685">The exception will not be handled.</span></span>
* <span data-ttu-id="8bb99-686">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8bb99-687">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8bb99-688">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="8bb99-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8bb99-689">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="8bb99-689">Resource filters</span></span>

<span data-ttu-id="8bb99-690">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-690">Resource filters:</span></span>

* <span data-ttu-id="8bb99-691">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8bb99-692">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8bb99-693">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8bb99-694">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8bb99-695">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="8bb99-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8bb99-696">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-696">Resource filter examples:</span></span>

* <span data-ttu-id="8bb99-697">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="8bb99-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8bb99-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8bb99-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8bb99-699">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="8bb99-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8bb99-700">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="8bb99-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8bb99-701">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="8bb99-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8bb99-702">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="8bb99-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="8bb99-703">Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8bb99-704">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8bb99-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8bb99-705">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-705">Action filters:</span></span>

* <span data-ttu-id="8bb99-706">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8bb99-707">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8bb99-708">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8bb99-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="8bb99-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8bb99-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-umożliwia odczytywanie danych wejściowych metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="8bb99-711"><xref:Microsoft.AspNetCore.Mvc.Controller>-Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8bb99-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8bb99-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8bb99-713">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8bb99-714">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8bb99-715">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="8bb99-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8bb99-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="8bb99-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8bb99-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="8bb99-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8bb99-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8bb99-719">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="8bb99-719">Setting this property to null:</span></span>

  * <span data-ttu-id="8bb99-720">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8bb99-721">`Result`jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8bb99-722">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="8bb99-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8bb99-723">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8bb99-724">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="8bb99-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8bb99-725">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="8bb99-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8bb99-726">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="8bb99-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8bb99-727">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="8bb99-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8bb99-728">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-728">Validate model state.</span></span>
* <span data-ttu-id="8bb99-729">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="8bb99-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="8bb99-730">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8bb99-731">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8bb99-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="8bb99-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8bb99-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8bb99-734">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="8bb99-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8bb99-735">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8bb99-736">`ActionExecutedContext.Result`jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8bb99-737">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="8bb99-737">Exception filters</span></span>

<span data-ttu-id="8bb99-738">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8bb99-738">Exception filters:</span></span>

* <span data-ttu-id="8bb99-739">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="8bb99-740">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8bb99-741">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8bb99-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8bb99-742">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8bb99-742">Exception filters:</span></span>

* <span data-ttu-id="8bb99-743">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="8bb99-743">Don't have before and after events.</span></span>
* <span data-ttu-id="8bb99-744">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8bb99-745">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8bb99-746">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="8bb99-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8bb99-747">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="8bb99-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8bb99-748">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-748">This stops propagation of the exception.</span></span> <span data-ttu-id="8bb99-749">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="8bb99-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8bb99-750">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-750">Only an action filter can do that.</span></span>

<span data-ttu-id="8bb99-751">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8bb99-751">Exception filters:</span></span>

* <span data-ttu-id="8bb99-752">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8bb99-753">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8bb99-754">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="8bb99-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8bb99-755">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8bb99-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8bb99-756">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="8bb99-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8bb99-757">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="8bb99-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8bb99-758">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="8bb99-758">Result filters</span></span>

<span data-ttu-id="8bb99-759">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="8bb99-759">Result filters:</span></span>

* <span data-ttu-id="8bb99-760">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="8bb99-760">Implement an interface:</span></span>
  * <span data-ttu-id="8bb99-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8bb99-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8bb99-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8bb99-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8bb99-763">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8bb99-764">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8bb99-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8bb99-765">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="8bb99-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8bb99-766">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8bb99-767">Akcja zwracająca widok będzie obejmować wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanych operacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8bb99-768">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8bb99-769">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="8bb99-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8bb99-770">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8bb99-771">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="8bb99-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="8bb99-772">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8bb99-773">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8bb99-774"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8bb99-775">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8bb99-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8bb99-776">Zgłaszanie wyjątku w programie `IResultFilter.OnResultExecuting` spowoduje:</span><span class="sxs-lookup"><span data-stu-id="8bb99-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="8bb99-777">Zapobiegaj wykonaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="8bb99-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8bb99-778">Być traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="8bb99-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8bb99-779">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="8bb99-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="8bb99-780">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="8bb99-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="8bb99-781">`ResultExecutedContext.Canceled`jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="8bb99-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8bb99-782">`ResultExecutedContext.Exception`ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8bb99-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8bb99-783">Ustawienie `Exception` wartości null powoduje skuteczną obsługę wyjątku i uniemożliwia ponowne zgłoszenie wyjątku przez ASP.NET Core w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="8bb99-784">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="8bb99-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8bb99-785">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="8bb99-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8bb99-786">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="8bb99-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8bb99-787">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8bb99-788">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="8bb99-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8bb99-789">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="8bb99-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8bb99-790">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8bb99-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8bb99-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8bb99-792">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="8bb99-792">This includes action results produced by:</span></span>

* <span data-ttu-id="8bb99-793">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8bb99-794">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="8bb99-794">Exception filters.</span></span>

<span data-ttu-id="8bb99-795">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="8bb99-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8bb99-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8bb99-796">IFilterFactory</span></span>

<span data-ttu-id="8bb99-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8bb99-798">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8bb99-799">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8bb99-800">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="8bb99-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8bb99-801">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8bb99-802">`IFilterFactory`można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="8bb99-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8bb99-803">Poprzedni kod może być testowany przez uruchomienie [przykładu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="8bb99-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="8bb99-804">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="8bb99-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8bb99-805">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="8bb99-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8bb99-806">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="8bb99-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8bb99-807">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="8bb99-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="8bb99-808">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8bb99-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8bb99-809">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8bb99-809">**internal:** `My header`</span></span>

<span data-ttu-id="8bb99-810">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="8bb99-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8bb99-811">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="8bb99-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8bb99-812">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="8bb99-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8bb99-813">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="8bb99-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="8bb99-814">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="8bb99-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8bb99-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="8bb99-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8bb99-816">`IFilterFactory`uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8bb99-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8bb99-817">`CreateInstance`Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="8bb99-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8bb99-818">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="8bb99-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8bb99-819">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="8bb99-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8bb99-820">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="8bb99-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8bb99-821">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8bb99-822">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego ASP.NET Core, co oznacza, że ma dostęp do ASP.NET Core kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="8bb99-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="8bb99-823">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="8bb99-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8bb99-824">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="8bb99-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8bb99-825">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="8bb99-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8bb99-826">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="8bb99-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8bb99-827">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="8bb99-827">Next actions</span></span>

* <span data-ttu-id="8bb99-828">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8bb99-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8bb99-829">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="8bb99-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
