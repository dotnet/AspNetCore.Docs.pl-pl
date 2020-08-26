---
title: Filtry w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak działają filtry i jak korzystać z nich w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
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
uid: mvc/controllers/filters
ms.openlocfilehash: c97e3afbbf94a4cb721c5d814a377eec3e26a03b
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865404"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="ba6a2-103">Filtry w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba6a2-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba6a2-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ba6a2-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ba6a2-105">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ba6a2-106">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ba6a2-107">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ba6a2-108">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ba6a2-109">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ba6a2-110">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ba6a2-111">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="ba6a2-112">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ba6a2-113">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="ba6a2-114">Filtry nie działają bezpośrednio ze [ Razor składnikami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="ba6a2-115">Filtr może mieć tylko pośredni wpływ na składnik, gdy:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="ba6a2-116">Składnik jest osadzony na stronie lub widoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="ba6a2-117">Strona lub kontroler/widok używają filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="ba6a2-118">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ba6a2-119">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="ba6a2-119">How filters work</span></span>

<span data-ttu-id="ba6a2-120">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="ba6a2-121">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok akcji wywołania.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ba6a2-124">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="ba6a2-124">Filter types</span></span>

<span data-ttu-id="ba6a2-125">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ba6a2-126">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ba6a2-127">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie nie jest autoryzowane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="ba6a2-128">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ba6a2-129">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-129">Run after authorization.</span></span>  
  * <span data-ttu-id="ba6a2-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> uruchamia kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ba6a2-131">Na przykład `OnResourceExecuting` uruchamia kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="ba6a2-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> uruchamia kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ba6a2-133">[Filtry akcji](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="ba6a2-134">Uruchom kod bezpośrednio przed i po wywołaniu metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="ba6a2-135">Może zmienić argumenty przekazane do akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="ba6a2-136">Można zmienić wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="ba6a2-137">**Nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ba6a2-138">[Filtry wyjątków](#exception-filters) stosują zasady globalne do nieobsłużonych wyjątków, które występują przed zapisaniem treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="ba6a2-139">[Filtry wyników](#result-filters) uruchamiają kod bezpośrednio przed i po wykonaniu wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="ba6a2-140">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ba6a2-141">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ba6a2-142">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ba6a2-145">Implementacja</span><span class="sxs-lookup"><span data-stu-id="ba6a2-145">Implementation</span></span>

<span data-ttu-id="ba6a2-146">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ba6a2-147">Filtry synchroniczne uruchamiają kod przed i po fazie potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="ba6a2-148">Na przykład, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="ba6a2-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba6a2-150">W poprzednim kodzie funkcja myFunction [jest funkcją](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) narzędziową w [przykładowym pobieraniu](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="ba6a2-151">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="ba6a2-152">Na przykład <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ba6a2-153">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ba6a2-154">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="ba6a2-154">Multiple filter stages</span></span>

<span data-ttu-id="ba6a2-155">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ba6a2-156">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="ba6a2-157">Synchroniczne: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> i  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba6a2-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="ba6a2-158">Asynchroniczny: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba6a2-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="ba6a2-159">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ba6a2-160">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ba6a2-161">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ba6a2-162">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ba6a2-163">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , Zastąp tylko metody synchroniczne lub metody asynchroniczne dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ba6a2-164">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-164">Built-in filter attributes</span></span>

<span data-ttu-id="ba6a2-165">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ba6a2-166">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ba6a2-167">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ba6a2-168">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="ba6a2-169">Użyj narzędzia, takiego jak [Narzędzia deweloperskie przeglądarki](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="ba6a2-170">W obszarze **nagłówki odpowiedzi** `author: Rick Anderson` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="ba6a2-171">Poniższy kod implementuje `ActionFilterAttribute` , że:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="ba6a2-172">Odczytuje tytuł i nazwę z systemu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="ba6a2-173">W przeciwieństwie do poprzedniego przykładu, poniższy kod nie wymaga dodania parametrów filtru do kodu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="ba6a2-174">Dodaje tytuł i nazwę do nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ba6a2-175">Opcje konfiguracji są dostępne z [systemu konfiguracji](xref:fundamentals/configuration/index) przy użyciu [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ba6a2-176">Na przykład, z *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="ba6a2-177">W `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="ba6a2-178">`PositionOptions`Klasa jest dodawana do kontenera usługi z `"Position"` obszarem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="ba6a2-179">`MyActionFilterAttribute`Zostanie dodany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="ba6a2-180">Poniższy kod przedstawia `PositionOptions` klasę:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="ba6a2-181">Poniższy kod stosuje `MyActionFilterAttribute` `Index2` metodę do:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="ba6a2-182">W obszarze **nagłówki odpowiedzi**, `author: Rick Anderson` i `Editor: Joe Smith` jest wyświetlana po `Sample/Index2` wywołaniu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="ba6a2-183">Poniższy kod stosuje `MyActionFilterAttribute` `AddHeaderAttribute` stronę i do Razor strony:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ba6a2-184">Nie można zastosować filtrów do Razor metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="ba6a2-185">Mogą być stosowane do Razor modelu strony lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="ba6a2-186">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ba6a2-187">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ba6a2-188">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="ba6a2-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="ba6a2-189">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ba6a2-190">Użycie atrybutu w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="ba6a2-191">Atrybutów filtru nie można stosować do Razor metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="ba6a2-192">Użycie atrybutu na kontrolerze lub Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="ba6a2-193">Globalnie dla wszystkich kontrolerów, akcji i Razor stron, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="ba6a2-194">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="ba6a2-194">Default order of execution</span></span>

<span data-ttu-id="ba6a2-195">Jeśli istnieje wiele filtrów dla określonego etapu potoku, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ba6a2-196">Filtry globalne Otocz filtry klas, które z kolei filtrują metody przestrzenne.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="ba6a2-197">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ba6a2-198">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-198">The filter sequence:</span></span>

* <span data-ttu-id="ba6a2-199">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ba6a2-200">*Przed* kodem i Razor filtrami stron.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="ba6a2-201">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ba6a2-202">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ba6a2-203">*Po* kodzie kontrolera i Razor filtrów strony.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="ba6a2-204">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ba6a2-205">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ba6a2-206">Sequence</span><span class="sxs-lookup"><span data-stu-id="ba6a2-206">Sequence</span></span> | <span data-ttu-id="ba6a2-207">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-207">Filter scope</span></span> | <span data-ttu-id="ba6a2-208">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ba6a2-209">1</span><span class="sxs-lookup"><span data-stu-id="ba6a2-209">1</span></span> | <span data-ttu-id="ba6a2-210">Globalnie</span><span class="sxs-lookup"><span data-stu-id="ba6a2-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-211">2</span><span class="sxs-lookup"><span data-stu-id="ba6a2-211">2</span></span> | <span data-ttu-id="ba6a2-212">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="ba6a2-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="ba6a2-213">3</span><span class="sxs-lookup"><span data-stu-id="ba6a2-213">3</span></span> | <span data-ttu-id="ba6a2-214">Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-215">4</span><span class="sxs-lookup"><span data-stu-id="ba6a2-215">4</span></span> | <span data-ttu-id="ba6a2-216">Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba6a2-217">5</span><span class="sxs-lookup"><span data-stu-id="ba6a2-217">5</span></span> | <span data-ttu-id="ba6a2-218">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="ba6a2-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba6a2-219">6</span><span class="sxs-lookup"><span data-stu-id="ba6a2-219">6</span></span> | <span data-ttu-id="ba6a2-220">Globalnie</span><span class="sxs-lookup"><span data-stu-id="ba6a2-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="ba6a2-221">Filtry na poziomie kontrolera</span><span class="sxs-lookup"><span data-stu-id="ba6a2-221">Controller level filters</span></span>

<span data-ttu-id="ba6a2-222">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ba6a2-223">Te metody:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-223">These methods:</span></span>

* <span data-ttu-id="ba6a2-224">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ba6a2-225">`OnActionExecuting` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ba6a2-226">`OnActionExecuted` jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ba6a2-227">`OnActionExecutionAsync` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ba6a2-228">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ba6a2-229">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ba6a2-230">Pomocnik `TestController`:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-230">The `TestController`:</span></span>

* <span data-ttu-id="ba6a2-231">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="ba6a2-232">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="ba6a2-233">Nawigowanie w celu `https://localhost:5001/Test2/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-233">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ba6a2-234">Filtry na poziomie kontrolera ustawiają Właściwość [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="ba6a2-235">Filtrów na poziomie kontrolera **nie** można ustawić do uruchomienia po zastosowaniu filtrów do metod.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="ba6a2-236">Klauzula Order została omówiona w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-236">Order is explained in the next section.</span></span>

<span data-ttu-id="ba6a2-237">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ba6a2-238">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="ba6a2-238">Overriding the default order</span></span>

<span data-ttu-id="ba6a2-239">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ba6a2-240">`IOrderedFilter` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ba6a2-241">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ba6a2-242">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ba6a2-243">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ba6a2-244">`Order`Właściwość jest ustawiona z parametrem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="ba6a2-245">Należy wziąć pod uwagę dwa filtry akcji na następującym kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ba6a2-246">Filtr globalny zostanie dodany w `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="ba6a2-247">3 filtry działają w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="ba6a2-248">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ba6a2-249">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ba6a2-250">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ba6a2-251">Jak wspomniano wcześniej, filtry na poziomie kontrolera [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ustawiają Właściwość Order `int.MinValue` dla filtrów wbudowanych, zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="ba6a2-252">W poprzednim kodzie `MySampleActionFilter` ma zakres globalny, dlatego działa wcześniej `MyAction2FilterAttribute` , który ma zakres kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="ba6a2-253">Aby `MyAction2FilterAttribute` najpierw wykonać przebieg, ustaw kolejność na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="ba6a2-254">Aby najpierw uruchomić filtr globalny `MySampleActionFilter` , ustaw opcję `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ba6a2-255">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="ba6a2-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ba6a2-256">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ba6a2-257">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ba6a2-258">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ba6a2-259">Pomocnik `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ba6a2-260">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ba6a2-261">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ba6a2-262">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ba6a2-263">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ba6a2-264">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="ba6a2-265">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="ba6a2-265">Dependency injection</span></span>

<span data-ttu-id="ba6a2-266">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ba6a2-267">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ba6a2-268">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ba6a2-269">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-269">A type-activated filter means:</span></span>

* <span data-ttu-id="ba6a2-270">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-270">An instance is created for each request.</span></span>
* <span data-ttu-id="ba6a2-271">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ba6a2-272">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ba6a2-273">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ba6a2-274">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ba6a2-275">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ba6a2-276">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ba6a2-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ba6a2-278">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ba6a2-279">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-279">Loggers are available from DI.</span></span> <span data-ttu-id="ba6a2-280">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ba6a2-281">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ba6a2-282">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-282">Logging added to filters:</span></span>

* <span data-ttu-id="ba6a2-283">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ba6a2-284">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ba6a2-285">Wbudowane filtry rejestrują akcje i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ba6a2-286">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="ba6a2-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="ba6a2-287">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ba6a2-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ba6a2-289">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba6a2-290">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="ba6a2-291">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ba6a2-292">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ba6a2-293">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba6a2-294">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ba6a2-295">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ba6a2-296">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ba6a2-297">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ba6a2-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba6a2-299">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba6a2-300">`CreateInstance` Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ba6a2-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ba6a2-301">TypeFilterAttribute</span></span>

<span data-ttu-id="ba6a2-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ba6a2-303">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ba6a2-304">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ba6a2-305">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ba6a2-306">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ba6a2-307">`TypeFilterAttribute` Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ba6a2-308">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ba6a2-309">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba6a2-310">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ba6a2-311">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ba6a2-312">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ba6a2-313">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="ba6a2-313">Authorization filters</span></span>

<span data-ttu-id="ba6a2-314">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-314">Authorization filters:</span></span>

* <span data-ttu-id="ba6a2-315">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ba6a2-316">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-316">Control access to action methods.</span></span>
* <span data-ttu-id="ba6a2-317">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="ba6a2-318">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ba6a2-319">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ba6a2-320">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="ba6a2-321">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-321">Calls the authorization system.</span></span>
* <span data-ttu-id="ba6a2-322">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-322">Does not authorize requests.</span></span>

<span data-ttu-id="ba6a2-323">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ba6a2-324">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-324">The exception will not be handled.</span></span>
* <span data-ttu-id="ba6a2-325">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ba6a2-326">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ba6a2-327">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ba6a2-328">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="ba6a2-328">Resource filters</span></span>

<span data-ttu-id="ba6a2-329">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-329">Resource filters:</span></span>

* <span data-ttu-id="ba6a2-330">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ba6a2-331">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ba6a2-332">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ba6a2-333">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ba6a2-334">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ba6a2-335">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-335">Resource filter examples:</span></span>

* <span data-ttu-id="ba6a2-336">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ba6a2-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ba6a2-338">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ba6a2-339">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ba6a2-340">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="ba6a2-340">Action filters</span></span>

<span data-ttu-id="ba6a2-341">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="ba6a2-342">Razor Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ba6a2-343">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ba6a2-344">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-344">Action filters:</span></span>

* <span data-ttu-id="ba6a2-345">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ba6a2-346">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ba6a2-347">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba6a2-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ba6a2-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> — umożliwia odczytywanie danych wejściowych do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="ba6a2-350"><xref:Microsoft.AspNetCore.Mvc.Controller> -Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ba6a2-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> -Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ba6a2-352">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ba6a2-353">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ba6a2-354">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba6a2-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ba6a2-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba6a2-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> -O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ba6a2-358">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-358">Setting this property to null:</span></span>

  * <span data-ttu-id="ba6a2-359">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ba6a2-360">`Result` jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ba6a2-361">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ba6a2-362">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ba6a2-363">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ba6a2-364">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ba6a2-365">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ba6a2-366">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ba6a2-367">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-367">Validate model state.</span></span>
* <span data-ttu-id="ba6a2-368">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="ba6a2-369">Kontrolery z adnotacją z `[ApiController]` atrybutem automatycznie weryfikują stan modelu i zwracają odpowiedź 400.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="ba6a2-370">Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="ba6a2-371">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ba6a2-372">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ba6a2-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba6a2-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ba6a2-375">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ba6a2-376">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ba6a2-377">`ActionExecutedContext.Result` jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ba6a2-378">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="ba6a2-378">Exception filters</span></span>

<span data-ttu-id="ba6a2-379">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-379">Exception filters:</span></span>

* <span data-ttu-id="ba6a2-380">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="ba6a2-381">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ba6a2-382">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ba6a2-383">Następujący kod testuje filtr wyjątku:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="ba6a2-384">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-384">Exception filters:</span></span>

* <span data-ttu-id="ba6a2-385">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-385">Don't have before and after events.</span></span>
* <span data-ttu-id="ba6a2-386">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ba6a2-387">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ba6a2-388">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ba6a2-389">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ba6a2-390">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-390">This stops propagation of the exception.</span></span> <span data-ttu-id="ba6a2-391">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="ba6a2-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ba6a2-392">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-392">Only an action filter can do that.</span></span>

<span data-ttu-id="ba6a2-393">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-393">Exception filters:</span></span>

* <span data-ttu-id="ba6a2-394">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ba6a2-395">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ba6a2-396">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ba6a2-397">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ba6a2-398">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ba6a2-399">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ba6a2-400">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="ba6a2-400">Result filters</span></span>

<span data-ttu-id="ba6a2-401">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-401">Result filters:</span></span>

* <span data-ttu-id="ba6a2-402">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-402">Implement an interface:</span></span>
  * <span data-ttu-id="ba6a2-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba6a2-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ba6a2-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba6a2-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ba6a2-405">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ba6a2-406">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba6a2-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ba6a2-407">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba6a2-408">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ba6a2-409">Akcja zwracająca widok obejmuje wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanej operacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ba6a2-410">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ba6a2-411">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ba6a2-412">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ba6a2-413">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="ba6a2-414">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ba6a2-415">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ba6a2-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ba6a2-417">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ba6a2-418">Zgłaszanie wyjątku w `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="ba6a2-419">Zapobiega wykonywaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ba6a2-420">Jest traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba6a2-421">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź prawdopodobnie została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="ba6a2-422">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="ba6a2-423">`ResultExecutedContext.Canceled` jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ba6a2-424">`ResultExecutedContext.Exception` ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ba6a2-425">Ustawienie `Exception` wartości null skutecznie obsługuje wyjątek i uniemożliwia ponowne zgłoszenie wyjątku w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="ba6a2-426">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ba6a2-427">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ba6a2-428">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ba6a2-429">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ba6a2-430">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ba6a2-431">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ba6a2-432">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba6a2-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ba6a2-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ba6a2-434">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-434">This includes action results produced by:</span></span>

* <span data-ttu-id="ba6a2-435">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ba6a2-436">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-436">Exception filters.</span></span>

<span data-ttu-id="ba6a2-437">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ba6a2-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ba6a2-438">IFilterFactory</span></span>

<span data-ttu-id="ba6a2-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ba6a2-440">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ba6a2-441">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ba6a2-442">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ba6a2-443">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ba6a2-444">`IFilterFactory` można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ba6a2-445">Filtr jest stosowany w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="ba6a2-446">Przetestuj poprzedni kod, uruchamiając [przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="ba6a2-447">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ba6a2-448">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="ba6a2-449">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ba6a2-450">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="ba6a2-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="ba6a2-451">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ba6a2-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ba6a2-452">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="ba6a2-452">**internal:** `My header`</span></span>

<span data-ttu-id="ba6a2-453">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ba6a2-454">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="ba6a2-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ba6a2-455">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ba6a2-456">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="ba6a2-457">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ba6a2-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba6a2-459">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba6a2-460">`CreateInstance` Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ba6a2-461">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ba6a2-462">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ba6a2-463">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ba6a2-464">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ba6a2-465">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego, co oznacza, że ma dostęp do kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="ba6a2-466">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ba6a2-467">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="ba6a2-468">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ba6a2-469">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ba6a2-470">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="ba6a2-470">Next actions</span></span>

* <span data-ttu-id="ba6a2-471">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="ba6a2-472">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba6a2-473">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ba6a2-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ba6a2-474">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ba6a2-475">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ba6a2-476">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ba6a2-477">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ba6a2-478">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ba6a2-479">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ba6a2-480">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="ba6a2-481">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ba6a2-482">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="ba6a2-483">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ba6a2-484">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="ba6a2-484">How filters work</span></span>

<span data-ttu-id="ba6a2-485">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="ba6a2-486">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok wywołania akcji ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ba6a2-489">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="ba6a2-489">Filter types</span></span>

<span data-ttu-id="ba6a2-490">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ba6a2-491">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ba6a2-492">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie jest nieautoryzowane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="ba6a2-493">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ba6a2-494">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-494">Run after authorization.</span></span>  
  * <span data-ttu-id="ba6a2-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> można uruchomić kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ba6a2-496">Na przykład program `OnResourceExecuting` może uruchomić kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="ba6a2-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> można uruchomić kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ba6a2-498">[Filtry akcji](#action-filters) mogą uruchomić kod bezpośrednio przed i po wywołaniu pojedynczej metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="ba6a2-499">Mogą one służyć do manipulowania argumentami przekazaną do akcji i wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="ba6a2-500">Filtry akcji **nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ba6a2-501">[Filtry wyjątków](#exception-filters) są używane do stosowania zasad globalnych do nieobsłużonych wyjątków, które wystąpiły przed zapisem w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="ba6a2-502">[Filtry wynikowe](#result-filters) mogą uruchamiać kod bezpośrednio przed i po wykonaniu poszczególnych wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="ba6a2-503">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ba6a2-504">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ba6a2-505">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ba6a2-508">Implementacja</span><span class="sxs-lookup"><span data-stu-id="ba6a2-508">Implementation</span></span>

<span data-ttu-id="ba6a2-509">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ba6a2-510">Filtry synchroniczne mogą uruchamiać kod przed ( `On-Stage-Executing` ) i po nim ( `On-Stage-Executed` ) ich etap potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="ba6a2-511">Na przykład, `OnActionExecuting` jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="ba6a2-512">`OnActionExecuted` jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba6a2-513">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ba6a2-514">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="ba6a2-515">Każda z tych `On-Stage-ExecutionAsync` metod przyjmuje `FilterType-ExecutionDelegate` etap potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ba6a2-516">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="ba6a2-516">Multiple filter stages</span></span>

<span data-ttu-id="ba6a2-517">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ba6a2-518">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje `IActionFilter` , `IResultFilter` i ich odpowiedniki asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="ba6a2-519">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ba6a2-520">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ba6a2-521">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ba6a2-522">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ba6a2-523">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> zastąpienie tylko metod synchronicznych lub metodę asynchroniczną dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ba6a2-524">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-524">Built-in filter attributes</span></span>

<span data-ttu-id="ba6a2-525">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ba6a2-526">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ba6a2-527">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ba6a2-528">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="ba6a2-529">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ba6a2-530">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ba6a2-531">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="ba6a2-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="ba6a2-532">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ba6a2-533">Użycie atrybutu w akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="ba6a2-534">Używanie atrybutu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="ba6a2-535">Globalnie dla wszystkich kontrolerów i akcji, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="ba6a2-536">Poprzedni kod dodaje trzy filtry globalnie przy użyciu kolekcji [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="ba6a2-537">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="ba6a2-537">Default order of execution</span></span>

<span data-ttu-id="ba6a2-538">Jeśli istnieje wiele filtrów tego *samego typu*, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ba6a2-539">Filtry globalne Otocz filtry klas.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-539">Global filters surround class filters.</span></span> <span data-ttu-id="ba6a2-540">Filtry klas Otocz filtry metod.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-540">Class filters surround method filters.</span></span>

<span data-ttu-id="ba6a2-541">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ba6a2-542">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-542">The filter sequence:</span></span>

* <span data-ttu-id="ba6a2-543">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ba6a2-544">*Przed* kodem filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="ba6a2-545">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ba6a2-546">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ba6a2-547">*Po* kodzie filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="ba6a2-548">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ba6a2-549">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ba6a2-550">Sequence</span><span class="sxs-lookup"><span data-stu-id="ba6a2-550">Sequence</span></span> | <span data-ttu-id="ba6a2-551">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-551">Filter scope</span></span> | <span data-ttu-id="ba6a2-552">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ba6a2-553">1</span><span class="sxs-lookup"><span data-stu-id="ba6a2-553">1</span></span> | <span data-ttu-id="ba6a2-554">Globalnie</span><span class="sxs-lookup"><span data-stu-id="ba6a2-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-555">2</span><span class="sxs-lookup"><span data-stu-id="ba6a2-555">2</span></span> | <span data-ttu-id="ba6a2-556">Kontroler</span><span class="sxs-lookup"><span data-stu-id="ba6a2-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-557">3</span><span class="sxs-lookup"><span data-stu-id="ba6a2-557">3</span></span> | <span data-ttu-id="ba6a2-558">Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-559">4</span><span class="sxs-lookup"><span data-stu-id="ba6a2-559">4</span></span> | <span data-ttu-id="ba6a2-560">Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba6a2-561">5</span><span class="sxs-lookup"><span data-stu-id="ba6a2-561">5</span></span> | <span data-ttu-id="ba6a2-562">Kontroler</span><span class="sxs-lookup"><span data-stu-id="ba6a2-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba6a2-563">6</span><span class="sxs-lookup"><span data-stu-id="ba6a2-563">6</span></span> | <span data-ttu-id="ba6a2-564">Globalnie</span><span class="sxs-lookup"><span data-stu-id="ba6a2-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="ba6a2-565">Ta sekwencja pokazuje:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-565">This sequence shows:</span></span>

* <span data-ttu-id="ba6a2-566">Filtr metody jest zagnieżdżony w obrębie filtru kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="ba6a2-567">Filtr kontrolera jest zagnieżdżony w obrębie filtru globalnego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="ba6a2-568">Filtry na Razor poziomie kontrolera i strony</span><span class="sxs-lookup"><span data-stu-id="ba6a2-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="ba6a2-569">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ba6a2-570">Te metody:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-570">These methods:</span></span>

* <span data-ttu-id="ba6a2-571">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ba6a2-572">`OnActionExecuting` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ba6a2-573">`OnActionExecuted` jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ba6a2-574">`OnActionExecutionAsync` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ba6a2-575">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ba6a2-576">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ba6a2-577">Pomocnik `TestController`:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-577">The `TestController`:</span></span>

* <span data-ttu-id="ba6a2-578">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="ba6a2-579">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ba6a2-580">Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ba6a2-581">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ba6a2-582">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="ba6a2-582">Overriding the default order</span></span>

<span data-ttu-id="ba6a2-583">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ba6a2-584">`IOrderedFilter` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ba6a2-585">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ba6a2-586">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ba6a2-587">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ba6a2-588">`Order`Właściwość można ustawić przy użyciu parametru konstruktora:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="ba6a2-589">Należy wziąć pod uwagę te same 3 filtry akcji, które przedstawiono w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="ba6a2-590">Jeśli `Order` Właściwość kontrolera i filtry globalne zostały odpowiednio ustawione na 1 i 2, kolejność wykonywania zostanie odwrócona.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="ba6a2-591">Sequence</span><span class="sxs-lookup"><span data-stu-id="ba6a2-591">Sequence</span></span> | <span data-ttu-id="ba6a2-592">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-592">Filter scope</span></span> | <span data-ttu-id="ba6a2-593">`Order` wartość</span><span class="sxs-lookup"><span data-stu-id="ba6a2-593">`Order` property</span></span> | <span data-ttu-id="ba6a2-594">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="ba6a2-595">1</span><span class="sxs-lookup"><span data-stu-id="ba6a2-595">1</span></span> | <span data-ttu-id="ba6a2-596">Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-596">Method</span></span> | <span data-ttu-id="ba6a2-597">0</span><span class="sxs-lookup"><span data-stu-id="ba6a2-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-598">2</span><span class="sxs-lookup"><span data-stu-id="ba6a2-598">2</span></span> | <span data-ttu-id="ba6a2-599">Kontroler</span><span class="sxs-lookup"><span data-stu-id="ba6a2-599">Controller</span></span> | <span data-ttu-id="ba6a2-600">1</span><span class="sxs-lookup"><span data-stu-id="ba6a2-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-601">3</span><span class="sxs-lookup"><span data-stu-id="ba6a2-601">3</span></span> | <span data-ttu-id="ba6a2-602">Globalnie</span><span class="sxs-lookup"><span data-stu-id="ba6a2-602">Global</span></span> | <span data-ttu-id="ba6a2-603">2</span><span class="sxs-lookup"><span data-stu-id="ba6a2-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ba6a2-604">4</span><span class="sxs-lookup"><span data-stu-id="ba6a2-604">4</span></span> | <span data-ttu-id="ba6a2-605">Globalnie</span><span class="sxs-lookup"><span data-stu-id="ba6a2-605">Global</span></span> | <span data-ttu-id="ba6a2-606">2</span><span class="sxs-lookup"><span data-stu-id="ba6a2-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ba6a2-607">5</span><span class="sxs-lookup"><span data-stu-id="ba6a2-607">5</span></span> | <span data-ttu-id="ba6a2-608">Kontroler</span><span class="sxs-lookup"><span data-stu-id="ba6a2-608">Controller</span></span> | <span data-ttu-id="ba6a2-609">1</span><span class="sxs-lookup"><span data-stu-id="ba6a2-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ba6a2-610">6</span><span class="sxs-lookup"><span data-stu-id="ba6a2-610">6</span></span> | <span data-ttu-id="ba6a2-611">Metoda</span><span class="sxs-lookup"><span data-stu-id="ba6a2-611">Method</span></span> | <span data-ttu-id="ba6a2-612">0</span><span class="sxs-lookup"><span data-stu-id="ba6a2-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="ba6a2-613">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ba6a2-614">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ba6a2-615">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ba6a2-616">W przypadku filtrów wbudowanych zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ba6a2-617">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="ba6a2-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ba6a2-618">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ba6a2-619">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ba6a2-620">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ba6a2-621">Pomocnik `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ba6a2-622">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ba6a2-623">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ba6a2-624">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ba6a2-625">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ba6a2-626">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="ba6a2-627">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="ba6a2-627">Dependency injection</span></span>

<span data-ttu-id="ba6a2-628">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ba6a2-629">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ba6a2-630">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ba6a2-631">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-631">A type-activated filter means:</span></span>

* <span data-ttu-id="ba6a2-632">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-632">An instance is created for each request.</span></span>
* <span data-ttu-id="ba6a2-633">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ba6a2-634">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ba6a2-635">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ba6a2-636">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ba6a2-637">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ba6a2-638">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ba6a2-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ba6a2-640">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ba6a2-641">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-641">Loggers are available from DI.</span></span> <span data-ttu-id="ba6a2-642">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ba6a2-643">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ba6a2-644">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-644">Logging added to filters:</span></span>

* <span data-ttu-id="ba6a2-645">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ba6a2-646">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ba6a2-647">Wbudowane filtry akcje dziennika i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ba6a2-648">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="ba6a2-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="ba6a2-649">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ba6a2-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ba6a2-651">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba6a2-652">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="ba6a2-653">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ba6a2-654">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ba6a2-655">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba6a2-656">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ba6a2-657">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ba6a2-658">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ba6a2-659">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ba6a2-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba6a2-661">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba6a2-662">`CreateInstance` Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ba6a2-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ba6a2-663">TypeFilterAttribute</span></span>

<span data-ttu-id="ba6a2-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ba6a2-665">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ba6a2-666">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ba6a2-667">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ba6a2-668">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ba6a2-669">`TypeFilterAttribute` Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ba6a2-670">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ba6a2-671">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba6a2-672">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ba6a2-673">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ba6a2-674">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ba6a2-675">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="ba6a2-675">Authorization filters</span></span>

<span data-ttu-id="ba6a2-676">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-676">Authorization filters:</span></span>

* <span data-ttu-id="ba6a2-677">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ba6a2-678">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-678">Control access to action methods.</span></span>
* <span data-ttu-id="ba6a2-679">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="ba6a2-680">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ba6a2-681">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ba6a2-682">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="ba6a2-683">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-683">Calls the authorization system.</span></span>
* <span data-ttu-id="ba6a2-684">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-684">Does not authorize requests.</span></span>

<span data-ttu-id="ba6a2-685">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ba6a2-686">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-686">The exception will not be handled.</span></span>
* <span data-ttu-id="ba6a2-687">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ba6a2-688">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ba6a2-689">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ba6a2-690">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="ba6a2-690">Resource filters</span></span>

<span data-ttu-id="ba6a2-691">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-691">Resource filters:</span></span>

* <span data-ttu-id="ba6a2-692">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ba6a2-693">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ba6a2-694">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ba6a2-695">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ba6a2-696">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ba6a2-697">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-697">Resource filter examples:</span></span>

* <span data-ttu-id="ba6a2-698">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ba6a2-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ba6a2-700">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ba6a2-701">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ba6a2-702">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="ba6a2-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ba6a2-703">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="ba6a2-704">Razor Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ba6a2-705">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ba6a2-706">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-706">Action filters:</span></span>

* <span data-ttu-id="ba6a2-707">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ba6a2-708">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ba6a2-709">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba6a2-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ba6a2-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -umożliwia odczytywanie danych wejściowych metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="ba6a2-712"><xref:Microsoft.AspNetCore.Mvc.Controller> -Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ba6a2-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> -Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ba6a2-714">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ba6a2-715">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ba6a2-716">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba6a2-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ba6a2-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba6a2-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> -O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ba6a2-720">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-720">Setting this property to null:</span></span>

  * <span data-ttu-id="ba6a2-721">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ba6a2-722">`Result` jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ba6a2-723">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ba6a2-724">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ba6a2-725">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ba6a2-726">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ba6a2-727">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ba6a2-728">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ba6a2-729">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-729">Validate model state.</span></span>
* <span data-ttu-id="ba6a2-730">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="ba6a2-731">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ba6a2-732">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ba6a2-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba6a2-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ba6a2-735">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ba6a2-736">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ba6a2-737">`ActionExecutedContext.Result` jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ba6a2-738">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="ba6a2-738">Exception filters</span></span>

<span data-ttu-id="ba6a2-739">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-739">Exception filters:</span></span>

* <span data-ttu-id="ba6a2-740">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="ba6a2-741">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ba6a2-742">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ba6a2-743">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-743">Exception filters:</span></span>

* <span data-ttu-id="ba6a2-744">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-744">Don't have before and after events.</span></span>
* <span data-ttu-id="ba6a2-745">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ba6a2-746">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ba6a2-747">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ba6a2-748">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ba6a2-749">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-749">This stops propagation of the exception.</span></span> <span data-ttu-id="ba6a2-750">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="ba6a2-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ba6a2-751">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-751">Only an action filter can do that.</span></span>

<span data-ttu-id="ba6a2-752">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-752">Exception filters:</span></span>

* <span data-ttu-id="ba6a2-753">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ba6a2-754">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ba6a2-755">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ba6a2-756">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ba6a2-757">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ba6a2-758">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ba6a2-759">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="ba6a2-759">Result filters</span></span>

<span data-ttu-id="ba6a2-760">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-760">Result filters:</span></span>

* <span data-ttu-id="ba6a2-761">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-761">Implement an interface:</span></span>
  * <span data-ttu-id="ba6a2-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba6a2-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ba6a2-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba6a2-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ba6a2-764">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ba6a2-765">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba6a2-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ba6a2-766">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba6a2-767">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ba6a2-768">Akcja zwracająca widok będzie obejmować wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanych operacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ba6a2-769">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ba6a2-770">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ba6a2-771">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ba6a2-772">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="ba6a2-773">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ba6a2-774">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ba6a2-775"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ba6a2-776">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ba6a2-777">Zgłaszanie wyjątku w programie `IResultFilter.OnResultExecuting` spowoduje:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="ba6a2-778">Zapobiegaj wykonaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ba6a2-779">Być traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba6a2-780">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="ba6a2-781">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="ba6a2-782">`ResultExecutedContext.Canceled` jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ba6a2-783">`ResultExecutedContext.Exception` ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ba6a2-784">Ustawienie `Exception` wartości null powoduje skuteczną obsługę wyjątku i uniemożliwia ponowne zgłoszenie wyjątku przez ASP.NET Core w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="ba6a2-785">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ba6a2-786">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ba6a2-787">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ba6a2-788">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ba6a2-789">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ba6a2-790">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ba6a2-791">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba6a2-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ba6a2-792"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ba6a2-793">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-793">This includes action results produced by:</span></span>

* <span data-ttu-id="ba6a2-794">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ba6a2-795">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-795">Exception filters.</span></span>

<span data-ttu-id="ba6a2-796">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ba6a2-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ba6a2-797">IFilterFactory</span></span>

<span data-ttu-id="ba6a2-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ba6a2-799">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ba6a2-800">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ba6a2-801">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ba6a2-802">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ba6a2-803">`IFilterFactory` można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ba6a2-804">Poprzedni kod może być testowany przez uruchomienie [przykładu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="ba6a2-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="ba6a2-805">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ba6a2-806">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="ba6a2-807">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ba6a2-808">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="ba6a2-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="ba6a2-809">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ba6a2-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ba6a2-810">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="ba6a2-810">**internal:** `My header`</span></span>

<span data-ttu-id="ba6a2-811">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ba6a2-812">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="ba6a2-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ba6a2-813">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ba6a2-814">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="ba6a2-815">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ba6a2-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba6a2-817">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba6a2-818">`CreateInstance` Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ba6a2-819">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="ba6a2-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ba6a2-820">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="ba6a2-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ba6a2-821">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="ba6a2-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ba6a2-822">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ba6a2-823">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego ASP.NET Core, co oznacza, że ma dostęp do ASP.NET Core kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="ba6a2-824">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ba6a2-825">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="ba6a2-826">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="ba6a2-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ba6a2-827">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="ba6a2-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ba6a2-828">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="ba6a2-828">Next actions</span></span>

* <span data-ttu-id="ba6a2-829">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="ba6a2-830">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="ba6a2-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
