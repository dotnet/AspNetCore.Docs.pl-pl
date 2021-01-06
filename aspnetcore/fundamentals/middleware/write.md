---
title: Napisz niestandardowe oprogramowanie pośredniczące ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak napisać niestandardowe oprogramowanie pośredniczące ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057469"
---
# <a name="write-custom-aspnet-core-middleware"></a>Napisz niestandardowe oprogramowanie pośredniczące ASP.NET Core

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)

Oprogramowanie pośredniczące jest oprogramowaniem, które jest połączone z potokiem aplikacji w celu obsługi żądań i odpowiedzi. ASP.NET Core udostępnia bogaty zestaw wbudowanych komponentów oprogramowania pośredniczącego, ale w niektórych scenariuszach warto napisać niestandardowe oprogramowanie pośredniczące.

> [!NOTE]
> W tym temacie opisano sposób pisania oprogramowania pośredniczącego *opartego na konwencjach* . W przypadku podejścia korzystającego z silnego wpisywania i aktywacji na żądanie, zobacz <xref:fundamentals/middleware/extensibility> .

## <a name="middleware-class"></a>Pośrednicząca Klasa oprogramowania

Oprogramowanie pośredniczące jest zazwyczaj hermetyzowane w klasie i udostępniane przy użyciu metody rozszerzenia. Rozważ następujące oprogramowanie pośredniczące, które ustawia kulturę dla bieżącego żądania z ciągu zapytania:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Poprzedni przykładowy kod służy do zademonstrowania tworzenia składnika pośredniczącego. Aby uzyskać wbudowaną obsługę lokalizacji ASP.NET Core, zobacz <xref:fundamentals/localization> .

Przetestuj oprogramowanie pośredniczące, przekazując kulturę. Na przykład żądanie `https://localhost:5001/?culture=no` .

Poniższy kod przenosi delegata oprogramowania pośredniczącego do klasy:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Klasa pośrednicząca musi zawierać następujące:

* Konstruktor publiczny z parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate> .
* Metoda publiczna o nazwie `Invoke` lub `InvokeAsync` . Ta metoda musi:
  * Zwróć `Task` .
  * Zaakceptuj pierwszy parametr typu <xref:Microsoft.AspNetCore.Http.HttpContext> .
  
Dodatkowe parametry dla konstruktora i `Invoke` / `InvokeAsync` są wypełniane przez [iniekcję zależności (di)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Zależności oprogramowania pośredniczącego

Oprogramowanie pośredniczące powinno postępować zgodnie z [zasadą jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) przez ujawnienie jej zależności w konstruktorze. Oprogramowanie pośredniczące jest zbudowane raz dla *okresu istnienia aplikacji*. Zapoznaj się z sekcją [zależności oprogramowania pośredniczącego na żądanie](#per-request-middleware-dependencies) , jeśli chcesz udostępnić usługi za pomocą oprogramowania pośredniczącego w ramach żądania.

Składniki pośredniczące mogą rozpoznać zależności od [iniekcji zależności (di)](xref:fundamentals/dependency-injection) przez parametry konstruktora. [UseMiddleware &lt; T &gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) mogą również akceptować dodatkowe parametry bezpośrednio.

## <a name="per-request-middleware-dependencies"></a>Zależności oprogramowania pośredniczącego na żądanie

Ponieważ oprogramowanie pośredniczące jest zbudowane przy uruchamianiu aplikacji, a nie na żądanie, usługi okresu istnienia w *zasięgu* , które są używane przez konstruktory oprogramowania pośredniczącego, nie są udostępniane innym typem z wstrzykiwanymi zależnościami podczas każdego żądania. Jeśli musisz udostępnić usługę z *zakresem* w ramach oprogramowania pośredniczącego i innych typów, Dodaj te usługi do `Invoke` sygnatury metody. `Invoke`Metoda może akceptować dodatkowe parametry, które są wypełniane przez di:

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

[Opcje okresu istnienia i rejestracji](xref:fundamentals/dependency-injection#lifetime-and-registration-options) zawierają pełny przykład oprogramowania pośredniczącego z usługami o okresie istnienia w *zakresie* .

## <a name="middleware-extension-method"></a>Metoda rozszerzenia oprogramowania pośredniczącego

Następujące metody rozszerzające udostępniają oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Poniższy kod wywołuje oprogramowanie pośredniczące z `Startup.Configure` :

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Opcje okresu istnienia i rejestracji](xref:fundamentals/dependency-injection#lifetime-and-registration-options) zawierają pełny przykład oprogramowania pośredniczącego z usługami *objętymi zakresem*, *przejściowymi* i *pojedynczymi* okresami istnienia.
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
