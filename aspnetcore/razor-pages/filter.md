---
title: Metody filtrowania dla Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak tworzyć metody filtrowania dla Razor stron w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: 436d640130b378e2c770322186020c6e252872ef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722517"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="52782-103">Metody filtrowania dla Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52782-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52782-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52782-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="52782-105">Razor[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają Razor stronom uruchamianie kodu przed Razor uruchomieniem programu obsługi stron i po nim.</span><span class="sxs-lookup"><span data-stu-id="52782-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="52782-106">Razor Filtry stron są podobne do [ASP.NET Core filtrów akcji MVC](xref:mvc/controllers/filters#action-filters), z wyjątkiem sytuacji, gdy nie można ich zastosować do metod obsługi poszczególnych stron.</span><span class="sxs-lookup"><span data-stu-id="52782-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="52782-107">Razor Filtry stron:</span><span class="sxs-lookup"><span data-stu-id="52782-107">Razor Page filters:</span></span>

* <span data-ttu-id="52782-108">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="52782-109">Uruchom kod przed wykonaniem metody obsługi, po utworzeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="52782-110">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="52782-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="52782-111">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="52782-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="52782-112">Nie można zastosować do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="52782-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="52782-113">Może mieć zależności konstruktora wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="52782-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="52782-114">Aby uzyskać więcej informacji, zobacz [Servicefilterattribute](../mvc/controllers/filters.md#servicefilterattribute) i [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="52782-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="52782-115">Podczas gdy konstruktory stron i oprogramowanie pośredniczące umożliwiają wykonywanie kodu niestandardowego przed wykonaniem metody obsługi, tylko Razor filtry strony umożliwiają dostęp do <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> i strony.</span><span class="sxs-lookup"><span data-stu-id="52782-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="52782-116">Oprogramowanie pośredniczące ma dostęp do programu `HttpContext` , ale nie do "kontekstu strony".</span><span class="sxs-lookup"><span data-stu-id="52782-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="52782-117">Filtry mają <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parametr pochodny, który zapewnia dostęp do `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="52782-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="52782-118">Oto przykład dla filtru strony: [implementuje atrybut filtru](#ifa) , który dodaje nagłówek do odpowiedzi, co nie można wykonać za pomocą konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="52782-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="52782-119">Dostęp do kontekstu strony, który obejmuje dostęp do wystąpień strony i jej modelu, jest dostępny tylko w przypadku wykonywania filtrów, programów obsługi lub treści Razor strony.</span><span class="sxs-lookup"><span data-stu-id="52782-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="52782-120">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52782-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="52782-121">Razor Filtry stron oferują następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="52782-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="52782-122">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="52782-122">Synchronous methods:</span></span>

  * <span data-ttu-id="52782-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : wywołuje się po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="52782-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : wywoływana przed wykonaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="52782-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : wywoływane po wykonaniu metody obsługi przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="52782-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="52782-126">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="52782-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="52782-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : wywołano asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="52782-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : wywołano asynchronicznie przed wywołaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="52782-129">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="52782-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="52782-130">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="52782-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="52782-131">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="52782-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="52782-132">W przypadku implementacji obu interfejsów są wywoływane tylko metody asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="52782-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="52782-133">Ta sama reguła dotyczy zastąpień na stronach, implementuje synchroniczną lub asynchroniczną wersję przesłonięcia, a nie obu.</span><span class="sxs-lookup"><span data-stu-id="52782-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="52782-134">Implementowanie Razor filtrów stron globalnie</span><span class="sxs-lookup"><span data-stu-id="52782-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="52782-135">Poniższy kod implementuje `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="52782-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="52782-136">W poprzednim kodzie `ProcessUserAgent.Write` jest podany przez użytkownika kod, który działa z ciągiem agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="52782-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="52782-137">Poniższy kod włącza `SampleAsyncPageFilter` w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="52782-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="52782-138">Poniższy kod wywołuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> , aby zastosować `SampleAsyncPageFilter` tylko do stron w */Movies*:</span><span class="sxs-lookup"><span data-stu-id="52782-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="52782-139">Poniższy kod implementuje synchroniczne `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="52782-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="52782-140">Poniższy kod włącza `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="52782-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="52782-141">Implementowanie Razor filtrów stron przez zastępowanie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="52782-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="52782-142">Poniższy kod przesłania asynchroniczne Razor filtry stron:</span><span class="sxs-lookup"><span data-stu-id="52782-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="52782-143">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="52782-143">Implement a filter attribute</span></span>

<span data-ttu-id="52782-144">Wbudowany filtr filtru oparty na atrybutach <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="52782-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="52782-145">Poniższy filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="52782-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="52782-146">Poniższy kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="52782-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="52782-147">Użyj narzędzia, takiego jak narzędzia deweloperskie przeglądarki, aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="52782-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="52782-148">W obszarze **nagłówki odpowiedzi** `author: Rick` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="52782-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="52782-149">Zobacz [przesłanianie domyślnej kolejności,](xref:mvc/controllers/filters#overriding-the-default-order) Aby uzyskać instrukcje dotyczące zastępowania kolejności.</span><span class="sxs-lookup"><span data-stu-id="52782-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="52782-150">Zobacz [Anulowanie i krótkie obwody](xref:mvc/controllers/filters#cancellation-and-short-circuiting) , aby uzyskać instrukcje dotyczące krótkiego obwodu potoku filtru z filtru.</span><span class="sxs-lookup"><span data-stu-id="52782-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="52782-151">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="52782-151">Authorize filter attribute</span></span>

<span data-ttu-id="52782-152">Atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="52782-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52782-153">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52782-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="52782-154">Razor[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają Razor stronom uruchamianie kodu przed Razor uruchomieniem programu obsługi stron i po nim.</span><span class="sxs-lookup"><span data-stu-id="52782-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="52782-155">Razor Filtry stron są podobne do [ASP.NET Core filtrów akcji MVC](xref:mvc/controllers/filters#action-filters), z wyjątkiem sytuacji, gdy nie można ich zastosować do metod obsługi poszczególnych stron.</span><span class="sxs-lookup"><span data-stu-id="52782-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="52782-156">Razor Filtry stron:</span><span class="sxs-lookup"><span data-stu-id="52782-156">Razor Page filters:</span></span>

* <span data-ttu-id="52782-157">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="52782-158">Uruchom kod przed wykonaniem metody obsługi, po utworzeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="52782-159">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="52782-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="52782-160">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="52782-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="52782-161">Nie można zastosować do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="52782-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="52782-162">Kod można uruchomić przed wykonaniem metody obsługi przy użyciu konstruktora stron lub oprogramowania pośredniczącego, ale tylko Razor filtry strony mają dostęp do elementu [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="52782-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="52782-163">Filtry mają parametr pochodny [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) , który zapewnia dostęp do `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="52782-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="52782-164">Na przykład, implementacja przykładu [atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, co nie jest możliwe za pomocą konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="52782-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="52782-165">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52782-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="52782-166">Razor Filtry stron oferują następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="52782-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="52782-167">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="52782-167">Synchronous methods:</span></span>

  * <span data-ttu-id="52782-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : wywołuje się po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="52782-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : wywoływana przed wykonaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="52782-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : wywoływane po wykonaniu metody obsługi przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="52782-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="52782-171">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="52782-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="52782-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : wywołano asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="52782-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : wywołano asynchronicznie przed wywołaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="52782-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="52782-174">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a nie** obu.</span><span class="sxs-lookup"><span data-stu-id="52782-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="52782-175">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="52782-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="52782-176">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="52782-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="52782-177">W przypadku implementacji obu interfejsów są wywoływane tylko metody asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="52782-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="52782-178">Ta sama reguła dotyczy zastąpień na stronach, implementuje synchroniczną lub asynchroniczną wersję przesłonięcia, a nie obu.</span><span class="sxs-lookup"><span data-stu-id="52782-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="52782-179">Implementowanie Razor filtrów stron globalnie</span><span class="sxs-lookup"><span data-stu-id="52782-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="52782-180">Poniższy kod implementuje `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="52782-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="52782-181">W poprzednim kodzie [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="52782-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="52782-182">Jest on używany w przykładzie w celu zapewnienia informacji o śledzeniu dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="52782-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="52782-183">Poniższy kod włącza `SampleAsyncPageFilter` w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="52782-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="52782-184">Poniższy kod przedstawia kompletną `Startup` klasę:</span><span class="sxs-lookup"><span data-stu-id="52782-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="52782-185">Poniższy kod wywołuje `AddFolderApplicationModelConvention` , aby zastosować `SampleAsyncPageFilter` tylko do stron w */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="52782-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="52782-186">Poniższy kod implementuje synchroniczne `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="52782-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="52782-187">Poniższy kod włącza `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="52782-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="52782-188">Implementowanie Razor filtrów stron przez zastępowanie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="52782-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="52782-189">Poniższy kod przesłania synchroniczne Razor filtry stron:</span><span class="sxs-lookup"><span data-stu-id="52782-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="52782-190">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="52782-190">Implement a filter attribute</span></span>

<span data-ttu-id="52782-191">Wbudowany filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru oparty na atrybutach może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="52782-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="52782-192">Poniższy filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="52782-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="52782-193">Poniższy kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="52782-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="52782-194">Zobacz [przesłanianie domyślnej kolejności,](xref:mvc/controllers/filters#overriding-the-default-order) Aby uzyskać instrukcje dotyczące zastępowania kolejności.</span><span class="sxs-lookup"><span data-stu-id="52782-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="52782-195">Zobacz [Anulowanie i krótkie obwody](xref:mvc/controllers/filters#cancellation-and-short-circuiting) , aby uzyskać instrukcje dotyczące krótkiego obwodu potoku filtru z filtru.</span><span class="sxs-lookup"><span data-stu-id="52782-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="52782-196">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="52782-196">Authorize filter attribute</span></span>

<span data-ttu-id="52782-197">Atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="52782-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end