---
title: Aktywacja oprogramowania pośredniczącego oparta na fabryce w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnego typu oprogramowania pośredniczącego z implementacją aktywacji opartej na fabryce w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 108d7d343a08bff8b5665df7b149f7f952220459
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774460"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Aktywacja oprogramowania pośredniczącego oparta na fabryce w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>jest punktem rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzające sprawdzają, czy typ zarejestrowanego <xref:Microsoft.AspNetCore.Http.IMiddleware>oprogramowania pośredniczącego jest zaimplementowany. W takim przypadku <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji. Oprogramowanie pośredniczące jest rejestrowane jako [Usługa w zakresie lub przejściowym](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.

Korzyści:

* Aktywacja na żądanie klienta (iniekcja usług objętych zakresem)
* Silne wpisywanie oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Http.IMiddleware>jest aktywowany dla żądania klienta (połączenie), więc usługi w zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) obsługuje żądania i zwraca <xref:System.Threading.Tasks.Task> wartość reprezentującą wykonywanie oprogramowania pośredniczącego.

Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Oprogramowanie pośredniczące aktywowane <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>przez:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Dla middlewares są tworzone rozszerzenia:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nie jest możliwe przekazanie obiektów do oprogramowania pośredniczącego aktywowanego przez fabrykę przy użyciu <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Oprogramowanie pośredniczące aktywowane fabrycznie jest dodawane do wbudowanego kontenera w programie `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middlewares są rejestrowane w potoku przetwarzania żądań w `Startup.Configure`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>zapewnia metody tworzenia oprogramowania pośredniczącego. Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa objęta zakresem.

Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, znajduje się w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>jest punktem rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzające sprawdzają, czy typ zarejestrowanego <xref:Microsoft.AspNetCore.Http.IMiddleware>oprogramowania pośredniczącego jest zaimplementowany. W takim przypadku <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji. Oprogramowanie pośredniczące jest rejestrowane jako [Usługa w zakresie lub przejściowym](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.

Korzyści:

* Aktywacja na żądanie klienta (iniekcja usług objętych zakresem)
* Silne wpisywanie oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Http.IMiddleware>jest aktywowany dla żądania klienta (połączenie), więc usługi w zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) obsługuje żądania i zwraca <xref:System.Threading.Tasks.Task> wartość reprezentującą wykonywanie oprogramowania pośredniczącego.

Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Oprogramowanie pośredniczące aktywowane <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>przez:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Dla middlewares są tworzone rozszerzenia:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nie jest możliwe przekazanie obiektów do oprogramowania pośredniczącego aktywowanego przez fabrykę przy użyciu <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Oprogramowanie pośredniczące aktywowane fabrycznie jest dodawane do wbudowanego kontenera w programie `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middlewares są rejestrowane w potoku przetwarzania żądań w `Startup.Configure`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>zapewnia metody tworzenia oprogramowania pośredniczącego. Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa objęta zakresem.

Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, znajduje się w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
