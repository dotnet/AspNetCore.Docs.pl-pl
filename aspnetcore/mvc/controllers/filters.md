---
title: Filtry w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak działają filtry i jak korzystać z nich w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: b53b017e63ada62438e352a6c5112b7584ff0b26
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589104"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="a3b18-103">Filtry w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a3b18-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a3b18-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a3b18-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a3b18-105">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="a3b18-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a3b18-106">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="a3b18-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a3b18-107">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="a3b18-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a3b18-108">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="a3b18-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="a3b18-109">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a3b18-110">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="a3b18-111">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="a3b18-112">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a3b18-113">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="a3b18-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="a3b18-114">Filtry nie działają bezpośrednio ze [ Razor składnikami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="a3b18-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="a3b18-115">Filtr może mieć tylko pośredni wpływ na składnik, gdy:</span><span class="sxs-lookup"><span data-stu-id="a3b18-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="a3b18-116">Składnik jest osadzony na stronie lub widoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="a3b18-117">Strona lub kontroler/widok używają filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="a3b18-118">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a3b18-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a3b18-119">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="a3b18-119">How filters work</span></span>

<span data-ttu-id="a3b18-120">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="a3b18-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="a3b18-121">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok akcji wywołania.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a3b18-124">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="a3b18-124">Filter types</span></span>

<span data-ttu-id="a3b18-125">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="a3b18-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="a3b18-126">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="a3b18-127">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie nie jest autoryzowane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="a3b18-128">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="a3b18-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="a3b18-129">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-129">Run after authorization.</span></span>  
  * <span data-ttu-id="a3b18-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> uruchamia kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="a3b18-131">Na przykład `OnResourceExecuting` uruchamia kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="a3b18-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> uruchamia kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="a3b18-133">[Filtry akcji](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="a3b18-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="a3b18-134">Uruchom kod bezpośrednio przed i po wywołaniu metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="a3b18-135">Może zmienić argumenty przekazane do akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="a3b18-136">Można zmienić wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="a3b18-137">**Nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="a3b18-138">[Filtry wyjątków](#exception-filters) stosują zasady globalne do nieobsłużonych wyjątków, które występują przed zapisaniem treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a3b18-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="a3b18-139">[Filtry wyników](#result-filters) uruchamiają kod bezpośrednio przed i po wykonaniu wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="a3b18-140">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a3b18-141">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a3b18-142">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a3b18-145">Implementacja</span><span class="sxs-lookup"><span data-stu-id="a3b18-145">Implementation</span></span>

<span data-ttu-id="a3b18-146">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="a3b18-147">Filtry synchroniczne uruchamiają kod przed i po fazie potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="a3b18-148">Na przykład, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="a3b18-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a3b18-150">W poprzednim kodzie funkcja myFunction [jest funkcją](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) narzędziową w [przykładowym pobieraniu](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="a3b18-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="a3b18-151">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="a3b18-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="a3b18-152">Na przykład <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="a3b18-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="a3b18-153">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="a3b18-154">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="a3b18-154">Multiple filter stages</span></span>

<span data-ttu-id="a3b18-155">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="a3b18-156">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje:</span><span class="sxs-lookup"><span data-stu-id="a3b18-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="a3b18-157">Synchroniczne: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> i  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="a3b18-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="a3b18-158">Asynchroniczny: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a3b18-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="a3b18-159">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a3b18-160">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="a3b18-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a3b18-161">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a3b18-162">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="a3b18-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="a3b18-163">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , Zastąp tylko metody synchroniczne lub metody asynchroniczne dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a3b18-164">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-164">Built-in filter attributes</span></span>

<span data-ttu-id="a3b18-165">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="a3b18-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="a3b18-166">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a3b18-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="a3b18-167">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="a3b18-168">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="a3b18-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="a3b18-169">Użyj narzędzia, takiego jak [Narzędzia deweloperskie przeglądarki](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="a3b18-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="a3b18-170">W obszarze **nagłówki odpowiedzi** `author: Rick Anderson` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="a3b18-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="a3b18-171">Poniższy kod implementuje `ActionFilterAttribute` , że:</span><span class="sxs-lookup"><span data-stu-id="a3b18-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="a3b18-172">Odczytuje tytuł i nazwę z systemu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="a3b18-173">W przeciwieństwie do poprzedniego przykładu, poniższy kod nie wymaga dodania parametrów filtru do kodu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="a3b18-174">Dodaje tytuł i nazwę do nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a3b18-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a3b18-175">Opcje konfiguracji są dostępne z [systemu konfiguracji](xref:fundamentals/configuration/index) przy użyciu [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a3b18-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a3b18-176">Na przykład, z *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="a3b18-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="a3b18-177">W `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="a3b18-178">`PositionOptions`Klasa jest dodawana do kontenera usługi z `"Position"` obszarem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="a3b18-179">`MyActionFilterAttribute`Zostanie dodany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="a3b18-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="a3b18-180">Poniższy kod przedstawia `PositionOptions` klasę:</span><span class="sxs-lookup"><span data-stu-id="a3b18-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a3b18-181">Poniższy kod stosuje `MyActionFilterAttribute` `Index2` metodę do:</span><span class="sxs-lookup"><span data-stu-id="a3b18-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="a3b18-182">W obszarze **nagłówki odpowiedzi**, `author: Rick Anderson` i `Editor: Joe Smith` jest wyświetlana po `Sample/Index2` wywołaniu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="a3b18-183">Poniższy kod stosuje `MyActionFilterAttribute` `AddHeaderAttribute` stronę i do Razor strony:</span><span class="sxs-lookup"><span data-stu-id="a3b18-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="a3b18-184">Nie można zastosować filtrów do Razor metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="a3b18-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="a3b18-185">Mogą być stosowane do Razor modelu strony lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="a3b18-186">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="a3b18-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a3b18-187">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="a3b18-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a3b18-188">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="a3b18-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="a3b18-189">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="a3b18-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="a3b18-190">Użycie atrybutu w akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="a3b18-191">Atrybutów filtru nie można stosować do Razor metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="a3b18-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="a3b18-192">Użycie atrybutu na kontrolerze lub Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="a3b18-193">Globalnie dla wszystkich kontrolerów, akcji i Razor stron, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="a3b18-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="a3b18-194">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="a3b18-194">Default order of execution</span></span>

<span data-ttu-id="a3b18-195">Jeśli istnieje wiele filtrów dla określonego etapu potoku, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a3b18-196">Filtry globalne Otocz filtry klas, które z kolei filtrują metody przestrzenne.</span><span class="sxs-lookup"><span data-stu-id="a3b18-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="a3b18-197">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="a3b18-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a3b18-198">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="a3b18-198">The filter sequence:</span></span>

* <span data-ttu-id="a3b18-199">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="a3b18-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="a3b18-200">*Przed* kodem i Razor filtrami stron.</span><span class="sxs-lookup"><span data-stu-id="a3b18-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="a3b18-201">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="a3b18-202">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="a3b18-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="a3b18-203">*Po* kodzie kontrolera i Razor filtrów strony.</span><span class="sxs-lookup"><span data-stu-id="a3b18-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="a3b18-204">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="a3b18-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="a3b18-205">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="a3b18-206">Sequence</span><span class="sxs-lookup"><span data-stu-id="a3b18-206">Sequence</span></span> | <span data-ttu-id="a3b18-207">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-207">Filter scope</span></span> | <span data-ttu-id="a3b18-208">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a3b18-209">1</span><span class="sxs-lookup"><span data-stu-id="a3b18-209">1</span></span> | <span data-ttu-id="a3b18-210">Globalnie</span><span class="sxs-lookup"><span data-stu-id="a3b18-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a3b18-211">2</span><span class="sxs-lookup"><span data-stu-id="a3b18-211">2</span></span> | <span data-ttu-id="a3b18-212">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="a3b18-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="a3b18-213">3</span><span class="sxs-lookup"><span data-stu-id="a3b18-213">3</span></span> | <span data-ttu-id="a3b18-214">Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a3b18-215">4</span><span class="sxs-lookup"><span data-stu-id="a3b18-215">4</span></span> | <span data-ttu-id="a3b18-216">Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a3b18-217">5</span><span class="sxs-lookup"><span data-stu-id="a3b18-217">5</span></span> | <span data-ttu-id="a3b18-218">Kontroler lub Razor Strona</span><span class="sxs-lookup"><span data-stu-id="a3b18-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a3b18-219">6</span><span class="sxs-lookup"><span data-stu-id="a3b18-219">6</span></span> | <span data-ttu-id="a3b18-220">Globalnie</span><span class="sxs-lookup"><span data-stu-id="a3b18-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="a3b18-221">Filtry na poziomie kontrolera</span><span class="sxs-lookup"><span data-stu-id="a3b18-221">Controller level filters</span></span>

<span data-ttu-id="a3b18-222">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="a3b18-223">Te metody:</span><span class="sxs-lookup"><span data-stu-id="a3b18-223">These methods:</span></span>

* <span data-ttu-id="a3b18-224">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="a3b18-225">`OnActionExecuting` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="a3b18-226">`OnActionExecuted` jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="a3b18-227">`OnActionExecutionAsync` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="a3b18-228">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="a3b18-229">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="a3b18-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="a3b18-230">The `TestController`:</span></span>

* <span data-ttu-id="a3b18-231">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="a3b18-232">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="a3b18-233">Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="a3b18-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="a3b18-234">Filtry na poziomie kontrolera ustawiają Właściwość [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="a3b18-235">Filtrów na poziomie kontrolera **nie** można ustawić do uruchomienia po zastosowaniu filtrów do metod.</span><span class="sxs-lookup"><span data-stu-id="a3b18-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="a3b18-236">Klauzula Order została omówiona w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-236">Order is explained in the next section.</span></span>

<span data-ttu-id="a3b18-237">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="a3b18-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a3b18-238">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="a3b18-238">Overriding the default order</span></span>

<span data-ttu-id="a3b18-239">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="a3b18-240">`IOrderedFilter` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a3b18-241">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="a3b18-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="a3b18-242">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="a3b18-243">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="a3b18-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="a3b18-244">`Order`Właściwość jest ustawiona z parametrem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="a3b18-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="a3b18-245">Należy wziąć pod uwagę dwa filtry akcji na następującym kontrolerze:</span><span class="sxs-lookup"><span data-stu-id="a3b18-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="a3b18-246">Filtr globalny zostanie dodany w `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="a3b18-247">3 filtry działają w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="a3b18-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="a3b18-248">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="a3b18-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="a3b18-249">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="a3b18-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a3b18-250">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="a3b18-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a3b18-251">Jak wspomniano wcześniej, filtry na poziomie kontrolera [](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ustawiają Właściwość Order `int.MinValue` dla filtrów wbudowanych, zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="a3b18-252">W poprzednim kodzie `MySampleActionFilter` ma zakres globalny, dlatego działa wcześniej `MyAction2FilterAttribute` , który ma zakres kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="a3b18-253">Aby `MyAction2FilterAttribute` najpierw wykonać przebieg, ustaw kolejność na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="a3b18-254">Aby najpierw uruchomić filtr globalny `MySampleActionFilter` , ustaw opcję `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a3b18-255">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="a3b18-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="a3b18-256">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="a3b18-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="a3b18-257">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="a3b18-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a3b18-258">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a3b18-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a3b18-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a3b18-260">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a3b18-261">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a3b18-262">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a3b18-263">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="a3b18-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a3b18-264">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a3b18-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=1,15)]

## <a name="dependency-injection"></a><span data-ttu-id="a3b18-265">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="a3b18-265">Dependency injection</span></span>

<span data-ttu-id="a3b18-266">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a3b18-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a3b18-267">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="a3b18-268">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="a3b18-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="a3b18-269">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="a3b18-269">A type-activated filter means:</span></span>

* <span data-ttu-id="a3b18-270">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-270">An instance is created for each request.</span></span>
* <span data-ttu-id="a3b18-271">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a3b18-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="a3b18-272">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a3b18-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a3b18-273">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="a3b18-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="a3b18-274">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="a3b18-275">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a3b18-276">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="a3b18-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="a3b18-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="a3b18-278">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="a3b18-279">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-279">Loggers are available from DI.</span></span> <span data-ttu-id="a3b18-280">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="a3b18-281">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="a3b18-282">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-282">Logging added to filters:</span></span>

* <span data-ttu-id="a3b18-283">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="a3b18-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="a3b18-284">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="a3b18-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="a3b18-285">Wbudowane filtry rejestrują akcje i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="a3b18-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a3b18-286">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="a3b18-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="a3b18-287">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="a3b18-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="a3b18-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="a3b18-289">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a3b18-290">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="a3b18-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="a3b18-291">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="a3b18-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a3b18-292">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a3b18-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="a3b18-293">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a3b18-294">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="a3b18-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="a3b18-295">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="a3b18-296">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="a3b18-297">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="a3b18-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a3b18-299">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a3b18-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a3b18-300">`CreateInstance` Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a3b18-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a3b18-301">TypeFilterAttribute</span></span>

<span data-ttu-id="a3b18-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="a3b18-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a3b18-303">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="a3b18-304">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="a3b18-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="a3b18-305">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="a3b18-306">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="a3b18-307">`TypeFilterAttribute` Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a3b18-308">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a3b18-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="a3b18-309">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a3b18-310">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="a3b18-311">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a3b18-312">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="a3b18-313">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a3b18-313">Authorization filters</span></span>

<span data-ttu-id="a3b18-314">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-314">Authorization filters:</span></span>

* <span data-ttu-id="a3b18-315">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="a3b18-316">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-316">Control access to action methods.</span></span>
* <span data-ttu-id="a3b18-317">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="a3b18-318">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="a3b18-319">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a3b18-320">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="a3b18-321">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-321">Calls the authorization system.</span></span>
* <span data-ttu-id="a3b18-322">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-322">Does not authorize requests.</span></span>

<span data-ttu-id="a3b18-323">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="a3b18-324">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="a3b18-324">The exception will not be handled.</span></span>
* <span data-ttu-id="a3b18-325">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="a3b18-326">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="a3b18-327">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="a3b18-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a3b18-328">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="a3b18-328">Resource filters</span></span>

<span data-ttu-id="a3b18-329">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-329">Resource filters:</span></span>

* <span data-ttu-id="a3b18-330">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="a3b18-331">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="a3b18-332">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="a3b18-333">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="a3b18-334">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="a3b18-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="a3b18-335">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-335">Resource filter examples:</span></span>

* <span data-ttu-id="a3b18-336">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="a3b18-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="a3b18-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="a3b18-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="a3b18-338">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="a3b18-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="a3b18-339">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="a3b18-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a3b18-340">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="a3b18-340">Action filters</span></span>

<span data-ttu-id="a3b18-341">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="a3b18-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="a3b18-342">Razor Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a3b18-343">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a3b18-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a3b18-344">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-344">Action filters:</span></span>

* <span data-ttu-id="a3b18-345">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="a3b18-346">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a3b18-347">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a3b18-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="a3b18-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a3b18-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> — umożliwia odczytywanie danych wejściowych do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="a3b18-350"><xref:Microsoft.AspNetCore.Mvc.Controller> -Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="a3b18-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> -Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="a3b18-352">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="a3b18-353">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="a3b18-354">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="a3b18-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a3b18-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="a3b18-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a3b18-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="a3b18-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a3b18-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> -O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="a3b18-358">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="a3b18-358">Setting this property to null:</span></span>

  * <span data-ttu-id="a3b18-359">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="a3b18-360">`Result` jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="a3b18-361">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="a3b18-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="a3b18-362">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a3b18-363">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="a3b18-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="a3b18-364">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="a3b18-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="a3b18-365">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="a3b18-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="a3b18-366">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="a3b18-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="a3b18-367">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-367">Validate model state.</span></span>
* <span data-ttu-id="a3b18-368">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="a3b18-369">Kontrolery z adnotacją z `[ApiController]` atrybutem automatycznie weryfikują stan modelu i zwracają odpowiedź 400.</span><span class="sxs-lookup"><span data-stu-id="a3b18-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="a3b18-370">Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="a3b18-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="a3b18-371">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="a3b18-372">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="a3b18-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="a3b18-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a3b18-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a3b18-375">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="a3b18-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="a3b18-376">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="a3b18-377">`ActionExecutedContext.Result` jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="a3b18-378">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="a3b18-378">Exception filters</span></span>

<span data-ttu-id="a3b18-379">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a3b18-379">Exception filters:</span></span>

* <span data-ttu-id="a3b18-380">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="a3b18-381">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="a3b18-382">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="a3b18-383">Następujący kod testuje filtr wyjątku:</span><span class="sxs-lookup"><span data-stu-id="a3b18-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="a3b18-384">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a3b18-384">Exception filters:</span></span>

* <span data-ttu-id="a3b18-385">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="a3b18-385">Don't have before and after events.</span></span>
* <span data-ttu-id="a3b18-386">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="a3b18-387">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="a3b18-388">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="a3b18-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="a3b18-389">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="a3b18-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="a3b18-390">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-390">This stops propagation of the exception.</span></span> <span data-ttu-id="a3b18-391">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="a3b18-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a3b18-392">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-392">Only an action filter can do that.</span></span>

<span data-ttu-id="a3b18-393">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a3b18-393">Exception filters:</span></span>

* <span data-ttu-id="a3b18-394">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="a3b18-395">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="a3b18-396">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a3b18-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a3b18-397">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a3b18-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="a3b18-398">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="a3b18-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a3b18-399">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="a3b18-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="a3b18-400">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="a3b18-400">Result filters</span></span>

<span data-ttu-id="a3b18-401">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="a3b18-401">Result filters:</span></span>

* <span data-ttu-id="a3b18-402">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="a3b18-402">Implement an interface:</span></span>
  * <span data-ttu-id="a3b18-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a3b18-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="a3b18-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="a3b18-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="a3b18-405">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a3b18-406">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a3b18-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a3b18-407">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="a3b18-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a3b18-408">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="a3b18-409">Akcja zwracająca widok obejmuje wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanej operacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="a3b18-410">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a3b18-411">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="a3b18-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="a3b18-412">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="a3b18-413">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="a3b18-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="a3b18-414">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="a3b18-415">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="a3b18-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="a3b18-417">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a3b18-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a3b18-418">Zgłaszanie wyjątku w `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="a3b18-419">Zapobiega wykonywaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a3b18-420">Jest traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a3b18-421">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź prawdopodobnie została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="a3b18-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="a3b18-422">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="a3b18-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="a3b18-423">`ResultExecutedContext.Canceled` jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="a3b18-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a3b18-424">`ResultExecutedContext.Exception` ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a3b18-425">Ustawienie `Exception` wartości null skutecznie obsługuje wyjątek i uniemożliwia ponowne zgłoszenie wyjątku w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="a3b18-426">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="a3b18-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="a3b18-427">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a3b18-428">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a3b18-429">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="a3b18-430">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="a3b18-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="a3b18-431">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a3b18-432">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a3b18-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a3b18-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="a3b18-434">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="a3b18-434">This includes action results produced by:</span></span>

* <span data-ttu-id="a3b18-435">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="a3b18-436">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a3b18-436">Exception filters.</span></span>

<span data-ttu-id="a3b18-437">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="a3b18-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

## <a name="ifilterfactory"></a><span data-ttu-id="a3b18-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a3b18-438">IFilterFactory</span></span>

<span data-ttu-id="a3b18-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a3b18-440">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a3b18-441">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a3b18-442">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="a3b18-443">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a3b18-444">`IFilterFactory.IsReusable`:</span><span class="sxs-lookup"><span data-stu-id="a3b18-444">`IFilterFactory.IsReusable`:</span></span>

* <span data-ttu-id="a3b18-445">Jest wskazówką dla fabryki, że wystąpienie filtru utworzone przez fabrykę może być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-445">Is a hint by the factory that the filter instance created by the factory may be reused outside of the request scope it was created within.</span></span>
* <span data-ttu-id="a3b18-446">***Nie*** powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-446">Should ***not*** be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a3b18-447">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="a3b18-447">The ASP.NET Core runtime doesn't guarantee:</span></span>

* <span data-ttu-id="a3b18-448">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-448">That a single instance of the filter will be created.</span></span>
* <span data-ttu-id="a3b18-449">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-449">The filter will not be re-requested from the DI container at some later point.</span></span>

> [!WARNING] 
> <span data-ttu-id="a3b18-450">Skonfigurować tylko <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> do zwrócenia, `true` Jeśli źródło filtrów nie jest niejednoznaczne, filtry są bezstanowe i filtry są bezpieczne do użycia w wielu żądaniach HTTP.</span><span class="sxs-lookup"><span data-stu-id="a3b18-450">Only configure <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> to return `true` if the source of the filters is unambiguous, the filters are stateless, and the filters are safe to use across multiple HTTP requests.</span></span> <span data-ttu-id="a3b18-451">Na przykład nie zwracaj filtrów z DI, które są zarejestrowane jako zakres lub przejściowe, jeśli `IFilterFactory.IsReusable` zwraca `true` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-451">For instance, don't return filters from DI that are registered as scoped or transient if `IFilterFactory.IsReusable` returns `true`.</span></span>

<span data-ttu-id="a3b18-452">`IFilterFactory` można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-452">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="a3b18-453">Filtr jest stosowany w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="a3b18-453">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="a3b18-454">Przetestuj poprzedni kod, uruchamiając [przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="a3b18-454">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="a3b18-455">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="a3b18-455">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="a3b18-456">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="a3b18-456">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="a3b18-457">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="a3b18-457">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="a3b18-458">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="a3b18-458">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="a3b18-459">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="a3b18-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="a3b18-460">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="a3b18-460">**internal:** `My header`</span></span>

<span data-ttu-id="a3b18-461">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="a3b18-461">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="a3b18-462">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="a3b18-462">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="a3b18-463">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="a3b18-463">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="a3b18-464">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="a3b18-464">Don't require passing parameters.</span></span>
* <span data-ttu-id="a3b18-465">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-465">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a3b18-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a3b18-467">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a3b18-467">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a3b18-468">`CreateInstance` Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="a3b18-468">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a3b18-469">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-469">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a3b18-470">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-470">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a3b18-471">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-471">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a3b18-472">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-472">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a3b18-473">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego, co oznacza, że ma dostęp do kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-473">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="a3b18-474">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-474">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="a3b18-475">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="a3b18-475">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="a3b18-476">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="a3b18-476">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a3b18-477">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-477">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a3b18-478">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="a3b18-478">Next actions</span></span>

* <span data-ttu-id="a3b18-479">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a3b18-479">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="a3b18-480">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="a3b18-480">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a3b18-481">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a3b18-481">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a3b18-482">*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.</span><span class="sxs-lookup"><span data-stu-id="a3b18-482">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a3b18-483">Filtry wbudowane obsługują zadania takie jak:</span><span class="sxs-lookup"><span data-stu-id="a3b18-483">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a3b18-484">Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).</span><span class="sxs-lookup"><span data-stu-id="a3b18-484">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a3b18-485">Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).</span><span class="sxs-lookup"><span data-stu-id="a3b18-485">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="a3b18-486">Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-486">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a3b18-487">Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-487">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="a3b18-488">Filtry unikają duplikowania kodu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-488">Filters avoid duplicating code.</span></span> <span data-ttu-id="a3b18-489">Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-489">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a3b18-490">Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.</span><span class="sxs-lookup"><span data-stu-id="a3b18-490">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="a3b18-491">[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a3b18-491">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a3b18-492">Jak działają filtry</span><span class="sxs-lookup"><span data-stu-id="a3b18-492">How filters work</span></span>

<span data-ttu-id="a3b18-493">Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.</span><span class="sxs-lookup"><span data-stu-id="a3b18-493">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="a3b18-494">Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-494">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok wywołania akcji ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a3b18-497">Typy filtrów</span><span class="sxs-lookup"><span data-stu-id="a3b18-497">Filter types</span></span>

<span data-ttu-id="a3b18-498">Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:</span><span class="sxs-lookup"><span data-stu-id="a3b18-498">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="a3b18-499">[Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-499">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="a3b18-500">Filtry autoryzacji skracają obwód w przypadku, gdy żądanie jest nieautoryzowane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-500">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="a3b18-501">[Filtry zasobów](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="a3b18-501">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="a3b18-502">Uruchom po autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-502">Run after authorization.</span></span>  
  * <span data-ttu-id="a3b18-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> można uruchomić kod przed resztą potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="a3b18-504">Na przykład program `OnResourceExecuting` może uruchomić kod przed powiązaniem modelu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-504">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="a3b18-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> można uruchomić kod po zakończeniu reszty potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="a3b18-506">[Filtry akcji](#action-filters) mogą uruchomić kod bezpośrednio przed i po wywołaniu pojedynczej metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-506">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="a3b18-507">Mogą one służyć do manipulowania argumentami przekazaną do akcji i wynik zwrócony z akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-507">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="a3b18-508">Filtry akcji **nie** są obsługiwane na Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-508">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="a3b18-509">[Filtry wyjątków](#exception-filters) są używane do stosowania zasad globalnych do nieobsłużonych wyjątków, które wystąpiły przed zapisem w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a3b18-509">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="a3b18-510">[Filtry wynikowe](#result-filters) mogą uruchamiać kod bezpośrednio przed i po wykonaniu poszczególnych wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-510">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="a3b18-511">Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-511">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a3b18-512">Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-512">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a3b18-513">Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-513">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a3b18-516">Implementacja</span><span class="sxs-lookup"><span data-stu-id="a3b18-516">Implementation</span></span>

<span data-ttu-id="a3b18-517">Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-517">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="a3b18-518">Filtry synchroniczne mogą uruchamiać kod przed ( `On-Stage-Executing` ) i po nim ( `On-Stage-Executed` ) ich etap potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-518">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="a3b18-519">Na przykład, `OnActionExecuting` jest wywoływana przed wywołaniem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-519">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="a3b18-520">`OnActionExecuted` jest wywoływana po powrocie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-520">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a3b18-521">Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę:</span><span class="sxs-lookup"><span data-stu-id="a3b18-521">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="a3b18-522">W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-522">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="a3b18-523">Każda z tych `On-Stage-ExecutionAsync` metod przyjmuje `FilterType-ExecutionDelegate` etap potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-523">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="a3b18-524">Wiele etapów filtrowania</span><span class="sxs-lookup"><span data-stu-id="a3b18-524">Multiple filter stages</span></span>

<span data-ttu-id="a3b18-525">Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-525">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="a3b18-526">Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje `IActionFilter` , `IResultFilter` i ich odpowiedniki asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="a3b18-526">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="a3b18-527">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-527">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a3b18-528">Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="a3b18-528">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a3b18-529">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-529">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a3b18-530">Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async.</span><span class="sxs-lookup"><span data-stu-id="a3b18-530">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="a3b18-531">W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> zastąpienie tylko metod synchronicznych lub metodę asynchroniczną dla każdego typu filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-531">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a3b18-532">Wbudowane atrybuty filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-532">Built-in filter attributes</span></span>

<span data-ttu-id="a3b18-533">ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="a3b18-533">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="a3b18-534">Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a3b18-534">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="a3b18-535">Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-535">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="a3b18-536">Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:</span><span class="sxs-lookup"><span data-stu-id="a3b18-536">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="a3b18-537">Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="a3b18-537">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a3b18-538">Atrybuty filtru:</span><span class="sxs-lookup"><span data-stu-id="a3b18-538">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a3b18-539">Zakresy filtrów i kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="a3b18-539">Filter scopes and order of execution</span></span>

<span data-ttu-id="a3b18-540">Filtr można dodać do potoku w jednym z trzech *zakresów*:</span><span class="sxs-lookup"><span data-stu-id="a3b18-540">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="a3b18-541">Użycie atrybutu w akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-541">Using an attribute on an action.</span></span>
* <span data-ttu-id="a3b18-542">Używanie atrybutu na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="a3b18-542">Using an attribute on a controller.</span></span>
* <span data-ttu-id="a3b18-543">Globalnie dla wszystkich kontrolerów i akcji, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="a3b18-543">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a3b18-544">Poprzedni kod dodaje trzy filtry globalnie przy użyciu kolekcji [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="a3b18-544">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="a3b18-545">Domyślna kolejność wykonywania</span><span class="sxs-lookup"><span data-stu-id="a3b18-545">Default order of execution</span></span>

<span data-ttu-id="a3b18-546">Jeśli istnieje wiele filtrów tego *samego typu*, zakres określa domyślną kolejność wykonywania filtrowania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-546">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a3b18-547">Filtry globalne Otocz filtry klas.</span><span class="sxs-lookup"><span data-stu-id="a3b18-547">Global filters surround class filters.</span></span> <span data-ttu-id="a3b18-548">Filtry klas Otocz filtry metod.</span><span class="sxs-lookup"><span data-stu-id="a3b18-548">Class filters surround method filters.</span></span>

<span data-ttu-id="a3b18-549">W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem.</span><span class="sxs-lookup"><span data-stu-id="a3b18-549">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a3b18-550">Sekwencja filtru:</span><span class="sxs-lookup"><span data-stu-id="a3b18-550">The filter sequence:</span></span>

* <span data-ttu-id="a3b18-551">*Przed* kodem filtrów globalnych.</span><span class="sxs-lookup"><span data-stu-id="a3b18-551">The *before* code of global filters.</span></span>
  * <span data-ttu-id="a3b18-552">*Przed* kodem filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-552">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="a3b18-553">Filtr *przed* kodem metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-553">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="a3b18-554">*Po* kodzie metody akcji filtry.</span><span class="sxs-lookup"><span data-stu-id="a3b18-554">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="a3b18-555">*Po* kodzie filtrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-555">The *after* code of controller filters.</span></span>
* <span data-ttu-id="a3b18-556">Kod *po* filtrach globalnych.</span><span class="sxs-lookup"><span data-stu-id="a3b18-556">The *after* code of global filters.</span></span>
  
<span data-ttu-id="a3b18-557">Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-557">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="a3b18-558">Sequence</span><span class="sxs-lookup"><span data-stu-id="a3b18-558">Sequence</span></span> | <span data-ttu-id="a3b18-559">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-559">Filter scope</span></span> | <span data-ttu-id="a3b18-560">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-560">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a3b18-561">1</span><span class="sxs-lookup"><span data-stu-id="a3b18-561">1</span></span> | <span data-ttu-id="a3b18-562">Globalnie</span><span class="sxs-lookup"><span data-stu-id="a3b18-562">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a3b18-563">2</span><span class="sxs-lookup"><span data-stu-id="a3b18-563">2</span></span> | <span data-ttu-id="a3b18-564">Kontroler</span><span class="sxs-lookup"><span data-stu-id="a3b18-564">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a3b18-565">3</span><span class="sxs-lookup"><span data-stu-id="a3b18-565">3</span></span> | <span data-ttu-id="a3b18-566">Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-566">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a3b18-567">4</span><span class="sxs-lookup"><span data-stu-id="a3b18-567">4</span></span> | <span data-ttu-id="a3b18-568">Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-568">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a3b18-569">5</span><span class="sxs-lookup"><span data-stu-id="a3b18-569">5</span></span> | <span data-ttu-id="a3b18-570">Kontroler</span><span class="sxs-lookup"><span data-stu-id="a3b18-570">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a3b18-571">6</span><span class="sxs-lookup"><span data-stu-id="a3b18-571">6</span></span> | <span data-ttu-id="a3b18-572">Globalnie</span><span class="sxs-lookup"><span data-stu-id="a3b18-572">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="a3b18-573">Ta sekwencja pokazuje:</span><span class="sxs-lookup"><span data-stu-id="a3b18-573">This sequence shows:</span></span>

* <span data-ttu-id="a3b18-574">Filtr metody jest zagnieżdżony w obrębie filtru kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-574">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="a3b18-575">Filtr kontrolera jest zagnieżdżony w obrębie filtru globalnego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-575">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="a3b18-576">Filtry na Razor poziomie kontrolera i strony</span><span class="sxs-lookup"><span data-stu-id="a3b18-576">Controller and Razor Page level filters</span></span>

<span data-ttu-id="a3b18-577">Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-577">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="a3b18-578">Te metody:</span><span class="sxs-lookup"><span data-stu-id="a3b18-578">These methods:</span></span>

* <span data-ttu-id="a3b18-579">Zawiń filtry, które są uruchamiane dla danego działania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-579">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="a3b18-580">`OnActionExecuting` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-580">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="a3b18-581">`OnActionExecuted` jest wywoływana po wszystkich filtrach akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-581">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="a3b18-582">`OnActionExecutionAsync` jest wywoływana przed dowolnym filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-582">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="a3b18-583">Kod w filtrze po `next` uruchomieniu po metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-583">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="a3b18-584">Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-584">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="a3b18-585">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="a3b18-585">The `TestController`:</span></span>

* <span data-ttu-id="a3b18-586">Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-586">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="a3b18-587">Przesłania `OnActionExecuting` i `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-587">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="a3b18-588">Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="a3b18-588">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="a3b18-589">Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="a3b18-589">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a3b18-590">Zastępowanie kolejności domyślnej</span><span class="sxs-lookup"><span data-stu-id="a3b18-590">Overriding the default order</span></span>

<span data-ttu-id="a3b18-591">Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-591">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="a3b18-592">`IOrderedFilter` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-592">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a3b18-593">Filtr o niższej `Order` wartości:</span><span class="sxs-lookup"><span data-stu-id="a3b18-593">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="a3b18-594">Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-594">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="a3b18-595">Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.</span><span class="sxs-lookup"><span data-stu-id="a3b18-595">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="a3b18-596">`Order`Właściwość można ustawić przy użyciu parametru konstruktora:</span><span class="sxs-lookup"><span data-stu-id="a3b18-596">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="a3b18-597">Należy wziąć pod uwagę te same 3 filtry akcji, które przedstawiono w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-597">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="a3b18-598">Jeśli `Order` Właściwość kontrolera i filtry globalne zostały odpowiednio ustawione na 1 i 2, kolejność wykonywania zostanie odwrócona.</span><span class="sxs-lookup"><span data-stu-id="a3b18-598">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="a3b18-599">Sequence</span><span class="sxs-lookup"><span data-stu-id="a3b18-599">Sequence</span></span> | <span data-ttu-id="a3b18-600">Zakres filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-600">Filter scope</span></span> | <span data-ttu-id="a3b18-601">`Order` wartość</span><span class="sxs-lookup"><span data-stu-id="a3b18-601">`Order` property</span></span> | <span data-ttu-id="a3b18-602">Filter — Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-602">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="a3b18-603">1</span><span class="sxs-lookup"><span data-stu-id="a3b18-603">1</span></span> | <span data-ttu-id="a3b18-604">Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-604">Method</span></span> | <span data-ttu-id="a3b18-605">0</span><span class="sxs-lookup"><span data-stu-id="a3b18-605">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a3b18-606">2</span><span class="sxs-lookup"><span data-stu-id="a3b18-606">2</span></span> | <span data-ttu-id="a3b18-607">Kontroler</span><span class="sxs-lookup"><span data-stu-id="a3b18-607">Controller</span></span> | <span data-ttu-id="a3b18-608">1</span><span class="sxs-lookup"><span data-stu-id="a3b18-608">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a3b18-609">3</span><span class="sxs-lookup"><span data-stu-id="a3b18-609">3</span></span> | <span data-ttu-id="a3b18-610">Globalnie</span><span class="sxs-lookup"><span data-stu-id="a3b18-610">Global</span></span> | <span data-ttu-id="a3b18-611">2</span><span class="sxs-lookup"><span data-stu-id="a3b18-611">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a3b18-612">4</span><span class="sxs-lookup"><span data-stu-id="a3b18-612">4</span></span> | <span data-ttu-id="a3b18-613">Globalnie</span><span class="sxs-lookup"><span data-stu-id="a3b18-613">Global</span></span> | <span data-ttu-id="a3b18-614">2</span><span class="sxs-lookup"><span data-stu-id="a3b18-614">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a3b18-615">5</span><span class="sxs-lookup"><span data-stu-id="a3b18-615">5</span></span> | <span data-ttu-id="a3b18-616">Kontroler</span><span class="sxs-lookup"><span data-stu-id="a3b18-616">Controller</span></span> | <span data-ttu-id="a3b18-617">1</span><span class="sxs-lookup"><span data-stu-id="a3b18-617">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a3b18-618">6</span><span class="sxs-lookup"><span data-stu-id="a3b18-618">6</span></span> | <span data-ttu-id="a3b18-619">Metoda</span><span class="sxs-lookup"><span data-stu-id="a3b18-619">Method</span></span> | <span data-ttu-id="a3b18-620">0</span><span class="sxs-lookup"><span data-stu-id="a3b18-620">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="a3b18-621">`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="a3b18-621">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="a3b18-622">Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań.</span><span class="sxs-lookup"><span data-stu-id="a3b18-622">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a3b18-623">Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0.</span><span class="sxs-lookup"><span data-stu-id="a3b18-623">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a3b18-624">W przypadku filtrów wbudowanych zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-624">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a3b18-625">Anulowanie i krótkie obwody</span><span class="sxs-lookup"><span data-stu-id="a3b18-625">Cancellation and short-circuiting</span></span>

<span data-ttu-id="a3b18-626">Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra.</span><span class="sxs-lookup"><span data-stu-id="a3b18-626">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="a3b18-627">Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:</span><span class="sxs-lookup"><span data-stu-id="a3b18-627">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a3b18-628">W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-628">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a3b18-629">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a3b18-629">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a3b18-630">Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-630">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a3b18-631">Krótkie obwody pozostała część potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-631">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a3b18-632">W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-632">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a3b18-633">Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze.</span><span class="sxs-lookup"><span data-stu-id="a3b18-633">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a3b18-634">`ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a3b18-634">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="a3b18-635">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="a3b18-635">Dependency injection</span></span>

<span data-ttu-id="a3b18-636">Filtry można dodawać według typu lub według wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a3b18-636">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a3b18-637">W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-637">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="a3b18-638">Jeśli typ zostanie dodany, jego typ jest aktywowany.</span><span class="sxs-lookup"><span data-stu-id="a3b18-638">If a type is added, it's type-activated.</span></span> <span data-ttu-id="a3b18-639">Filtr aktywowany przez typ oznacza:</span><span class="sxs-lookup"><span data-stu-id="a3b18-639">A type-activated filter means:</span></span>

* <span data-ttu-id="a3b18-640">Wystąpienie jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-640">An instance is created for each request.</span></span>
* <span data-ttu-id="a3b18-641">Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a3b18-641">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="a3b18-642">Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a3b18-642">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a3b18-643">Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="a3b18-643">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="a3b18-644">Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-644">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="a3b18-645">Jest to ograniczenie działania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-645">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a3b18-646">Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:</span><span class="sxs-lookup"><span data-stu-id="a3b18-646">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="a3b18-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> zaimplementowane dla atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="a3b18-648">Powyższe filtry można zastosować do kontrolera lub metody akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-648">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="a3b18-649">Rejestratory są dostępne z programu DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-649">Loggers are available from DI.</span></span> <span data-ttu-id="a3b18-650">Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-650">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="a3b18-651">[Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-651">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="a3b18-652">Rejestrowanie dodane do filtrów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-652">Logging added to filters:</span></span>

* <span data-ttu-id="a3b18-653">Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.</span><span class="sxs-lookup"><span data-stu-id="a3b18-653">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="a3b18-654">**Nie** należy rejestrować akcji ani innych zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="a3b18-654">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="a3b18-655">Wbudowane filtry akcje dziennika i zdarzenia struktury.</span><span class="sxs-lookup"><span data-stu-id="a3b18-655">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a3b18-656">Servicefilterattribute</span><span class="sxs-lookup"><span data-stu-id="a3b18-656">ServiceFilterAttribute</span></span>

<span data-ttu-id="a3b18-657">Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-657">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="a3b18-658"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.</span><span class="sxs-lookup"><span data-stu-id="a3b18-658">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="a3b18-659">Poniższy kod przedstawia `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-659">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a3b18-660">W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="a3b18-660">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a3b18-661">W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="a3b18-661">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a3b18-662">W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a3b18-662">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="a3b18-663">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-663">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a3b18-664">Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:</span><span class="sxs-lookup"><span data-stu-id="a3b18-664">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="a3b18-665">Zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-665">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="a3b18-666">Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-666">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="a3b18-667">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-667">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="a3b18-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a3b18-669">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a3b18-669">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a3b18-670">`CreateInstance` Ładuje określony typ z DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-670">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a3b18-671">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a3b18-671">TypeFilterAttribute</span></span>

<span data-ttu-id="a3b18-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="a3b18-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a3b18-673">Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-673">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="a3b18-674">Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:</span><span class="sxs-lookup"><span data-stu-id="a3b18-674">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="a3b18-675">Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-675">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="a3b18-676">Ich zależności są spełnione przez kontener DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-676">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="a3b18-677">`TypeFilterAttribute` Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-677">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a3b18-678">W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a3b18-678">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="a3b18-679">Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach.</span><span class="sxs-lookup"><span data-stu-id="a3b18-679">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a3b18-680">Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-680">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="a3b18-681">Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-681">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a3b18-682">Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-682">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="a3b18-683">Filtry autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a3b18-683">Authorization filters</span></span>

<span data-ttu-id="a3b18-684">Filtry autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-684">Authorization filters:</span></span>

* <span data-ttu-id="a3b18-685">Są pierwszymi filtrami uruchamianymi w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-685">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="a3b18-686">Kontrola dostępu do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-686">Control access to action methods.</span></span>
* <span data-ttu-id="a3b18-687">Ma metodę Before, ale nie po metodzie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-687">Have a before method, but no after method.</span></span>

<span data-ttu-id="a3b18-688">Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-688">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="a3b18-689">Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-689">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a3b18-690">Wbudowany filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-690">The built-in authorization filter:</span></span>

* <span data-ttu-id="a3b18-691">Wywołuje system autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-691">Calls the authorization system.</span></span>
* <span data-ttu-id="a3b18-692">Nie zezwala na żądania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-692">Does not authorize requests.</span></span>

<span data-ttu-id="a3b18-693">**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-693">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="a3b18-694">Wyjątek nie zostanie obsłużony.</span><span class="sxs-lookup"><span data-stu-id="a3b18-694">The exception will not be handled.</span></span>
* <span data-ttu-id="a3b18-695">Filtry wyjątków nie będą obsługiwać wyjątku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-695">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="a3b18-696">Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-696">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="a3b18-697">Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="a3b18-697">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a3b18-698">Filtry zasobów</span><span class="sxs-lookup"><span data-stu-id="a3b18-698">Resource filters</span></span>

<span data-ttu-id="a3b18-699">Filtry zasobów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-699">Resource filters:</span></span>

* <span data-ttu-id="a3b18-700">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="a3b18-701">Wykonanie powoduje Zawijanie większości potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-701">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="a3b18-702">Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-702">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="a3b18-703">Filtry zasobów są przydatne do krótkiego obwodu większości potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-703">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="a3b18-704">Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="a3b18-704">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="a3b18-705">Przykłady filtru zasobów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-705">Resource filter examples:</span></span>

* <span data-ttu-id="a3b18-706">[Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="a3b18-706">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="a3b18-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="a3b18-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="a3b18-708">Uniemożliwia powiązanie modelu z dostępem do danych formularza.</span><span class="sxs-lookup"><span data-stu-id="a3b18-708">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="a3b18-709">Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.</span><span class="sxs-lookup"><span data-stu-id="a3b18-709">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a3b18-710">Filtry akcji</span><span class="sxs-lookup"><span data-stu-id="a3b18-710">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a3b18-711">Filtry akcji **nie** mają zastosowania do Razor stron.</span><span class="sxs-lookup"><span data-stu-id="a3b18-711">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="a3b18-712">Razor Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-712">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a3b18-713">Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a3b18-713">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a3b18-714">Filtry akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-714">Action filters:</span></span>

* <span data-ttu-id="a3b18-715">Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-715">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="a3b18-716">Ich wykonanie otacza wykonywanie metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-716">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a3b18-717">Poniższy kod przedstawia przykładowy filtr akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-717">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a3b18-718"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="a3b18-718">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a3b18-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -umożliwia odczytywanie danych wejściowych metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="a3b18-720"><xref:Microsoft.AspNetCore.Mvc.Controller> -Włącza manipulowanie wystąpieniem kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a3b18-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="a3b18-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> -Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="a3b18-722">Zgłaszanie wyjątku w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-722">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="a3b18-723">Zapobiega uruchamianiu kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-723">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="a3b18-724">W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.</span><span class="sxs-lookup"><span data-stu-id="a3b18-724">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a3b18-725"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="a3b18-725">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a3b18-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="a3b18-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a3b18-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> -O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="a3b18-728">Ustawienie tej właściwości na wartość null:</span><span class="sxs-lookup"><span data-stu-id="a3b18-728">Setting this property to null:</span></span>

  * <span data-ttu-id="a3b18-729">Skutecznie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-729">Effectively handles the exception.</span></span>
  * <span data-ttu-id="a3b18-730">`Result` jest wykonywany tak, jakby został zwrócony z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-730">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="a3b18-731">Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="a3b18-731">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="a3b18-732">Wykonuje wszystkie kolejne filtry akcji i metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-732">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a3b18-733">Zwraca wartość `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="a3b18-733">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="a3b18-734">Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="a3b18-734">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="a3b18-735">Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="a3b18-735">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="a3b18-736">`OnActionExecuting`Filtr akcji może służyć do:</span><span class="sxs-lookup"><span data-stu-id="a3b18-736">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="a3b18-737">Zweryfikuj stan modelu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-737">Validate model state.</span></span>
* <span data-ttu-id="a3b18-738">Zwróć błąd, jeśli stan jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="a3b18-738">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="a3b18-739">`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-739">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="a3b18-740">I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-740">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="a3b18-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="a3b18-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a3b18-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a3b18-743">Ustawienie `Exception` na wartość null:</span><span class="sxs-lookup"><span data-stu-id="a3b18-743">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="a3b18-744">Efektywnie obsługuje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-744">Effectively handles an exception.</span></span>
  * <span data-ttu-id="a3b18-745">`ActionExecutedContext.Result` jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-745">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="a3b18-746">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="a3b18-746">Exception filters</span></span>

<span data-ttu-id="a3b18-747">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a3b18-747">Exception filters:</span></span>

* <span data-ttu-id="a3b18-748">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-748">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="a3b18-749">Może służyć do implementowania typowych zasad obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-749">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="a3b18-750">Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a3b18-750">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="a3b18-751">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a3b18-751">Exception filters:</span></span>

* <span data-ttu-id="a3b18-752">Nie mam zdarzeń przed i po.</span><span class="sxs-lookup"><span data-stu-id="a3b18-752">Don't have before and after events.</span></span>
* <span data-ttu-id="a3b18-753">Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-753">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="a3b18-754">Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-754">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="a3b18-755">**Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.</span><span class="sxs-lookup"><span data-stu-id="a3b18-755">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="a3b18-756">Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="a3b18-756">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="a3b18-757">Spowoduje to zatrzymanie propagacji wyjątku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-757">This stops propagation of the exception.</span></span> <span data-ttu-id="a3b18-758">Filtr wyjątku nie może przekształcić wyjątku w "powodzenie".</span><span class="sxs-lookup"><span data-stu-id="a3b18-758">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a3b18-759">Można to zrobić tylko dla filtru akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-759">Only an action filter can do that.</span></span>

<span data-ttu-id="a3b18-760">Filtry wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a3b18-760">Exception filters:</span></span>

* <span data-ttu-id="a3b18-761">Są dobre dla wyjątków zalewkowania występujących w ramach akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-761">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="a3b18-762">Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-762">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="a3b18-763">Preferuj oprogramowanie pośredniczące dla obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a3b18-763">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a3b18-764">Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a3b18-764">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="a3b18-765">Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML.</span><span class="sxs-lookup"><span data-stu-id="a3b18-765">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a3b18-766">Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.</span><span class="sxs-lookup"><span data-stu-id="a3b18-766">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="a3b18-767">Filtry wyników</span><span class="sxs-lookup"><span data-stu-id="a3b18-767">Result filters</span></span>

<span data-ttu-id="a3b18-768">Filtry wyników:</span><span class="sxs-lookup"><span data-stu-id="a3b18-768">Result filters:</span></span>

* <span data-ttu-id="a3b18-769">Zaimplementuj interfejs:</span><span class="sxs-lookup"><span data-stu-id="a3b18-769">Implement an interface:</span></span>
  * <span data-ttu-id="a3b18-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a3b18-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="a3b18-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="a3b18-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="a3b18-772">Ich wykonanie otacza wykonywanie wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-772">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a3b18-773">IResultFilter i IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a3b18-773">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a3b18-774">Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:</span><span class="sxs-lookup"><span data-stu-id="a3b18-774">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a3b18-775">Rodzaj wykonywanego wyniku zależy od akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-775">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="a3b18-776">Akcja zwracająca widok będzie obejmować wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanych operacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-776">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="a3b18-777">Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-777">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a3b18-778">Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="a3b18-778">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="a3b18-779">Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-779">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="a3b18-780">Filtry wynikowe nie są wykonywane, gdy:</span><span class="sxs-lookup"><span data-stu-id="a3b18-780">Result filters are not executed when:</span></span>

* <span data-ttu-id="a3b18-781">Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-781">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="a3b18-782">Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-782">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="a3b18-783"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-783">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="a3b18-784">Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a3b18-784">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a3b18-785">Zgłaszanie wyjątku w programie `IResultFilter.OnResultExecuting` spowoduje:</span><span class="sxs-lookup"><span data-stu-id="a3b18-785">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="a3b18-786">Zapobiegaj wykonaniu wyniku akcji i kolejnych filtrów.</span><span class="sxs-lookup"><span data-stu-id="a3b18-786">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a3b18-787">Być traktowany jako niepowodzenie, a nie wynikowy pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="a3b18-787">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a3b18-788">Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź została już wysłana do klienta.</span><span class="sxs-lookup"><span data-stu-id="a3b18-788">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="a3b18-789">Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="a3b18-789">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="a3b18-790">`ResultExecutedContext.Canceled` jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.</span><span class="sxs-lookup"><span data-stu-id="a3b18-790">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a3b18-791">`ResultExecutedContext.Exception` ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a3b18-791">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a3b18-792">Ustawienie `Exception` wartości null powoduje skuteczną obsługę wyjątku i uniemożliwia ponowne zgłoszenie wyjątku przez ASP.NET Core w dalszej części potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-792">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="a3b18-793">Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym.</span><span class="sxs-lookup"><span data-stu-id="a3b18-793">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="a3b18-794">Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="a3b18-794">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a3b18-795">Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania.</span><span class="sxs-lookup"><span data-stu-id="a3b18-795">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a3b18-796">Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-796">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="a3b18-797">Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="a3b18-797">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="a3b18-798">Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.</span><span class="sxs-lookup"><span data-stu-id="a3b18-798">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a3b18-799">IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a3b18-799">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a3b18-800"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-800">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="a3b18-801">Obejmuje to wyniki akcji generowane przez:</span><span class="sxs-lookup"><span data-stu-id="a3b18-801">This includes action results produced by:</span></span>

* <span data-ttu-id="a3b18-802">Filtry autoryzacji i filtry zasobów, które są skracane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-802">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="a3b18-803">Filtry wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a3b18-803">Exception filters.</span></span>

<span data-ttu-id="a3b18-804">Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="a3b18-804">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

## <a name="ifilterfactory"></a><span data-ttu-id="a3b18-805">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a3b18-805">IFilterFactory</span></span>

<span data-ttu-id="a3b18-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a3b18-807">W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-807">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a3b18-808">Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-808">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a3b18-809">Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="a3b18-809">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="a3b18-810">Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-810">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a3b18-811">`IFilterFactory` można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:</span><span class="sxs-lookup"><span data-stu-id="a3b18-811">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="a3b18-812">Poprzedni kod może być testowany przez uruchomienie [przykładu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="a3b18-812">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="a3b18-813">Wywołaj narzędzia deweloperskie F12.</span><span class="sxs-lookup"><span data-stu-id="a3b18-813">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="a3b18-814">Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="a3b18-814">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="a3b18-815">Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:</span><span class="sxs-lookup"><span data-stu-id="a3b18-815">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="a3b18-816">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="a3b18-816">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="a3b18-817">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="a3b18-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="a3b18-818">**wewnętrzny:**`My header`</span><span class="sxs-lookup"><span data-stu-id="a3b18-818">**internal:** `My header`</span></span>

<span data-ttu-id="a3b18-819">Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="a3b18-819">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="a3b18-820">IFilterFactory zaimplementowane dla atrybutu</span><span class="sxs-lookup"><span data-stu-id="a3b18-820">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="a3b18-821">Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:</span><span class="sxs-lookup"><span data-stu-id="a3b18-821">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="a3b18-822">Nie wymagaj parametrów przekazujących.</span><span class="sxs-lookup"><span data-stu-id="a3b18-822">Don't require passing parameters.</span></span>
* <span data-ttu-id="a3b18-823">Mają zależności konstruktora, które muszą być wypełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="a3b18-823">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a3b18-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a3b18-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a3b18-825">`IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a3b18-825">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a3b18-826">`CreateInstance` Ładuje określony typ z kontenera usług (DI).</span><span class="sxs-lookup"><span data-stu-id="a3b18-826">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a3b18-827">Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="a3b18-827">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a3b18-828">W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="a3b18-828">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a3b18-829">Używanie oprogramowania pośredniczącego w potoku filtru</span><span class="sxs-lookup"><span data-stu-id="a3b18-829">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a3b18-830">Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-830">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a3b18-831">Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego ASP.NET Core, co oznacza, że ma dostęp do ASP.NET Core kontekstu i konstrukcji.</span><span class="sxs-lookup"><span data-stu-id="a3b18-831">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="a3b18-832">Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru.</span><span class="sxs-lookup"><span data-stu-id="a3b18-832">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="a3b18-833">W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:</span><span class="sxs-lookup"><span data-stu-id="a3b18-833">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="a3b18-834">Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="a3b18-834">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a3b18-835">Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.</span><span class="sxs-lookup"><span data-stu-id="a3b18-835">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a3b18-836">Następne akcje</span><span class="sxs-lookup"><span data-stu-id="a3b18-836">Next actions</span></span>

* <span data-ttu-id="a3b18-837">Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a3b18-837">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="a3b18-838">Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="a3b18-838">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
