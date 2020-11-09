---
title: Aktywacja oprogramowania pośredniczącego oparta na fabryce w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnego typu oprogramowania pośredniczącego z implementacją aktywacji opartej na fabryce w ASP.NET Core.
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 125ebbba9fadf0db357fe7c69af7ca0d8ddc8041
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057495"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="1b85b-103">Aktywacja oprogramowania pośredniczącego oparta na fabryce w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b85b-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b85b-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> jest punktem rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="1b85b-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="1b85b-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozszerzające sprawdzają, czy typ zarejestrowanego oprogramowania pośredniczącego jest zaimplementowany <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="1b85b-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="1b85b-106">W <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> takim przypadku wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji.</span><span class="sxs-lookup"><span data-stu-id="1b85b-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="1b85b-107">Oprogramowanie pośredniczące jest rejestrowane jako [Usługa w zakresie lub przejściowym](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b85b-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="1b85b-108">Korzyści:</span><span class="sxs-lookup"><span data-stu-id="1b85b-108">Benefits:</span></span>

* <span data-ttu-id="1b85b-109">Aktywacja na żądanie klienta (iniekcja usług objętych zakresem)</span><span class="sxs-lookup"><span data-stu-id="1b85b-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="1b85b-110">Silne wpisywanie oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="1b85b-110">Strong typing of middleware</span></span>

<span data-ttu-id="1b85b-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> jest aktywowany dla żądania klienta (połączenie), więc usługi w zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1b85b-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="1b85b-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b85b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="1b85b-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="1b85b-113">IMiddleware</span></span>

<span data-ttu-id="1b85b-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b85b-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="1b85b-115">Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) obsługuje żądania i zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą wykonywanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1b85b-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="1b85b-116">Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="1b85b-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="1b85b-117">Oprogramowanie pośredniczące aktywowane przez <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="1b85b-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="1b85b-118">Dla middlewares są tworzone rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="1b85b-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="1b85b-119">Nie jest możliwe przekazanie obiektów do oprogramowania pośredniczącego aktywowanego przez fabrykę przy użyciu <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="1b85b-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="1b85b-120">Oprogramowanie pośredniczące aktywowane fabrycznie jest dodawane do wbudowanego kontenera w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1b85b-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="1b85b-121">Oba middlewares są rejestrowane w potoku przetwarzania żądań w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1b85b-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="1b85b-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="1b85b-122">IMiddlewareFactory</span></span>

<span data-ttu-id="1b85b-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zapewnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1b85b-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="1b85b-124">Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa objęta zakresem.</span><span class="sxs-lookup"><span data-stu-id="1b85b-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="1b85b-125">Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , znajduje się w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="1b85b-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b85b-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> jest punktem rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="1b85b-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="1b85b-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozszerzające sprawdzają, czy typ zarejestrowanego oprogramowania pośredniczącego jest zaimplementowany <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="1b85b-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="1b85b-128">W <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> takim przypadku wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji.</span><span class="sxs-lookup"><span data-stu-id="1b85b-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="1b85b-129">Oprogramowanie pośredniczące jest rejestrowane jako [Usługa w zakresie lub przejściowym](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b85b-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="1b85b-130">Korzyści:</span><span class="sxs-lookup"><span data-stu-id="1b85b-130">Benefits:</span></span>

* <span data-ttu-id="1b85b-131">Aktywacja na żądanie klienta (iniekcja usług objętych zakresem)</span><span class="sxs-lookup"><span data-stu-id="1b85b-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="1b85b-132">Silne wpisywanie oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="1b85b-132">Strong typing of middleware</span></span>

<span data-ttu-id="1b85b-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> jest aktywowany dla żądania klienta (połączenie), więc usługi w zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1b85b-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="1b85b-134">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b85b-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="1b85b-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="1b85b-135">IMiddleware</span></span>

<span data-ttu-id="1b85b-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b85b-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="1b85b-137">Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) obsługuje żądania i zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą wykonywanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1b85b-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="1b85b-138">Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="1b85b-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="1b85b-139">Oprogramowanie pośredniczące aktywowane przez <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="1b85b-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="1b85b-140">Dla middlewares są tworzone rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="1b85b-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="1b85b-141">Nie jest możliwe przekazanie obiektów do oprogramowania pośredniczącego aktywowanego przez fabrykę przy użyciu <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="1b85b-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="1b85b-142">Oprogramowanie pośredniczące aktywowane fabrycznie jest dodawane do wbudowanego kontenera w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1b85b-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="1b85b-143">Oba middlewares są rejestrowane w potoku przetwarzania żądań w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1b85b-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="1b85b-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="1b85b-144">IMiddlewareFactory</span></span>

<span data-ttu-id="1b85b-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zapewnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1b85b-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="1b85b-146">Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa objęta zakresem.</span><span class="sxs-lookup"><span data-stu-id="1b85b-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="1b85b-147">Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , znajduje się w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="1b85b-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1b85b-148">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1b85b-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
