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
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>Metody filtrowania stron razor w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Filtry strony razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają stronki Razor uruchamianie kodu przed i po uruchomieniu programu obsługi strony Razor. Filtry strony razor są podobne do [filtrów akcji ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), z tą różnicą, że nie można ich zastosować do poszczególnych metod obsługi strony.

Filtry strony razor:

* Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.
* Uruchom kod przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.
* Uruchom kod po wykonaniu metody obsługi.
* Można zaimplementować na stronie lub globalnie.
* Nie można zastosować do określonych metod obsługi strony.
* Może mieć zależności konstruktora wypełnione przez [iniekcję zależności](xref:fundamentals/dependency-injection) (DI). Aby uzyskać więcej informacji, zobacz [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) i [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).

Podczas konstruktorów stron i oprogramowania pośredniczącego umożliwiają wykonywanie kodu niestandardowego przed <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> wykonaniem metody obsługi, tylko filtry razor Page umożliwiają dostęp do i strony. Oprogramowanie pośredniczące `HttpContext`ma dostęp do , ale nie do "kontekstu strony". Filtry mają <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> pochodny parametr, który `HttpContext`zapewnia dostęp do . Na przykład [Implementowanie przykładu atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, coś, czego nie można zrobić z konstruktorów lub oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))

Filtry strony razor zapewniają następujące metody, które mogą być stosowane globalnie lub na poziomie strony:

* Metody synchroniczne:

  * [OnPageHandlerSelected:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) Wywoływane po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.
  * [OnPageHandlerWydajanie:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) Wywoływane przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Wywoływane po wykonaniu metody obsługi, przed wynikiem akcji.

* Metody asynchroniczne:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie przed wywołanie metody obsługi, po zakończeniu wiązania modelu.

Zaimplementuj synchroniową lub asynchronizową wersję interfejsu filtru, **a nie** obie. **either** Struktura sprawdza najpierw, czy filtr implementuje interfejs asynkowy, a jeśli tak, to wywołuje to. Jeśli nie, wywołuje metodę(-y) interfejsu synchronicznego. Jeśli oba interfejsy są implementowane, tylko metody asynchronii są wywoływane. Ta sama reguła ma zastosowanie do zastąpienia na stronach, implementować synchroniczne lub asynchroniczne wersji zastąpienia, nie oba.

## <a name="implement-razor-page-filters-globally"></a>Globalnie wdrażaj filtry strony razor na całym świecie

Poniższy kod `IAsyncPageFilter`implementuje:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

W poprzednim kodzie `ProcessUserAgent.Write` jest podany przez użytkownika kod, który współpracuje z ciągiem agenta użytkownika.

Poniższy kod `SampleAsyncPageFilter` włącza `Startup` w klasie:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

Następujące wywołania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> kodu, `SampleAsyncPageFilter` aby zastosować tylko do stron w */Movies:*

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Poniższy kod implementuje synchroniczne: `IPageFilter`

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Poniższy kod `SamplePageFilter`umożliwia:

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementowanie filtrów strony razor przez zastąpienie metod filtrowania

Poniższy kod zastępuje filtry asynchroniiowa strony Razor:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementowanie atrybutu filtru

Wbudowany <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtr oparty na atrybutach może być podklasyfikowany. Następujący filtr dodaje nagłówek do odpowiedzi:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Następujący kod stosuje `AddHeader` atrybut:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

Użyj narzędzia, takiego jak narzędzia programistyczne przeglądarki, aby sprawdzić nagłówki. W obszarze Nagłówki `author: Rick` **odpowiedzi**zostanie wyświetlona opcja "Nagłówki odpowiedzi".

