---
title: Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnego typu oprogramowania pośredniczącego z aktywacją opartą na fabryce i kontenerem innej firmy w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057768"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="685f6-103">Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="685f6-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="685f6-104">W tym artykule przedstawiono sposób użycia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punktu rozszerzalności aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) przy użyciu kontenera innej firmy.</span><span class="sxs-lookup"><span data-stu-id="685f6-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="685f6-105">Aby uzyskać informacje wprowadzające w systemach `IMiddlewareFactory` i `IMiddleware` , zobacz <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="685f6-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="685f6-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="685f6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="685f6-107">Przykładowa aplikacja pokazuje aktywację oprogramowania pośredniczącego przez `IMiddlewareFactory` implementację `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="685f6-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="685f6-108">Przykład używa elementu prostego wtrysku zależności [iniektora](https://simpleinjector.org) (di).</span><span class="sxs-lookup"><span data-stu-id="685f6-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="685f6-109">Implementacja przykładowego oprogramowania pośredniczącego rejestruje wartość podaną przez parametr ciągu zapytania ( `key` ).</span><span class="sxs-lookup"><span data-stu-id="685f6-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="685f6-110">Oprogramowanie pośredniczące używa wstrzykniętego kontekstu bazy danych (usługi w zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="685f6-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="685f6-111">Przykładowa aplikacja używa [prostego iniektora](https://github.com/simpleinjector/SimpleInjector) wyłącznie w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="685f6-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="685f6-112">Użycie prostego iniektora nie jest adnotacją.</span><span class="sxs-lookup"><span data-stu-id="685f6-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="685f6-113">Metody aktywacji oprogramowania pośredniczącego opisane w temacie prosta dokumentacja iniektora i problemy z usługą GitHub są zalecane przez utrzymujący prostego iniektora.</span><span class="sxs-lookup"><span data-stu-id="685f6-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="685f6-114">Aby uzyskać więcej informacji, zobacz [prostą dokumentację iniektora](https://simpleinjector.readthedocs.io/en/latest/index.html) i [prosty repozytorium GitHub](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="685f6-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="685f6-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="685f6-115">IMiddlewareFactory</span></span>

<span data-ttu-id="685f6-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zapewnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="685f6-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="685f6-117">W przykładowej aplikacji jest zaimplementowana fabryka oprogramowania pośredniczącego w celu utworzenia `SimpleInjectorActivatedMiddleware` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="685f6-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="685f6-118">Fabryka programów pośredniczących używa prostego kontenera iniektora do rozpoznawania oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="685f6-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="685f6-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="685f6-119">IMiddleware</span></span>

<span data-ttu-id="685f6-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="685f6-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="685f6-121">Oprogramowanie pośredniczące aktywowane przez `IMiddlewareFactory` implementację ( *oprogramowanie pośredniczące/SimpleInjectorActivatedMiddleware. cs* ):</span><span class="sxs-lookup"><span data-stu-id="685f6-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="685f6-122">Utworzono rozszerzenie dla oprogramowania pośredniczącego ( *oprogramowanie pośredniczące/MiddlewareExtensions. cs* ):</span><span class="sxs-lookup"><span data-stu-id="685f6-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="685f6-123">`Startup.ConfigureServices` należy wykonać kilka zadań:</span><span class="sxs-lookup"><span data-stu-id="685f6-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="685f6-124">Skonfiguruj prosty kontener iniektora.</span><span class="sxs-lookup"><span data-stu-id="685f6-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="685f6-125">Zarejestruj fabrykę i oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="685f6-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="685f6-126">Udostępnienie kontekstu bazy danych aplikacji z prostego kontenera iniektora.</span><span class="sxs-lookup"><span data-stu-id="685f6-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="685f6-127">Oprogramowanie pośredniczące jest zarejestrowane w potoku przetwarzania żądań w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="685f6-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="685f6-128">W tym artykule przedstawiono sposób użycia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punktu rozszerzalności aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) przy użyciu kontenera innej firmy.</span><span class="sxs-lookup"><span data-stu-id="685f6-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="685f6-129">Aby uzyskać informacje wprowadzające w systemach `IMiddlewareFactory` i `IMiddleware` , zobacz <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="685f6-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="685f6-130">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="685f6-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="685f6-131">Przykładowa aplikacja pokazuje aktywację oprogramowania pośredniczącego przez `IMiddlewareFactory` implementację `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="685f6-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="685f6-132">Przykład używa elementu prostego wtrysku zależności [iniektora](https://simpleinjector.org) (di).</span><span class="sxs-lookup"><span data-stu-id="685f6-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="685f6-133">Implementacja przykładowego oprogramowania pośredniczącego rejestruje wartość podaną przez parametr ciągu zapytania ( `key` ).</span><span class="sxs-lookup"><span data-stu-id="685f6-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="685f6-134">Oprogramowanie pośredniczące używa wstrzykniętego kontekstu bazy danych (usługi w zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="685f6-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="685f6-135">Przykładowa aplikacja używa [prostego iniektora](https://github.com/simpleinjector/SimpleInjector) wyłącznie w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="685f6-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="685f6-136">Użycie prostego iniektora nie jest adnotacją.</span><span class="sxs-lookup"><span data-stu-id="685f6-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="685f6-137">Metody aktywacji oprogramowania pośredniczącego opisane w temacie prosta dokumentacja iniektora i problemy z usługą GitHub są zalecane przez utrzymujący prostego iniektora.</span><span class="sxs-lookup"><span data-stu-id="685f6-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="685f6-138">Aby uzyskać więcej informacji, zobacz [prostą dokumentację iniektora](https://simpleinjector.readthedocs.io/en/latest/index.html) i [prosty repozytorium GitHub](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="685f6-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="685f6-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="685f6-139">IMiddlewareFactory</span></span>

<span data-ttu-id="685f6-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zapewnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="685f6-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="685f6-141">W przykładowej aplikacji jest zaimplementowana fabryka oprogramowania pośredniczącego w celu utworzenia `SimpleInjectorActivatedMiddleware` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="685f6-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="685f6-142">Fabryka programów pośredniczących używa prostego kontenera iniektora do rozpoznawania oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="685f6-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="685f6-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="685f6-143">IMiddleware</span></span>

<span data-ttu-id="685f6-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="685f6-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="685f6-145">Oprogramowanie pośredniczące aktywowane przez `IMiddlewareFactory` implementację ( *oprogramowanie pośredniczące/SimpleInjectorActivatedMiddleware. cs* ):</span><span class="sxs-lookup"><span data-stu-id="685f6-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="685f6-146">Utworzono rozszerzenie dla oprogramowania pośredniczącego ( *oprogramowanie pośredniczące/MiddlewareExtensions. cs* ):</span><span class="sxs-lookup"><span data-stu-id="685f6-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="685f6-147">`Startup.ConfigureServices` należy wykonać kilka zadań:</span><span class="sxs-lookup"><span data-stu-id="685f6-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="685f6-148">Skonfiguruj prosty kontener iniektora.</span><span class="sxs-lookup"><span data-stu-id="685f6-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="685f6-149">Zarejestruj fabrykę i oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="685f6-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="685f6-150">Udostępnienie kontekstu bazy danych aplikacji z prostego kontenera iniektora.</span><span class="sxs-lookup"><span data-stu-id="685f6-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="685f6-151">Oprogramowanie pośredniczące jest zarejestrowane w potoku przetwarzania żądań w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="685f6-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="685f6-152">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="685f6-152">Additional resources</span></span>

* [<span data-ttu-id="685f6-153">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="685f6-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="685f6-154">Aktywacja oprogramowania pośredniczącego oparta na fabryce</span><span class="sxs-lookup"><span data-stu-id="685f6-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="685f6-155">Proste repozytorium GitHub iniektora</span><span class="sxs-lookup"><span data-stu-id="685f6-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="685f6-156">Prosta dokumentacja iniektora</span><span class="sxs-lookup"><span data-stu-id="685f6-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
