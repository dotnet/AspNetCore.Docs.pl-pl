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
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Fabryczna aktywacja oprogramowania pośredniczącego w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>jest punktem rozszerzalności aktywacji [oprogramowania pośredniczącego.](xref:fundamentals/middleware/index)

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzenia sprawdzić, czy zarejestrowany typ <xref:Microsoft.AspNetCore.Http.IMiddleware>oprogramowania pośredniczącego implementuje . Jeśli tak, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> wystąpienie zarejestrowane w kontenerze <xref:Microsoft.AspNetCore.Http.IMiddleware> jest używane do rozpoznawania implementacji zamiast przy użyciu logiki aktywacji oprogramowania pośredniczącego opartego na konwencji. Oprogramowanie pośredniczące jest rejestrowane jako usługa o [określonym zakresie lub przejściowe](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.

Korzyści:

* Aktywacja na żądanie klienta (iniekcja usług o określonym zakresie)
* Mocne pisanie oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Http.IMiddleware>jest aktywowany na żądanie klienta (połączenie), więc usługi o określonym zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware ( IMiddleware )

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji. [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) metoda obsługuje żądania <xref:System.Threading.Tasks.Task> i zwraca, który reprezentuje wykonanie oprogramowania pośredniczącego.

Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>aktywowane przez:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Rozszerzenia są tworzone dla oprogramowania pośredniczącego:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nie można przekazywać obiektów do fabrycznie aktywowane <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>oprogramowanie pośredniczące z:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Fabrycznie aktywowane oprogramowanie pośredniczące jest dodawane `Startup.ConfigureServices`do wbudowanego kontenera w:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba oprogramowanie pośredniczące są zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory (IMiddlewareFactory)

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego. Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa o określonym zakresie.

Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, znajduje się w pakiecie [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>jest punktem rozszerzalności aktywacji [oprogramowania pośredniczącego.](xref:fundamentals/middleware/index)

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzenia sprawdzić, czy zarejestrowany typ <xref:Microsoft.AspNetCore.Http.IMiddleware>oprogramowania pośredniczącego implementuje . Jeśli tak, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> wystąpienie zarejestrowane w kontenerze <xref:Microsoft.AspNetCore.Http.IMiddleware> jest używane do rozpoznawania implementacji zamiast przy użyciu logiki aktywacji oprogramowania pośredniczącego opartego na konwencji. Oprogramowanie pośredniczące jest rejestrowane jako usługa o [określonym zakresie lub przejściowe](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.

Korzyści:

* Aktywacja na żądanie klienta (iniekcja usług o określonym zakresie)
* Mocne pisanie oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Http.IMiddleware>jest aktywowany na żądanie klienta (połączenie), więc usługi o określonym zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware ( IMiddleware )

<xref:Microsoft.AspNetCore.Http.IMiddleware>definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji. [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) metoda obsługuje żądania <xref:System.Threading.Tasks.Task> i zwraca, który reprezentuje wykonanie oprogramowania pośredniczącego.

Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>aktywowane przez:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Rozszerzenia są tworzone dla oprogramowania pośredniczącego:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nie można przekazywać obiektów do fabrycznie aktywowane <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>oprogramowanie pośredniczące z:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Fabrycznie aktywowane oprogramowanie pośredniczące jest dodawane `Startup.ConfigureServices`do wbudowanego kontenera w:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba oprogramowanie pośredniczące są zarejestrowane `Startup.Configure`w potoku przetwarzania żądań w:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory (IMiddlewareFactory)

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>udostępnia metody tworzenia oprogramowania pośredniczącego. Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa o określonym zakresie.

Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, znajduje się w pakiecie [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
