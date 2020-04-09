---
title: Pisanie niestandardowego oprogramowania pośredniczącego ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak pisać niestandardowe oprogramowanie pośredniczące ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: e74bba9e1bd826d4f493b0ee642a198f984daada
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663927"
---
# <a name="write-custom-aspnet-core-middleware"></a>Pisanie niestandardowego oprogramowania pośredniczącego ASP.NET Core

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Steve [Smith](https://ardalis.com/)

Oprogramowanie pośredniczące to oprogramowanie, które jest montowane w potoku aplikacji do obsługi żądań i odpowiedzi. ASP.NET Core udostępnia bogaty zestaw wbudowanych składników oprogramowania pośredniczącego, ale w niektórych scenariuszach można napisać niestandardowe oprogramowanie pośredniczące.

## <a name="middleware-class"></a>Klasa oprogramowania pośredniczącego

Oprogramowanie pośredniczące jest zazwyczaj hermetyzowane w klasie i udostępniane za pomocą metody rozszerzenia. Należy wziąć pod uwagę następujące oprogramowanie pośredniczące, który ustawia kultury dla bieżącego żądania z ciągu zapytania:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Poprzedni przykładowy kod służy do wykazania tworzenia składnika oprogramowania pośredniczącego. Aby uzyskać ASP.NET wbudowaną obsługę lokalizacji Core, <xref:fundamentals/localization>zobacz .

Przetestuj oprogramowanie pośredniczące, przechodząc w kulturze. Na przykład `https://localhost:5001/?culture=no`poproś o plik .

Następujący kod przenosi delegata oprogramowania pośredniczącego do klasy:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Klasa oprogramowania pośredniczącego musi zawierać:

* Konstruktor publiczny z parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Metoda publiczna `Invoke` `InvokeAsync`o nazwie lub . Metoda ta musi:
  * Zwróć `Task`plik .
  * Zaakceptuj pierwszy parametr <xref:Microsoft.AspNetCore.Http.HttpContext>typu .
  
Dodatkowe parametry dla konstruktora i `Invoke` / `InvokeAsync` są wypełniane przez [iniekcję zależności (DI)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Zależności oprogramowania pośredniczącego

Oprogramowanie pośredniczące należy przestrzegać [zasady jawne zależności,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) ujawniając jego zależności w jego konstruktora. Oprogramowanie pośredniczące jest konstruowane raz na *okres istnienia aplikacji.* Zobacz [sekcję Zależności oprogramowania pośredniczącego na żądanie,](#per-request-middleware-dependencies) jeśli chcesz udostępnić usługi oprogramowaniu pośredniczącemu w żądaniu.

Składniki oprogramowania pośredniczącego można rozwiązać ich zależności od [iniekcji zależności (DI)](xref:fundamentals/dependency-injection) za pomocą parametrów konstruktora. [UseMiddleware&lt;&gt; T](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) może również bezpośrednio akceptować dodatkowe parametry.

## <a name="per-request-middleware-dependencies"></a>Zależności oprogramowania pośredniczącego na żądanie

Ponieważ oprogramowanie pośredniczące jest skonstruowane podczas uruchamiania aplikacji, nie na żądanie, usługi okresu istnienia o *określonym zakresie* używane przez konstruktory oprogramowania pośredniczącego nie są współużytkowane z innymi typami wstrzykniętymi zależnościami podczas każdego żądania. Jeśli musisz udostępnić usługę *o określonym zakresie* między oprogramowaniem pośredniczącym i innymi typami, dodaj te usługi do podpisu `Invoke` metody. Metoda `Invoke` może akceptować dodatkowe parametry, które są wypełniane przez DI:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a>Metoda rozszerzenia oprogramowania pośredniczącego

Następująca metoda rozszerzenia udostępnia oprogramowanie <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>pośredniczące poprzez:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Poniższy kod wywołuje oprogramowanie `Startup.Configure`pośredniczące z:

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
