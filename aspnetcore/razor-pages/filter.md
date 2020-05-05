---
title: Metody filtrowania dla Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak tworzyć metody Razor filtrowania dla stron w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: 68962d5a3a49e52510d72899e7dead2c1983d8b6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775521"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="5eec4-103">Metody filtrowania dla Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eec4-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5eec4-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5eec4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="5eec4-105">[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają Razor stronom uruchamianie kodu przed uruchomieniem programu obsługi stron Razor i po nim.</span><span class="sxs-lookup"><span data-stu-id="5eec4-105"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="5eec4-106">Filtry stron są podobne do [ASP.NET Core filtrów akcji MVC](xref:mvc/controllers/filters#action-filters), z wyjątkiem sytuacji, gdy nie można ich zastosować do metod obsługi poszczególnych stron.</span><span class="sxs-lookup"><span data-stu-id="5eec4-106"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="5eec4-107">Filtry stron:</span><span class="sxs-lookup"><span data-stu-id="5eec4-107"> Page filters:</span></span>

* <span data-ttu-id="5eec4-108">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="5eec4-109">Uruchom kod przed wykonaniem metody obsługi, po utworzeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="5eec4-110">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="5eec4-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="5eec4-111">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="5eec4-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="5eec4-112">Nie można zastosować do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="5eec4-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="5eec4-113">Może mieć zależności konstruktora wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="5eec4-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="5eec4-114">Aby uzyskać więcej informacji, zobacz [Servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) i [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="5eec4-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="5eec4-115">Podczas gdy konstruktory stron i oprogramowanie pośredniczące umożliwiają wykonywanie kodu niestandardowego przed wykonaniem metody Razor obsługi, tylko filtry strony <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> umożliwiają dostęp do i strony.</span><span class="sxs-lookup"><span data-stu-id="5eec4-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="5eec4-116">Oprogramowanie pośredniczące ma dostęp do `HttpContext`programu, ale nie do "kontekstu strony".</span><span class="sxs-lookup"><span data-stu-id="5eec4-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="5eec4-117">Filtry mają parametr <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> pochodny, który zapewnia dostęp do `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="5eec4-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="5eec4-118">Na przykład, implementacja przykładu [atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, co nie jest możliwe za pomocą konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="5eec4-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="5eec4-119">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5eec4-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="5eec4-120">Filtry stron oferują następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="5eec4-120"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="5eec4-121">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="5eec4-121">Synchronous methods:</span></span>

  * <span data-ttu-id="5eec4-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : wywołuje się po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5eec4-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : wywoływana przed wykonaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="5eec4-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : wywoływane po wykonaniu metody obsługi przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="5eec4-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="5eec4-125">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="5eec4-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="5eec4-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : wywołano asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5eec4-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : wywołano asynchronicznie przed wywołaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="5eec4-128">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="5eec4-129">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="5eec4-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="5eec4-130">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="5eec4-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="5eec4-131">W przypadku implementacji obu interfejsów są wywoływane tylko metody asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="5eec4-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="5eec4-132">Ta sama reguła dotyczy zastąpień na stronach, implementuje synchroniczną lub asynchroniczną wersję przesłonięcia, a nie obu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="5eec4-133">Implementowanie Razor filtrów stron globalnie</span><span class="sxs-lookup"><span data-stu-id="5eec4-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="5eec4-134">Poniższy kod implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="5eec4-135">W poprzednim kodzie `ProcessUserAgent.Write` jest podany przez użytkownika kod, który działa z ciągiem agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5eec4-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="5eec4-136">Poniższy kod włącza `SampleAsyncPageFilter` w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="5eec4-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="5eec4-137">Poniższy kod wywołuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> , `SampleAsyncPageFilter` aby zastosować tylko do stron w */Movies*:</span><span class="sxs-lookup"><span data-stu-id="5eec4-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="5eec4-138">Poniższy kod implementuje synchroniczne `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="5eec4-139">Poniższy kod włącza `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="5eec4-140">Implementowanie Razor filtrów stron przez zastępowanie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="5eec4-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="5eec4-141">Poniższy kod przesłania asynchroniczne Razor filtry stron:</span><span class="sxs-lookup"><span data-stu-id="5eec4-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="5eec4-142">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="5eec4-142">Implement a filter attribute</span></span>

<span data-ttu-id="5eec4-143">Wbudowany filtr filtru <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> oparty na atrybutach może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="5eec4-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="5eec4-144">Poniższy filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="5eec4-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="5eec4-145">Poniższy kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="5eec4-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="5eec4-146">Użyj narzędzia, takiego jak narzędzia deweloperskie przeglądarki, aby przeanalizować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="5eec4-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="5eec4-147">W obszarze **nagłówki odpowiedzi** `author: Rick` jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="5eec4-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="5eec4-148">Zobacz [przesłanianie domyślnej kolejności,](xref:mvc/controllers/filters#overriding-the-default-order) Aby uzyskać instrukcje dotyczące zastępowania kolejności.</span><span class="sxs-lookup"><span data-stu-id="5eec4-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="5eec4-149">Zobacz [Anulowanie i krótkie obwody](xref:mvc/controllers/filters#cancellation-and-short-circuiting) , aby uzyskać instrukcje dotyczące krótkiego obwodu potoku filtru z filtru.</span><span class="sxs-lookup"><span data-stu-id="5eec4-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="5eec4-150">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="5eec4-150">Authorize filter attribute</span></span>

<span data-ttu-id="5eec4-151">Atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować do `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5eec4-152">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5eec4-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="5eec4-153">[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają Razor stronom uruchamianie kodu przed uruchomieniem programu obsługi stron Razor i po nim.</span><span class="sxs-lookup"><span data-stu-id="5eec4-153"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="5eec4-154">Filtry stron są podobne do [ASP.NET Core filtrów akcji MVC](xref:mvc/controllers/filters#action-filters), z wyjątkiem sytuacji, gdy nie można ich zastosować do metod obsługi poszczególnych stron.</span><span class="sxs-lookup"><span data-stu-id="5eec4-154"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="5eec4-155">Filtry stron:</span><span class="sxs-lookup"><span data-stu-id="5eec4-155"> Page filters:</span></span>

* <span data-ttu-id="5eec4-156">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="5eec4-157">Uruchom kod przed wykonaniem metody obsługi, po utworzeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="5eec4-158">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="5eec4-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="5eec4-159">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="5eec4-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="5eec4-160">Nie można zastosować do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="5eec4-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="5eec4-161">Kod można uruchomić przed wykonaniem metody obsługi przy użyciu konstruktora stron lub oprogramowania pośredniczącego, ale tylko Razor filtry strony mają dostęp do elementu [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="5eec4-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="5eec4-162">Filtry mają parametr pochodny [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) , który zapewnia dostęp do `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="5eec4-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="5eec4-163">Na przykład, implementacja przykładu [atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, co nie jest możliwe za pomocą konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="5eec4-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="5eec4-164">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5eec4-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="5eec4-165">Filtry stron oferują następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="5eec4-165"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="5eec4-166">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="5eec4-166">Synchronous methods:</span></span>

  * <span data-ttu-id="5eec4-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : wywołuje się po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5eec4-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : wywoływana przed wykonaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="5eec4-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : wywoływane po wykonaniu metody obsługi przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="5eec4-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="5eec4-170">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="5eec4-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="5eec4-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : wywołano asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5eec4-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : wywołano asynchronicznie przed wywołaniem metody obsługi, po zakończeniu powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="5eec4-173">Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a nie** obu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="5eec4-174">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="5eec4-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="5eec4-175">Jeśli nie, wywołuje metody interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="5eec4-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="5eec4-176">W przypadku implementacji obu interfejsów są wywoływane tylko metody asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="5eec4-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="5eec4-177">Ta sama reguła dotyczy zastąpień na stronach, implementuje synchroniczną lub asynchroniczną wersję przesłonięcia, a nie obu.</span><span class="sxs-lookup"><span data-stu-id="5eec4-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="5eec4-178">Implementowanie Razor filtrów stron globalnie</span><span class="sxs-lookup"><span data-stu-id="5eec4-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="5eec4-179">Poniższy kod implementuje `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="5eec4-180">W poprzednim kodzie [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="5eec4-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="5eec4-181">Jest on używany w przykładzie w celu zapewnienia informacji o śledzeniu dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5eec4-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="5eec4-182">Poniższy kod włącza `SampleAsyncPageFilter` w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="5eec4-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="5eec4-183">Poniższy kod przedstawia kompletną `Startup` klasę:</span><span class="sxs-lookup"><span data-stu-id="5eec4-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="5eec4-184">Poniższy kod wywołuje `AddFolderApplicationModelConvention` , `SampleAsyncPageFilter` aby zastosować tylko do stron w */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="5eec4-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="5eec4-185">Poniższy kod implementuje synchroniczne `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="5eec4-186">Poniższy kod włącza `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="5eec4-187">Implementowanie Razor filtrów stron przez zastępowanie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="5eec4-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="5eec4-188">Poniższy kod przesłania synchroniczne Razor filtry stron:</span><span class="sxs-lookup"><span data-stu-id="5eec4-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="5eec4-189">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="5eec4-189">Implement a filter attribute</span></span>

<span data-ttu-id="5eec4-190">Wbudowany filtr [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtru oparty na atrybutach może być podklasą.</span><span class="sxs-lookup"><span data-stu-id="5eec4-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="5eec4-191">Poniższy filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="5eec4-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="5eec4-192">Poniższy kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="5eec4-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="5eec4-193">Zobacz [przesłanianie domyślnej kolejności,](xref:mvc/controllers/filters#overriding-the-default-order) Aby uzyskać instrukcje dotyczące zastępowania kolejności.</span><span class="sxs-lookup"><span data-stu-id="5eec4-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="5eec4-194">Zobacz [Anulowanie i krótkie obwody](xref:mvc/controllers/filters#cancellation-and-short-circuiting) , aby uzyskać instrukcje dotyczące krótkiego obwodu potoku filtru z filtru.</span><span class="sxs-lookup"><span data-stu-id="5eec4-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="5eec4-195">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="5eec4-195">Authorize filter attribute</span></span>

<span data-ttu-id="5eec4-196">Atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować do `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="5eec4-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
