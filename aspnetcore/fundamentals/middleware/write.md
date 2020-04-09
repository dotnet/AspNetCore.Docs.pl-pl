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
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="46ff6-103">Pisanie niestandardowego oprogramowania pośredniczącego ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46ff6-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="46ff6-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Steve [Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="46ff6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="46ff6-105">Oprogramowanie pośredniczące to oprogramowanie, które jest montowane w potoku aplikacji do obsługi żądań i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46ff6-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="46ff6-106">ASP.NET Core udostępnia bogaty zestaw wbudowanych składników oprogramowania pośredniczącego, ale w niektórych scenariuszach można napisać niestandardowe oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="46ff6-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="46ff6-107">Klasa oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="46ff6-107">Middleware class</span></span>

<span data-ttu-id="46ff6-108">Oprogramowanie pośredniczące jest zazwyczaj hermetyzowane w klasie i udostępniane za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="46ff6-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="46ff6-109">Należy wziąć pod uwagę następujące oprogramowanie pośredniczące, który ustawia kultury dla bieżącego żądania z ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="46ff6-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="46ff6-110">Poprzedni przykładowy kod służy do wykazania tworzenia składnika oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="46ff6-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="46ff6-111">Aby uzyskać ASP.NET wbudowaną obsługę lokalizacji Core, <xref:fundamentals/localization>zobacz .</span><span class="sxs-lookup"><span data-stu-id="46ff6-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="46ff6-112">Przetestuj oprogramowanie pośredniczące, przechodząc w kulturze.</span><span class="sxs-lookup"><span data-stu-id="46ff6-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="46ff6-113">Na przykład `https://localhost:5001/?culture=no`poproś o plik .</span><span class="sxs-lookup"><span data-stu-id="46ff6-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="46ff6-114">Następujący kod przenosi delegata oprogramowania pośredniczącego do klasy:</span><span class="sxs-lookup"><span data-stu-id="46ff6-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="46ff6-115">Klasa oprogramowania pośredniczącego musi zawierać:</span><span class="sxs-lookup"><span data-stu-id="46ff6-115">The middleware class must include:</span></span>

* <span data-ttu-id="46ff6-116">Konstruktor publiczny z parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="46ff6-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="46ff6-117">Metoda publiczna `Invoke` `InvokeAsync`o nazwie lub .</span><span class="sxs-lookup"><span data-stu-id="46ff6-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="46ff6-118">Metoda ta musi:</span><span class="sxs-lookup"><span data-stu-id="46ff6-118">This method must:</span></span>
  * <span data-ttu-id="46ff6-119">Zwróć `Task`plik .</span><span class="sxs-lookup"><span data-stu-id="46ff6-119">Return a `Task`.</span></span>
  * <span data-ttu-id="46ff6-120">Zaakceptuj pierwszy parametr <xref:Microsoft.AspNetCore.Http.HttpContext>typu .</span><span class="sxs-lookup"><span data-stu-id="46ff6-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="46ff6-121">Dodatkowe parametry dla konstruktora i `Invoke` / `InvokeAsync` są wypełniane przez [iniekcję zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="46ff6-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="46ff6-122">Zależności oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="46ff6-122">Middleware dependencies</span></span>

<span data-ttu-id="46ff6-123">Oprogramowanie pośredniczące należy przestrzegać [zasady jawne zależności,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) ujawniając jego zależności w jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="46ff6-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="46ff6-124">Oprogramowanie pośredniczące jest konstruowane raz na *okres istnienia aplikacji.*</span><span class="sxs-lookup"><span data-stu-id="46ff6-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="46ff6-125">Zobacz [sekcję Zależności oprogramowania pośredniczącego na żądanie,](#per-request-middleware-dependencies) jeśli chcesz udostępnić usługi oprogramowaniu pośredniczącemu w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="46ff6-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="46ff6-126">Składniki oprogramowania pośredniczącego można rozwiązać ich zależności od [iniekcji zależności (DI)](xref:fundamentals/dependency-injection) za pomocą parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="46ff6-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="46ff6-127">[UseMiddleware&lt;&gt; T](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) może również bezpośrednio akceptować dodatkowe parametry.</span><span class="sxs-lookup"><span data-stu-id="46ff6-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="46ff6-128">Zależności oprogramowania pośredniczącego na żądanie</span><span class="sxs-lookup"><span data-stu-id="46ff6-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="46ff6-129">Ponieważ oprogramowanie pośredniczące jest skonstruowane podczas uruchamiania aplikacji, nie na żądanie, usługi okresu istnienia o *określonym zakresie* używane przez konstruktory oprogramowania pośredniczącego nie są współużytkowane z innymi typami wstrzykniętymi zależnościami podczas każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="46ff6-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="46ff6-130">Jeśli musisz udostępnić usługę *o określonym zakresie* między oprogramowaniem pośredniczącym i innymi typami, dodaj te usługi do podpisu `Invoke` metody.</span><span class="sxs-lookup"><span data-stu-id="46ff6-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="46ff6-131">Metoda `Invoke` może akceptować dodatkowe parametry, które są wypełniane przez DI:</span><span class="sxs-lookup"><span data-stu-id="46ff6-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="middleware-extension-method"></a><span data-ttu-id="46ff6-132">Metoda rozszerzenia oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="46ff6-132">Middleware extension method</span></span>

<span data-ttu-id="46ff6-133">Następująca metoda rozszerzenia udostępnia oprogramowanie <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>pośredniczące poprzez:</span><span class="sxs-lookup"><span data-stu-id="46ff6-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="46ff6-134">Poniższy kod wywołuje oprogramowanie `Startup.Configure`pośredniczące z:</span><span class="sxs-lookup"><span data-stu-id="46ff6-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="46ff6-135">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="46ff6-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
