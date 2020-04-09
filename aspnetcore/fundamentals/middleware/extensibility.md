---
title: Fabryczna aktywacja oprogramowania pośredniczącego w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnie typizowanego oprogramowania pośredniczącego z implementacją aktywacji fabrycznej w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: abc6268584d12fe43d972c79a99316b94e8bee4b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663094"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="96a72-103">Fabryczna aktywacja oprogramowania pośredniczącego w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96a72-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="96a72-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>jest punktem rozszerzalności aktywacji [oprogramowania pośredniczącego.](xref:fundamentals/middleware/index)</span><span class="sxs-lookup"><span data-stu-id="96a72-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="96a72-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzenia sprawdzić, czy zarejestrowany typ <xref:Microsoft.AspNetCore.Http.IMiddleware>oprogramowania pośredniczącego implementuje .</span><span class="sxs-lookup"><span data-stu-id="96a72-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="96a72-106">Jeśli tak, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> wystąpienie zarejestrowane w kontenerze <xref:Microsoft.AspNetCore.Http.IMiddleware> jest używane do rozpoznawania implementacji zamiast przy użyciu logiki aktywacji oprogramowania pośredniczącego opartego na konwencji.</span><span class="sxs-lookup"><span data-stu-id="96a72-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="96a72-107">Oprogramowanie pośredniczące jest rejestrowane jako usługa o [określonym zakresie lub przejściowe](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96a72-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="96a72-108">Korzyści:</span><span class="sxs-lookup"><span data-stu-id="96a72-108">Benefits:</span></span>

* <span data-ttu-id="96a72-109">Aktywacja na żądanie klienta (iniekcja usług o określonym zakresie)</span><span class="sxs-lookup"><span data-stu-id="96a72-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="96a72-110">Mocne pisanie oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="96a72-110">Strong typing of middleware</span></span>

<span data-ttu-id="96a72-111"><xref:Microsoft.AspNetCore.Http.IMiddleware>jest aktywowany na żądanie klienta (połączenie), więc usługi o określonym zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="96a72-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="96a72-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="96a72-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="96a72-113">IMiddleware ( IMiddleware )</span><span class="sxs-lookup"><span data-stu-id="96a72-113">IMiddleware</span></span>

<span data-ttu-id="96a72-114"><xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96a72-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="96a72-115">[InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) metoda obsługuje żądania <xref:System.Threading.Tasks.Task> i zwraca, który reprezentuje wykonanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="96a72-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="96a72-116">Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="96a72-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="96a72-117">Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>aktywowane przez:</span><span class="sxs-lookup"><span data-stu-id="96a72-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="96a72-118">Rozszerzenia są tworzone dla oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="96a72-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="96a72-119">Nie można przekazywać obiektów do fabrycznie aktywowane <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>oprogramowanie pośredniczące z:</span><span class="sxs-lookup"><span data-stu-id="96a72-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="96a72-120">Fabrycznie aktywowane oprogramowanie pośredniczące jest dodawane `Startup.ConfigureServices`do wbudowanego kontenera w:</span><span class="sxs-lookup"><span data-stu-id="96a72-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="96a72-121">Oba oprogramowanie pośredniczące są zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:</span><span class="sxs-lookup"><span data-stu-id="96a72-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="96a72-122">IMiddlewareFactory (IMiddlewareFactory)</span><span class="sxs-lookup"><span data-stu-id="96a72-122">IMiddlewareFactory</span></span>

<span data-ttu-id="96a72-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="96a72-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="96a72-124">Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="96a72-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="96a72-125">Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, znajduje się w pakiecie [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)</span><span class="sxs-lookup"><span data-stu-id="96a72-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="96a72-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>jest punktem rozszerzalności aktywacji [oprogramowania pośredniczącego.](xref:fundamentals/middleware/index)</span><span class="sxs-lookup"><span data-stu-id="96a72-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="96a72-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzenia sprawdzić, czy zarejestrowany typ <xref:Microsoft.AspNetCore.Http.IMiddleware>oprogramowania pośredniczącego implementuje .</span><span class="sxs-lookup"><span data-stu-id="96a72-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="96a72-128">Jeśli tak, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> wystąpienie zarejestrowane w kontenerze <xref:Microsoft.AspNetCore.Http.IMiddleware> jest używane do rozpoznawania implementacji zamiast przy użyciu logiki aktywacji oprogramowania pośredniczącego opartego na konwencji.</span><span class="sxs-lookup"><span data-stu-id="96a72-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="96a72-129">Oprogramowanie pośredniczące jest rejestrowane jako usługa o [określonym zakresie lub przejściowe](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96a72-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="96a72-130">Korzyści:</span><span class="sxs-lookup"><span data-stu-id="96a72-130">Benefits:</span></span>

* <span data-ttu-id="96a72-131">Aktywacja na żądanie klienta (iniekcja usług o określonym zakresie)</span><span class="sxs-lookup"><span data-stu-id="96a72-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="96a72-132">Mocne pisanie oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="96a72-132">Strong typing of middleware</span></span>

<span data-ttu-id="96a72-133"><xref:Microsoft.AspNetCore.Http.IMiddleware>jest aktywowany na żądanie klienta (połączenie), więc usługi o określonym zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="96a72-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="96a72-134">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="96a72-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="96a72-135">IMiddleware ( IMiddleware )</span><span class="sxs-lookup"><span data-stu-id="96a72-135">IMiddleware</span></span>

<span data-ttu-id="96a72-136"><xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96a72-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="96a72-137">[InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) metoda obsługuje żądania <xref:System.Threading.Tasks.Task> i zwraca, który reprezentuje wykonanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="96a72-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="96a72-138">Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:</span><span class="sxs-lookup"><span data-stu-id="96a72-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="96a72-139">Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>aktywowane przez:</span><span class="sxs-lookup"><span data-stu-id="96a72-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="96a72-140">Rozszerzenia są tworzone dla oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="96a72-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="96a72-141">Nie można przekazywać obiektów do fabrycznie aktywowane <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>oprogramowanie pośredniczące z:</span><span class="sxs-lookup"><span data-stu-id="96a72-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="96a72-142">Fabrycznie aktywowane oprogramowanie pośredniczące jest dodawane `Startup.ConfigureServices`do wbudowanego kontenera w:</span><span class="sxs-lookup"><span data-stu-id="96a72-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="96a72-143">Oba oprogramowanie pośredniczące są zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:</span><span class="sxs-lookup"><span data-stu-id="96a72-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="96a72-144">IMiddlewareFactory (IMiddlewareFactory)</span><span class="sxs-lookup"><span data-stu-id="96a72-144">IMiddlewareFactory</span></span>

<span data-ttu-id="96a72-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="96a72-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="96a72-146">Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="96a72-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="96a72-147">Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, znajduje się w pakiecie [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)</span><span class="sxs-lookup"><span data-stu-id="96a72-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="96a72-148">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="96a72-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
