---
title: Aktywacja oprogramowania pośredniczącego oparta na fabryce w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać silnego typu oprogramowania pośredniczącego z implementacją aktywacji opartej na fabryce w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: c4835d0d9b0cc20a1176bbb5b0aedf3c5b7d51af
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587192"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Aktywacja oprogramowania pośredniczącego oparta na fabryce w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> jest punktem rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozszerzające sprawdzają, czy typ zarejestrowanego oprogramowania pośredniczącego jest zaimplementowany <xref:Microsoft.AspNetCore.Http.IMiddleware> . W <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> takim przypadku wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji. Oprogramowanie pośredniczące jest rejestrowane jako [Usługa w zakresie lub przejściowym](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.

Korzyści:

* Aktywacja na żądanie klienta (iniekcja usług objętych zakresem)
* Silne wpisywanie oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Http.IMiddleware> jest aktywowany dla żądania klienta (połączenie), więc usługi w zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) obsługuje żądania i zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą wykonywanie oprogramowania pośredniczącego.

Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Oprogramowanie pośredniczące aktywowane przez <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Dla middlewares są tworzone rozszerzenia:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nie jest możliwe przekazanie obiektów do oprogramowania pośredniczącego aktywowanego przez fabrykę przy użyciu <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Oprogramowanie pośredniczące aktywowane fabrycznie jest dodawane do wbudowanego kontenera w programie `Startup.ConfigureServices` :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middlewares są rejestrowane w potoku przetwarzania żądań w `Startup.Configure` :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zapewnia metody tworzenia oprogramowania pośredniczącego. Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa objęta zakresem.

Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , znajduje się w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> jest punktem rozszerzalności dla aktywacji [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozszerzające sprawdzają, czy typ zarejestrowanego oprogramowania pośredniczącego jest zaimplementowany <xref:Microsoft.AspNetCore.Http.IMiddleware> . W <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> takim przypadku wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji. Oprogramowanie pośredniczące jest rejestrowane jako [Usługa w zakresie lub przejściowym](xref:fundamentals/dependency-injection#service-lifetimes) w kontenerze usługi aplikacji.

Korzyści:

* Aktywacja na żądanie klienta (iniekcja usług objętych zakresem)
* Silne wpisywanie oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Http.IMiddleware> jest aktywowany dla żądania klienta (połączenie), więc usługi w zakresie mogą być wstrzykiwane do konstruktora oprogramowania pośredniczącego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> definiuje oprogramowanie pośredniczące dla potoku żądania aplikacji. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) obsługuje żądania i zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą wykonywanie oprogramowania pośredniczącego.

Oprogramowanie pośredniczące aktywowane zgodnie z konwencją:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Oprogramowanie pośredniczące aktywowane przez <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Dla middlewares są tworzone rozszerzenia:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nie jest możliwe przekazanie obiektów do oprogramowania pośredniczącego aktywowanego przez fabrykę przy użyciu <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Oprogramowanie pośredniczące aktywowane fabrycznie jest dodawane do wbudowanego kontenera w programie `Startup.ConfigureServices` :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middlewares są rejestrowane w potoku przetwarzania żądań w `Startup.Configure` :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zapewnia metody tworzenia oprogramowania pośredniczącego. Implementacja fabryki oprogramowania pośredniczącego jest zarejestrowana w kontenerze jako usługa objęta zakresem.

Domyślna <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementacja, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , znajduje się w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
