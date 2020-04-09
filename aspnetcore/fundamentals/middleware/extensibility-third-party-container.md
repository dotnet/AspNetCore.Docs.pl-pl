---
title: Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnie typizowanego oprogramowania pośredniczącego z aktywacją fabryczną i kontenerem innej firmy w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a5c5bf6dff6ef795add075df932dd625129ef793
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663136"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="98c4a-103">Aktywacja oprogramowania pośredniczącego za pomocą kontenera innej firmy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98c4a-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="98c4a-104">W tym artykule <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> pokazano, jak używać i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punkt rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) w kontenerze innej firmy.</span><span class="sxs-lookup"><span data-stu-id="98c4a-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="98c4a-105">Aby uzyskać informacje `IMiddlewareFactory` wprowadzające na i `IMiddleware`, zobacz <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="98c4a-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="98c4a-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="98c4a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="98c4a-107">Przykładowa aplikacja pokazuje aktywację `IMiddlewareFactory` oprogramowania `SimpleInjectorMiddlewareFactory`pośredniczącego przez implementację, .</span><span class="sxs-lookup"><span data-stu-id="98c4a-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="98c4a-108">Próbka używa kontenera zależności prostego [wtryskiwacza](https://simpleinjector.org) (DI).</span><span class="sxs-lookup"><span data-stu-id="98c4a-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="98c4a-109">Implementacja oprogramowania pośredniczącego w przykładzie rejestruje`key`wartość dostarczoną przez parametr ciągu zapytania ( ).</span><span class="sxs-lookup"><span data-stu-id="98c4a-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="98c4a-110">Oprogramowanie pośredniczące używa kontekstu wstrzykniętej bazy danych (usługa o określonym zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="98c4a-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="98c4a-111">Przykładowa aplikacja używa [prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector) wyłącznie do celów demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="98c4a-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="98c4a-112">Korzystanie z Prostego wtryskiwacza nie jest potwierdzeniem.</span><span class="sxs-lookup"><span data-stu-id="98c4a-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="98c4a-113">Metody aktywacji oprogramowania pośredniczącego opisane w dokumentacji prostego wtryskiwacza i problemów z githubem są zalecane przez opiekunów simple injector.</span><span class="sxs-lookup"><span data-stu-id="98c4a-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="98c4a-114">Aby uzyskać więcej informacji, zobacz [dokumentację prostego wtryskiwacza](https://simpleinjector.readthedocs.io/en/latest/index.html) i [repozytor github prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="98c4a-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="98c4a-115">IMiddlewareFactory (IMiddlewareFactory)</span><span class="sxs-lookup"><span data-stu-id="98c4a-115">IMiddlewareFactory</span></span>

<span data-ttu-id="98c4a-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="98c4a-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="98c4a-117">W przykładowej aplikacji fabryka oprogramowania pośredniczącego jest implementowana w celu utworzenia `SimpleInjectorActivatedMiddleware` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="98c4a-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="98c4a-118">Fabryka oprogramowania pośredniczącego używa kontenera Prostego wtryskiwacza do rozwiązania oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="98c4a-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="98c4a-119">IMiddleware ( IMiddleware )</span><span class="sxs-lookup"><span data-stu-id="98c4a-119">IMiddleware</span></span>

<span data-ttu-id="98c4a-120"><xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="98c4a-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="98c4a-121">Oprogramowanie pośredniczące `IMiddlewareFactory` aktywowane przez implementację (*Oprogramowanie pośredniczące / SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="98c4a-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="98c4a-122">Rozszerzenie jest tworzone dla oprogramowania pośredniczącego *(Oprogramowanie pośredniczące / MiddlewareExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="98c4a-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="98c4a-123">`Startup.ConfigureServices`musi wykonywać kilka zadań:</span><span class="sxs-lookup"><span data-stu-id="98c4a-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="98c4a-124">Skonfiguruj pojemnik prostego wtryskiwacza.</span><span class="sxs-lookup"><span data-stu-id="98c4a-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="98c4a-125">Zarejestruj fabrykę i oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="98c4a-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="98c4a-126">Udostępnij kontekst bazy danych aplikacji z kontenera Prostego wtryskiwacza.</span><span class="sxs-lookup"><span data-stu-id="98c4a-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="98c4a-127">Oprogramowanie pośredniczące jest zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:</span><span class="sxs-lookup"><span data-stu-id="98c4a-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="98c4a-128">W tym artykule <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> pokazano, jak używać i <xref:Microsoft.AspNetCore.Http.IMiddleware> jako punkt rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) w kontenerze innej firmy.</span><span class="sxs-lookup"><span data-stu-id="98c4a-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="98c4a-129">Aby uzyskać informacje `IMiddlewareFactory` wprowadzające na i `IMiddleware`, zobacz <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="98c4a-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="98c4a-130">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="98c4a-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="98c4a-131">Przykładowa aplikacja pokazuje aktywację `IMiddlewareFactory` oprogramowania `SimpleInjectorMiddlewareFactory`pośredniczącego przez implementację, .</span><span class="sxs-lookup"><span data-stu-id="98c4a-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="98c4a-132">Próbka używa kontenera zależności prostego [wtryskiwacza](https://simpleinjector.org) (DI).</span><span class="sxs-lookup"><span data-stu-id="98c4a-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="98c4a-133">Implementacja oprogramowania pośredniczącego w przykładzie rejestruje`key`wartość dostarczoną przez parametr ciągu zapytania ( ).</span><span class="sxs-lookup"><span data-stu-id="98c4a-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="98c4a-134">Oprogramowanie pośredniczące używa kontekstu wstrzykniętej bazy danych (usługa o określonym zakresie) do rejestrowania wartości ciągu zapytania w bazie danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="98c4a-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="98c4a-135">Przykładowa aplikacja używa [prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector) wyłącznie do celów demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="98c4a-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="98c4a-136">Korzystanie z Prostego wtryskiwacza nie jest potwierdzeniem.</span><span class="sxs-lookup"><span data-stu-id="98c4a-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="98c4a-137">Metody aktywacji oprogramowania pośredniczącego opisane w dokumentacji prostego wtryskiwacza i problemów z githubem są zalecane przez opiekunów simple injector.</span><span class="sxs-lookup"><span data-stu-id="98c4a-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="98c4a-138">Aby uzyskać więcej informacji, zobacz [dokumentację prostego wtryskiwacza](https://simpleinjector.readthedocs.io/en/latest/index.html) i [repozytor github prostego wtryskiwacza](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="98c4a-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="98c4a-139">IMiddlewareFactory (IMiddlewareFactory)</span><span class="sxs-lookup"><span data-stu-id="98c4a-139">IMiddlewareFactory</span></span>

<span data-ttu-id="98c4a-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="98c4a-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="98c4a-141">W przykładowej aplikacji fabryka oprogramowania pośredniczącego jest implementowana w celu utworzenia `SimpleInjectorActivatedMiddleware` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="98c4a-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="98c4a-142">Fabryka oprogramowania pośredniczącego używa kontenera Prostego wtryskiwacza do rozwiązania oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="98c4a-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="98c4a-143">IMiddleware ( IMiddleware )</span><span class="sxs-lookup"><span data-stu-id="98c4a-143">IMiddleware</span></span>

<span data-ttu-id="98c4a-144"><xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="98c4a-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="98c4a-145">Oprogramowanie pośredniczące `IMiddlewareFactory` aktywowane przez implementację (*Oprogramowanie pośredniczące / SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="98c4a-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="98c4a-146">Rozszerzenie jest tworzone dla oprogramowania pośredniczącego *(Oprogramowanie pośredniczące / MiddlewareExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="98c4a-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="98c4a-147">`Startup.ConfigureServices`musi wykonywać kilka zadań:</span><span class="sxs-lookup"><span data-stu-id="98c4a-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="98c4a-148">Skonfiguruj pojemnik prostego wtryskiwacza.</span><span class="sxs-lookup"><span data-stu-id="98c4a-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="98c4a-149">Zarejestruj fabrykę i oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="98c4a-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="98c4a-150">Udostępnij kontekst bazy danych aplikacji z kontenera Prostego wtryskiwacza.</span><span class="sxs-lookup"><span data-stu-id="98c4a-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="98c4a-151">Oprogramowanie pośredniczące jest zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:</span><span class="sxs-lookup"><span data-stu-id="98c4a-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="98c4a-152">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="98c4a-152">Additional resources</span></span>

* [<span data-ttu-id="98c4a-153">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="98c4a-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="98c4a-154">Fabryczna aktywacja oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="98c4a-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="98c4a-155">Proste repozytor GitHub wtryskiwacza</span><span class="sxs-lookup"><span data-stu-id="98c4a-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="98c4a-156">Prosta dokumentacja wtryskiwacza</span><span class="sxs-lookup"><span data-stu-id="98c4a-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
