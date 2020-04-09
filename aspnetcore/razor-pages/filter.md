---
title: Metody filtrowania stron razor w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak tworzyć metody filtrowania stron razor w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660756"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="33966-103">Metody filtrowania stron razor w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33966-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="33966-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33966-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33966-105">Filtry strony razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają stronki Razor uruchamianie kodu przed i po uruchomieniu programu obsługi strony Razor.</span><span class="sxs-lookup"><span data-stu-id="33966-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="33966-106">Filtry strony razor są podobne do [filtrów akcji ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), z tą różnicą, że nie można ich zastosować do poszczególnych metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="33966-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="33966-107">Filtry strony razor:</span><span class="sxs-lookup"><span data-stu-id="33966-107">Razor Page filters:</span></span>

* <span data-ttu-id="33966-108">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="33966-109">Uruchom kod przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="33966-110">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="33966-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="33966-111">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="33966-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="33966-112">Nie można zastosować do określonych metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="33966-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="33966-113">Może mieć zależności konstruktora wypełnione przez [iniekcję zależności](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="33966-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="33966-114">Aby uzyskać więcej informacji, zobacz [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) i [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="33966-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="33966-115">Podczas konstruktorów stron i oprogramowania pośredniczącego umożliwiają wykonywanie kodu niestandardowego przed <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> wykonaniem metody obsługi, tylko filtry razor Page umożliwiają dostęp do i strony.</span><span class="sxs-lookup"><span data-stu-id="33966-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="33966-116">Oprogramowanie pośredniczące `HttpContext`ma dostęp do , ale nie do "kontekstu strony".</span><span class="sxs-lookup"><span data-stu-id="33966-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="33966-117">Filtry mają <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> pochodny parametr, który `HttpContext`zapewnia dostęp do .</span><span class="sxs-lookup"><span data-stu-id="33966-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="33966-118">Na przykład [Implementowanie przykładu atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, coś, czego nie można zrobić z konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="33966-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="33966-119">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33966-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="33966-120">Filtry strony razor zapewniają następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="33966-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="33966-121">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="33966-121">Synchronous methods:</span></span>

  * <span data-ttu-id="33966-122">[OnPageHandlerSelected:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) Wywoływane po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="33966-123">[OnPageHandlerWydajanie:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) Wywoływane przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="33966-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Wywoływane po wykonaniu metody obsługi, przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="33966-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="33966-125">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="33966-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="33966-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="33966-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie przed wywołanie metody obsługi, po zakończeniu wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="33966-128">Zaimplementuj synchroniową lub asynchronizową wersję interfejsu filtru, **a nie** obie. **either**</span><span class="sxs-lookup"><span data-stu-id="33966-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="33966-129">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynkowy, a jeśli tak, to wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="33966-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="33966-130">Jeśli nie, wywołuje metodę(-y) interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="33966-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="33966-131">Jeśli oba interfejsy są implementowane, tylko metody asynchronii są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="33966-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="33966-132">Ta sama reguła ma zastosowanie do zastąpienia na stronach, implementować synchroniczne lub asynchroniczne wersji zastąpienia, nie oba.</span><span class="sxs-lookup"><span data-stu-id="33966-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="33966-133">Globalnie wdrażaj filtry strony razor na całym świecie</span><span class="sxs-lookup"><span data-stu-id="33966-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="33966-134">Poniższy kod `IAsyncPageFilter`implementuje:</span><span class="sxs-lookup"><span data-stu-id="33966-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="33966-135">W poprzednim kodzie `ProcessUserAgent.Write` jest podany przez użytkownika kod, który współpracuje z ciągiem agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="33966-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="33966-136">Poniższy kod `SampleAsyncPageFilter` włącza `Startup` w klasie:</span><span class="sxs-lookup"><span data-stu-id="33966-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="33966-137">Następujące wywołania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> kodu, `SampleAsyncPageFilter` aby zastosować tylko do stron w */Movies:*</span><span class="sxs-lookup"><span data-stu-id="33966-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="33966-138">Poniższy kod implementuje synchroniczne: `IPageFilter`</span><span class="sxs-lookup"><span data-stu-id="33966-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="33966-139">Poniższy kod `SamplePageFilter`umożliwia:</span><span class="sxs-lookup"><span data-stu-id="33966-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="33966-140">Implementowanie filtrów strony razor przez zastąpienie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="33966-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="33966-141">Poniższy kod zastępuje filtry asynchroniiowa strony Razor:</span><span class="sxs-lookup"><span data-stu-id="33966-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="33966-142">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="33966-142">Implement a filter attribute</span></span>

<span data-ttu-id="33966-143">Wbudowany <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtr oparty na atrybutach może być podklasyfikowany.</span><span class="sxs-lookup"><span data-stu-id="33966-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="33966-144">Następujący filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="33966-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="33966-145">Następujący kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="33966-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="33966-146">Użyj narzędzia, takiego jak narzędzia programistyczne przeglądarki, aby sprawdzić nagłówki.</span><span class="sxs-lookup"><span data-stu-id="33966-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="33966-147">W obszarze Nagłówki `author: Rick` **odpowiedzi**zostanie wyświetlona opcja "Nagłówki odpowiedzi".</span><span class="sxs-lookup"><span data-stu-id="33966-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="33966-148">Zobacz [Zastępowanie domyślnej kolejności](xref:mvc/controllers/filters#overriding-the-default-order) instrukcji dotyczących zastępowania zamówienia.</span><span class="sxs-lookup"><span data-stu-id="33966-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="33966-149">Zobacz [Anulowanie i zwarcie,](xref:mvc/controllers/filters#cancellation-and-short-circuiting) aby uzyskać instrukcje dotyczące zwarcia rurociągu filtra z filtra.</span><span class="sxs-lookup"><span data-stu-id="33966-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="33966-150">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="33966-150">Authorize filter attribute</span></span>

<span data-ttu-id="33966-151">Atrybut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować `PageModel`do:</span><span class="sxs-lookup"><span data-stu-id="33966-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="33966-152">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33966-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33966-153">Filtry strony razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają stronki Razor uruchamianie kodu przed i po uruchomieniu programu obsługi strony Razor.</span><span class="sxs-lookup"><span data-stu-id="33966-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="33966-154">Filtry strony razor są podobne do [filtrów akcji ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), z tą różnicą, że nie można ich zastosować do poszczególnych metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="33966-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="33966-155">Filtry strony razor:</span><span class="sxs-lookup"><span data-stu-id="33966-155">Razor Page filters:</span></span>

* <span data-ttu-id="33966-156">Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="33966-157">Uruchom kod przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="33966-158">Uruchom kod po wykonaniu metody obsługi.</span><span class="sxs-lookup"><span data-stu-id="33966-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="33966-159">Można zaimplementować na stronie lub globalnie.</span><span class="sxs-lookup"><span data-stu-id="33966-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="33966-160">Nie można zastosować do określonych metod obsługi strony.</span><span class="sxs-lookup"><span data-stu-id="33966-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="33966-161">Kod można uruchomić, zanim metoda obsługi zostanie wykonana przy użyciu konstruktora strony lub oprogramowania pośredniczącego, ale tylko filtry strony Razor mają dostęp do [protokołu HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="33966-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="33966-162">Filtry mają parametr pochodny [FilterContext,](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) `HttpContext`który zapewnia dostęp do .</span><span class="sxs-lookup"><span data-stu-id="33966-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="33966-163">Na przykład [Implementowanie przykładu atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, coś, czego nie można zrobić z konstruktorów lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="33966-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="33966-164">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33966-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="33966-165">Filtry strony razor zapewniają następujące metody, które mogą być stosowane globalnie lub na poziomie strony:</span><span class="sxs-lookup"><span data-stu-id="33966-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="33966-166">Metody synchroniczne:</span><span class="sxs-lookup"><span data-stu-id="33966-166">Synchronous methods:</span></span>

  * <span data-ttu-id="33966-167">[OnPageHandlerSelected:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) Wywoływane po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="33966-168">[OnPageHandlerWydajanie:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) Wywoływane przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="33966-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Wywoływane po wykonaniu metody obsługi, przed wynikiem akcji.</span><span class="sxs-lookup"><span data-stu-id="33966-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="33966-170">Metody asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="33966-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="33966-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="33966-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie przed wywołanie metody obsługi, po zakończeniu wiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="33966-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="33966-173">Zaimplementuj synchroniową lub asynchronizową wersję interfejsu filtru, a nie obie. **either**</span><span class="sxs-lookup"><span data-stu-id="33966-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="33966-174">Struktura sprawdza najpierw, czy filtr implementuje interfejs asynkowy, a jeśli tak, to wywołuje to.</span><span class="sxs-lookup"><span data-stu-id="33966-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="33966-175">Jeśli nie, wywołuje metodę(-y) interfejsu synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="33966-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="33966-176">Jeśli oba interfejsy są implementowane, tylko metody asynchronii są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="33966-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="33966-177">Ta sama reguła ma zastosowanie do zastąpienia na stronach, implementować synchroniczne lub asynchroniczne wersji zastąpienia, nie oba.</span><span class="sxs-lookup"><span data-stu-id="33966-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="33966-178">Globalnie wdrażaj filtry strony razor na całym świecie</span><span class="sxs-lookup"><span data-stu-id="33966-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="33966-179">Poniższy kod `IAsyncPageFilter`implementuje:</span><span class="sxs-lookup"><span data-stu-id="33966-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="33966-180">W poprzednim kodzie [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="33966-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="33966-181">Jest on używany w próbce, aby zapewnić informacje śledzenia dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="33966-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="33966-182">Poniższy kod `SampleAsyncPageFilter` włącza `Startup` w klasie:</span><span class="sxs-lookup"><span data-stu-id="33966-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="33966-183">Poniższy kod przedstawia `Startup` pełną klasę:</span><span class="sxs-lookup"><span data-stu-id="33966-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="33966-184">Następujący kod `AddFolderApplicationModelConvention` wywołuje zastosowanie `SampleAsyncPageFilter` tylko do stron w */subFolder:*</span><span class="sxs-lookup"><span data-stu-id="33966-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="33966-185">Poniższy kod implementuje synchroniczne: `IPageFilter`</span><span class="sxs-lookup"><span data-stu-id="33966-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="33966-186">Poniższy kod `SamplePageFilter`umożliwia:</span><span class="sxs-lookup"><span data-stu-id="33966-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="33966-187">Implementowanie filtrów strony razor przez zastąpienie metod filtrowania</span><span class="sxs-lookup"><span data-stu-id="33966-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="33966-188">Następujący kod zastępuje filtry synchroniczowej strony Razor:</span><span class="sxs-lookup"><span data-stu-id="33966-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="33966-189">Implementowanie atrybutu filtru</span><span class="sxs-lookup"><span data-stu-id="33966-189">Implement a filter attribute</span></span>

<span data-ttu-id="33966-190">Wbudowany filtr oparty na atrybutach [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtr może być podklasyfikowany.</span><span class="sxs-lookup"><span data-stu-id="33966-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="33966-191">Następujący filtr dodaje nagłówek do odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="33966-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="33966-192">Następujący kod stosuje `AddHeader` atrybut:</span><span class="sxs-lookup"><span data-stu-id="33966-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="33966-193">Zobacz [Zastępowanie domyślnej kolejności](xref:mvc/controllers/filters#overriding-the-default-order) instrukcji dotyczących zastępowania zamówienia.</span><span class="sxs-lookup"><span data-stu-id="33966-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="33966-194">Zobacz [Anulowanie i zwarcie,](xref:mvc/controllers/filters#cancellation-and-short-circuiting) aby uzyskać instrukcje dotyczące zwarcia rurociągu filtra z filtra.</span><span class="sxs-lookup"><span data-stu-id="33966-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="33966-195">Autoryzuj atrybut filtru</span><span class="sxs-lookup"><span data-stu-id="33966-195">Authorize filter attribute</span></span>

<span data-ttu-id="33966-196">Atrybut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować `PageModel`do:</span><span class="sxs-lookup"><span data-stu-id="33966-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
