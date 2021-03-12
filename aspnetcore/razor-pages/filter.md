---
title: Metody filtrowania dla Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak tworzyć metody filtrowania dla Razor stron w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: 178e6348d2d50dae34feea6a0ed261de01037136
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586140"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="fc133-103">Metody filtrowania dla Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc133-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc133-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fc133-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fc133-105">Razor[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają Razor stronom uruchamianie kodu przed Razor uruchomieniem programu obsługi stron i po nim.</span><span class="sxs-lookup"><span data-stu-id="fc133-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="fc133-106">Razor Filtry stron są podobne do [ASP.NET Core filtrów akcji MVC](xref:mvc/controllers/filters#action-filters), z wyjątkiem sytuacji, gdy nie można ich zastosować do metod obsługi poszczególnych stron.</span><span class="sxs-lookup"><span data-stu-id="fc133-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="fc133-107">Razor Filtry stron:</span><span class="sxs-lookup"><span data-stu-id="fc133-107">Razor Page filters:</span></span>

* <span data-ttu-id="fc133-108">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="fc133-109">Uruchom kod przed wykonaniem metody obsługi, po utworzeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="fc133-110">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="fc133-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="fc133-111">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="fc133-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="fc133-112">Nie można zastosować do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="fc133-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="fc133-113">Może mieć zależności konstruktora wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="fc133-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="fc133-114">Aby uzyskać więcej informacji, zobacz [Servicefilterattribute](../mvc/controllers/filters.md#servicefilterattribute) i [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="fc133-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="fc133-115">Podczas gdy konstruktory stron i oprogramowanie pośredniczące umożliwiają wykonywanie kodu niestandardowego przed wykonaniem metody obsługi, tylko Razor filtry strony umożliwiają dostęp do <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> i strony.</span><span class="sxs-lookup"><span data-stu-id="fc133-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="fc133-116">Oprogramowanie pośredniczące ma dostęp do programu `HttpContext` , ale nie do "kontekstu strony".</span><span class="sxs-lookup"><span data-stu-id="fc133-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="fc133-117">Filtry mają <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parametr pochodny, który zapewnia dostęp do `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="fc133-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="fc133-118">Oto przykład dla filtru strony: [implementuje atrybut filtru](#ifa) , który dodaje nagłówek do odpowiedzi, co nie można wykonać za pomocą konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="fc133-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="fc133-119">Dostęp do kontekstu strony, który obejmuje dostęp do wystąpień strony i jej modelu, jest dostępny tylko w przypadku wykonywania filtrów, programów obsługi lub treści Razor strony.</span><span class="sxs-lookup"><span data-stu-id="fc133-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="fc133-120">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc133-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fc133-121">Razor Filtry stron oferują następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="fc133-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="fc133-122">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="fc133-122">Synchronous methods:</span></span>

  * <span data-ttu-id="fc133-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : wywołuje się po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fc133-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : wywoływana przed wykonaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="fc133-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : wywoływane po wykonaniu metody obsługi przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="fc133-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="fc133-126">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="fc133-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="fc133-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : wywołano asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fc133-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : wywołano asynchronicznie przed wywołaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="fc133-129">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="fc133-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="fc133-130">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="fc133-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="fc133-131">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="fc133-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="fc133-132">W przypadku implementacji obu interfejsów są wywoływane tylko metody asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="fc133-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="fc133-133">Ta sama reguła dotyczy zastąpień na stronach, implementuje synchroniczną lub asynchroniczną wersję przesłonięcia, a nie obu.</span><span class="sxs-lookup"><span data-stu-id="fc133-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="fc133-134">Implementowanie Razor filtrów stron globalnie</span><span class="sxs-lookup"><span data-stu-id="fc133-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="fc133-135">Poniższy kod implementuje `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="fc133-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="fc133-136">W poprzednim kodzie `ProcessUserAgent.Write` jest podany przez użytkownika kod, który działa z ciągiem agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fc133-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="fc133-137">Poniższy kod włącza `SampleAsyncPageFilter` w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="fc133-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="fc133-138">Poniższy kod wywołuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> , aby zastosować `SampleAsyncPageFilter` tylko do stron w */Movies*:</span><span class="sxs-lookup"><span data-stu-id="fc133-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="fc133-139">Poniższy kod implementuje synchroniczne `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="fc133-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="fc133-140">Poniższy kod włącza `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="fc133-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="fc133-141">Implementowanie Razor filtrów stron przez zastępowanie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="fc133-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="fc133-142">Poniższy kod przesłania asynchroniczne Razor filtry stron:</span><span class="sxs-lookup"><span data-stu-id="fc133-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="fc133-143">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="fc133-143">Implement a filter attribute</span></span>

<span data-ttu-id="fc133-144">Wbudowany filtr filtru oparty na atrybutach <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="fc133-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="fc133-145">Poniższy filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="fc133-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="fc133-146">Poniższy kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="fc133-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="fc133-147">Użyj narzędzia, takiego jak narzędzia deweloperskie przeglądarki, aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="fc133-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="fc133-148">W obszarze **nagłówki odpowiedzi** `author: Rick` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="fc133-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="fc133-149">Zobacz [przesłanianie domyślnej kolejności,](xref:mvc/controllers/filters#overriding-the-default-order) Aby uzyskać instrukcje dotyczące zastępowania kolejności.</span><span class="sxs-lookup"><span data-stu-id="fc133-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="fc133-150">Zobacz [Anulowanie i krótkie obwody](xref:mvc/controllers/filters#cancellation-and-short-circuiting) , aby uzyskać instrukcje dotyczące krótkiego obwodu potoku filtru z filtru.</span><span class="sxs-lookup"><span data-stu-id="fc133-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="fc133-151">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="fc133-151">Authorize filter attribute</span></span>

<span data-ttu-id="fc133-152">Atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="fc133-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc133-153">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fc133-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fc133-154">Razor[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają Razor stronom uruchamianie kodu przed Razor uruchomieniem programu obsługi stron i po nim.</span><span class="sxs-lookup"><span data-stu-id="fc133-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="fc133-155">Razor Filtry stron są podobne do [ASP.NET Core filtrów akcji MVC](xref:mvc/controllers/filters#action-filters), z wyjątkiem sytuacji, gdy nie można ich zastosować do metod obsługi poszczególnych stron.</span><span class="sxs-lookup"><span data-stu-id="fc133-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="fc133-156">Razor Filtry stron:</span><span class="sxs-lookup"><span data-stu-id="fc133-156">Razor Page filters:</span></span>

* <span data-ttu-id="fc133-157">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="fc133-158">Uruchom kod przed wykonaniem metody obsługi, po utworzeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="fc133-159">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="fc133-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="fc133-160">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="fc133-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="fc133-161">Nie można zastosować do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="fc133-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="fc133-162">Kod można uruchomić przed wykonaniem metody obsługi przy użyciu konstruktora stron lub oprogramowania pośredniczącego, ale tylko Razor filtry strony mają dostęp do elementu [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="fc133-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="fc133-163">Filtry mają parametr pochodny [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) , który zapewnia dostęp do `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="fc133-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="fc133-164">Na przykład, implementacja przykładu [atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, co nie jest możliwe za pomocą konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="fc133-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="fc133-165">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc133-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fc133-166">Razor Filtry stron oferują następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="fc133-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="fc133-167">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="fc133-167">Synchronous methods:</span></span>

  * <span data-ttu-id="fc133-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : wywołuje się po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fc133-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : wywoływana przed wykonaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="fc133-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : wywoływane po wykonaniu metody obsługi przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="fc133-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="fc133-171">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="fc133-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="fc133-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : wywołano asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fc133-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : wywołano asynchronicznie przed wywołaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fc133-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="fc133-174">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a nie** obu.</span><span class="sxs-lookup"><span data-stu-id="fc133-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="fc133-175">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="fc133-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="fc133-176">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="fc133-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="fc133-177">W przypadku implementacji obu interfejsów są wywoływane tylko metody asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="fc133-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="fc133-178">Ta sama reguła dotyczy zastąpień na stronach, implementuje synchroniczną lub asynchroniczną wersję przesłonięcia, a nie obu.</span><span class="sxs-lookup"><span data-stu-id="fc133-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="fc133-179">Implementowanie Razor filtrów stron globalnie</span><span class="sxs-lookup"><span data-stu-id="fc133-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="fc133-180">Poniższy kod implementuje `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="fc133-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="fc133-181">W poprzednim kodzie [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="fc133-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="fc133-182">Jest on używany w przykładzie w celu zapewnienia informacji o śledzeniu dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc133-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="fc133-183">Poniższy kod włącza `SampleAsyncPageFilter` w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="fc133-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="fc133-184">Poniższy kod przedstawia kompletną `Startup` klasę:</span><span class="sxs-lookup"><span data-stu-id="fc133-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="fc133-185">Poniższy kod wywołuje `AddFolderApplicationModelConvention` , aby zastosować `SampleAsyncPageFilter` tylko do stron w */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="fc133-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="fc133-186">Poniższy kod implementuje synchroniczne `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="fc133-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="fc133-187">Poniższy kod włącza `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="fc133-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="fc133-188">Implementowanie Razor filtrów stron przez zastępowanie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="fc133-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="fc133-189">Poniższy kod przesłania synchroniczne Razor filtry stron:</span><span class="sxs-lookup"><span data-stu-id="fc133-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="fc133-190">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="fc133-190">Implement a filter attribute</span></span>

<span data-ttu-id="fc133-191">Wbudowany filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru oparty na atrybutach może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="fc133-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="fc133-192">Poniższy filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="fc133-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="fc133-193">Poniższy kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="fc133-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="fc133-194">Zobacz [przesłanianie domyślnej kolejności,](xref:mvc/controllers/filters#overriding-the-default-order) Aby uzyskać instrukcje dotyczące zastępowania kolejności.</span><span class="sxs-lookup"><span data-stu-id="fc133-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="fc133-195">Zobacz [Anulowanie i krótkie obwody](xref:mvc/controllers/filters#cancellation-and-short-circuiting) , aby uzyskać instrukcje dotyczące krótkiego obwodu potoku filtru z filtru.</span><span class="sxs-lookup"><span data-stu-id="fc133-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="fc133-196">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="fc133-196">Authorize filter attribute</span></span>

<span data-ttu-id="fc133-197">Atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="fc133-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end