Zobacz [Zastępowanie domyślnej kolejności](xref:mvc/controllers/filters#overriding-the-default-order) instrukcji dotyczących zastępowania zamówienia.

Zobacz [Anulowanie i zwarcie,](xref:mvc/controllers/filters#cancellation-and-short-circuiting) aby uzyskać instrukcje dotyczące zwarcia rurociągu filtra z filtra.

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autoryzuj atrybut filtru

Atrybut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować `PageModel`do:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Filtry strony razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) i [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) umożliwiają stronki Razor uruchamianie kodu przed i po uruchomieniu programu obsługi strony Razor. Filtry strony razor są podobne do [filtrów akcji ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), z tą różnicą, że nie można ich zastosować do poszczególnych metod obsługi strony.

Filtry strony razor:

* Uruchom kod po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.
* Uruchom kod przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.
* Uruchom kod po wykonaniu metody obsługi.
* Można zaimplementować na stronie lub globalnie.
* Nie można zastosować do określonych metod obsługi strony.

Kod można uruchomić, zanim metoda obsługi zostanie wykonana przy użyciu konstruktora strony lub oprogramowania pośredniczącego, ale tylko filtry strony Razor mają dostęp do [protokołu HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filtry mają parametr pochodny [FilterContext,](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) `HttpContext`który zapewnia dostęp do . Na przykład [Implementowanie przykładu atrybutu filtru](#ifa) dodaje nagłówek do odpowiedzi, coś, czego nie można zrobić z konstruktorów lub oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([jak pobrać](xref:index#how-to-download-a-sample))

Filtry strony razor zapewniają następujące metody, które mogą być stosowane globalnie lub na poziomie strony:

* Metody synchroniczne:

  * [OnPageHandlerSelected:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) Wywoływane po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.
  * [OnPageHandlerWydajanie:](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) Wywoływane przed wykonaniem metody obsługi, po zakończeniu wiązania modelu.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Wywoływane po wykonaniu metody obsługi, przed wynikiem akcji.

* Metody asynchroniczne:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie po wybraniu metody obsługi, ale przed wystąpieniem powiązania modelu.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Wywoływane asynchronicznie przed wywołanie metody obsługi, po zakończeniu wiązania modelu.

> [!NOTE]
> Zaimplementuj synchroniową lub asynchronizową wersję interfejsu filtru, a nie obie. **either** Struktura sprawdza najpierw, czy filtr implementuje interfejs asynkowy, a jeśli tak, to wywołuje to. Jeśli nie, wywołuje metodę(-y) interfejsu synchronicznego. Jeśli oba interfejsy są implementowane, tylko metody asynchronii są wywoływane. Ta sama reguła ma zastosowanie do zastąpienia na stronach, implementować synchroniczne lub asynchroniczne wersji zastąpienia, nie oba.

## <a name="implement-razor-page-filters-globally"></a>Globalnie wdrażaj filtry strony razor na całym świecie

Poniższy kod `IAsyncPageFilter`implementuje:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

W poprzednim kodzie [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nie jest wymagane. Jest on używany w próbce, aby zapewnić informacje śledzenia dla aplikacji.

Poniższy kod `SampleAsyncPageFilter` włącza `Startup` w klasie:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Poniższy kod przedstawia `Startup` pełną klasę:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Następujący kod `AddFolderApplicationModelConvention` wywołuje zastosowanie `SampleAsyncPageFilter` tylko do stron w */subFolder:*

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Poniższy kod implementuje synchroniczne: `IPageFilter`

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Poniższy kod `SamplePageFilter`umożliwia:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementowanie filtrów strony razor przez zastąpienie metod filtrowania

Następujący kod zastępuje filtry synchroniczowej strony Razor:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementowanie atrybutu filtru

Wbudowany filtr oparty na atrybutach [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtr może być podklasyfikowany. Następujący filtr dodaje nagłówek do odpowiedzi:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Następujący kod stosuje `AddHeader` atrybut:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Zobacz [Zastępowanie domyślnej kolejności](xref:mvc/controllers/filters#overriding-the-default-order) instrukcji dotyczących zastępowania zamówienia.

Zobacz [Anulowanie i zwarcie,](xref:mvc/controllers/filters#cancellation-and-short-circuiting) aby uzyskać instrukcje dotyczące zwarcia rurociągu filtra z filtra. 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autoryzuj atrybut filtru

Atrybut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) można zastosować `PageModel`do:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
