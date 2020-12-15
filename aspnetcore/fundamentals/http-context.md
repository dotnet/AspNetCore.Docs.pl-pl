---
title: Dostęp do obiektu HttpContext w ASP.NET Core
author: coderandhiker
description: Dowiedz się, jak uzyskać dostęp do obiektu HttpContext w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: d5ada72bb03cd74cf77c31450e5eafb9c2841049
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506997"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="d2204-103">Dostęp do obiektu HttpContext w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2204-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="d2204-104">Dostęp do aplikacji ASP.NET Core `HttpContext` za pomocą <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interfejsu i jego domyślnej implementacji <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> .</span><span class="sxs-lookup"><span data-stu-id="d2204-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="d2204-105">Jest to konieczne tylko wtedy, `IHttpContextAccessor` gdy potrzebujesz dostępu do `HttpContext` wnętrza usługi.</span><span class="sxs-lookup"><span data-stu-id="d2204-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="d2204-106">Użyj kontekstu HttpContext ze Razor stron</span><span class="sxs-lookup"><span data-stu-id="d2204-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="d2204-107">RazorStrony <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> uwidaczniają <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> Właściwość:</span><span class="sxs-lookup"><span data-stu-id="d2204-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="d2204-108">Używanie elementu HttpContext z Razor widoku</span><span class="sxs-lookup"><span data-stu-id="d2204-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="d2204-109">Razorwidoki uwidaczniają `HttpContext` bezpośrednio przez właściwość [ Razor Page. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) w widoku.</span><span class="sxs-lookup"><span data-stu-id="d2204-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="d2204-110">Poniższy przykład pobiera bieżącą nazwę użytkownika w aplikacji intranetowej przy użyciu uwierzytelniania systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="d2204-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="d2204-111">Używanie elementu HttpContext z poziomu kontrolera</span><span class="sxs-lookup"><span data-stu-id="d2204-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="d2204-112">Kontrolery uwidaczniają Właściwość [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :</span><span class="sxs-lookup"><span data-stu-id="d2204-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="d2204-113">Używanie obiektu HttpContext w oprogramowaniu pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="d2204-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="d2204-114">Podczas pracy z niestandardowymi składnikami oprogramowania pośredniczącego `HttpContext` jest przenoszona do `Invoke` `InvokeAsync` metody lub i można uzyskać do niej dostęp po skonfigurowaniu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d2204-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="d2204-115">Używanie obiektu HttpContext ze składników niestandardowych</span><span class="sxs-lookup"><span data-stu-id="d2204-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="d2204-116">W przypadku innych platform i składników niestandardowych, które wymagają dostępu do `HttpContext` , zalecane podejście polega na zarejestrowaniu zależności przy użyciu wbudowanego kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="d2204-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d2204-117">Kontener iniekcji zależności dostarcza `IHttpContextAccessor` klasy do wszystkich klas, które deklarują ją jako zależność w konstruktorach:</span><span class="sxs-lookup"><span data-stu-id="d2204-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="d2204-118">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d2204-118">In the following example:</span></span>

* <span data-ttu-id="d2204-119">`UserRepository` deklaruje swoją zależność od `IHttpContextAccessor` .</span><span class="sxs-lookup"><span data-stu-id="d2204-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="d2204-120">Zależność jest podawana, gdy iniekcja zależności rozpoznaje łańcuch zależności i tworzy wystąpienie `UserRepository` .</span><span class="sxs-lookup"><span data-stu-id="d2204-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="d2204-121">Dostęp do obiektu HttpContext z wątku w tle</span><span class="sxs-lookup"><span data-stu-id="d2204-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="d2204-122">`HttpContext` nie jest bezpieczny wątkowo.</span><span class="sxs-lookup"><span data-stu-id="d2204-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="d2204-123">Odczyt lub zapis właściwości `HttpContext` poza przetwarzaniem żądania może skutkować <xref:System.NullReferenceException> .</span><span class="sxs-lookup"><span data-stu-id="d2204-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="d2204-124">Jeśli aplikacja generuje sporadyczne `NullReferenceException` błędy, zapoznaj się z częścią kodu, który rozpoczyna przetwarzanie w tle, lub Kontynuuj przetwarzanie po zakończeniu żądania.</span><span class="sxs-lookup"><span data-stu-id="d2204-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="d2204-125">Poszukaj błędów, takich jak Definiowanie metody kontrolera jako `async void` .</span><span class="sxs-lookup"><span data-stu-id="d2204-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="d2204-126">Aby bezpiecznie wykonać prace w tle z `HttpContext` danymi:</span><span class="sxs-lookup"><span data-stu-id="d2204-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="d2204-127">Skopiuj wymagane dane podczas przetwarzania żądania.</span><span class="sxs-lookup"><span data-stu-id="d2204-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="d2204-128">Przekaż skopiowane dane do zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="d2204-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="d2204-129">Aby uniknąć niebezpiecznego kodu, nigdy nie przekazuj `HttpContext` do metody, która wykonuje działania w tle.</span><span class="sxs-lookup"><span data-stu-id="d2204-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="d2204-130">Zamiast tego Przekaż wymagane dane.</span><span class="sxs-lookup"><span data-stu-id="d2204-130">Pass the required data instead.</span></span> <span data-ttu-id="d2204-131">W poniższym przykładzie `SendEmailCore` jest wywoływana w celu rozpoczęcia wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="d2204-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="d2204-132">`correlationId`Jest przenoszona do `SendEmailCore` , nie jest `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d2204-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="d2204-133">Wykonanie kodu nie oczekuje na `SendEmailCore` zakończenie:</span><span class="sxs-lookup"><span data-stu-id="d2204-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="d2204-134">Blazor i udostępniony stan</span><span class="sxs-lookup"><span data-stu-id="d2204-134">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
