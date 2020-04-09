---
title: Dostęp do protokołu HttpContext w ASP.NET Core
author: coderandhiker
description: Dowiedz się, jak uzyskać dostęp do protokołu HttpContext w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658747"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="ef2b3-103">Dostęp do protokołu HttpContext w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef2b3-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="ef2b3-104">ASP.NET dostęp `HttpContext` do aplikacji Core <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> za pośrednictwem <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>interfejsu i jego domyślnej implementacji .</span><span class="sxs-lookup"><span data-stu-id="ef2b3-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="ef2b3-105">Jest to konieczne tylko `IHttpContextAccessor` wtedy, gdy `HttpContext` potrzebujesz dostępu do wewnątrz usługi.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="ef2b3-106">Użyj protokołu HttpContext ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="ef2b3-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="ef2b3-107">Strony <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor udostępnia <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> właściwość:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="ef2b3-108">Używanie protokołu HttpContext z widoku Razor</span><span class="sxs-lookup"><span data-stu-id="ef2b3-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="ef2b3-109">Widoki razor `HttpContext` uwidaczniają bezpośrednio za pośrednictwem [właściwości RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) w widoku.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="ef2b3-110">Poniższy przykład pobiera bieżącą nazwę użytkownika w aplikacji intranetowej przy użyciu uwierzytelniania systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="ef2b3-111">Używanie protokołu HttpContext z kontrolera</span><span class="sxs-lookup"><span data-stu-id="ef2b3-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="ef2b3-112">Kontrolery uwidaczniają [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) właściwości:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="ef2b3-113">Użyj protokołu HttpContext z oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="ef2b3-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="ef2b3-114">Podczas pracy z niestandardowymi `HttpContext` składnikami `Invoke` oprogramowania pośredniczącego, jest przekazywana do metody lub `InvokeAsync` i jest dostępna, gdy oprogramowanie pośredniczące jest skonfigurowane:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="ef2b3-115">Używanie protokołu HttpContext ze składników niestandardowych</span><span class="sxs-lookup"><span data-stu-id="ef2b3-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="ef2b3-116">Dla innych składników framework i `HttpContext`niestandardowych, które wymagają dostępu do , zalecane podejście jest zarejestrowanie zależności przy użyciu wbudowanego [kontenera iniekcji zależności.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="ef2b3-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ef2b3-117">Kontener iniekcji zależności `IHttpContextAccessor` dostarcza do wszystkich klas, które deklarują go jako zależność w ich konstruktorów:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="ef2b3-118">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-118">In the following example:</span></span>

* <span data-ttu-id="ef2b3-119">`UserRepository`deklaruje swoją zależność `IHttpContextAccessor`od .</span><span class="sxs-lookup"><span data-stu-id="ef2b3-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="ef2b3-120">Zależność jest dostarczana, gdy iniekcja zależności rozwiązuje `UserRepository`łańcuch zależności i tworzy wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="ef2b3-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="ef2b3-121">Dostęp httpcontext z wątku tła</span><span class="sxs-lookup"><span data-stu-id="ef2b3-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="ef2b3-122">`HttpContext`nie jest bezpieczny dla wątków.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="ef2b3-123">Odczytywanie lub zapisywanie właściwości `HttpContext` z zewnątrz przetwarzania <xref:System.NullReferenceException>żądania może spowodować .</span><span class="sxs-lookup"><span data-stu-id="ef2b3-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="ef2b3-124">Jeśli aplikacja generuje sporadyczne `NullReferenceException` błędy, przejrzyj części kodu, które rozpoczynają przetwarzanie w tle lub które kontynuują przetwarzanie po zakończeniu żądania.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="ef2b3-125">Poszukaj błędów, takich jak `async void`definiowanie metody kontrolera jako .</span><span class="sxs-lookup"><span data-stu-id="ef2b3-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="ef2b3-126">Aby bezpiecznie wykonywać `HttpContext` pracę w tle z danymi:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="ef2b3-127">Skopiuj wymagane dane podczas przetwarzania żądania.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="ef2b3-128">Przekaż skopiowane dane do zadania działającego w tle.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="ef2b3-129">Aby uniknąć niebezpiecznego `HttpContext` kodu, nigdy nie należy przekazywać do metody, która wykonuje pracę w tle.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="ef2b3-130">Zamiast tego przekaż wymagane dane.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-130">Pass the required data instead.</span></span> <span data-ttu-id="ef2b3-131">W poniższym `SendEmailCore` przykładzie jest wywoływana, aby rozpocząć wysyłanie wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="ef2b3-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="ef2b3-132">Jest `correlationId` przekazywana `SendEmailCore`do `HttpContext`, a nie do .</span><span class="sxs-lookup"><span data-stu-id="ef2b3-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="ef2b3-133">Wykonanie kodu nie czeka `SendEmailCore` na zakończenie:</span><span class="sxs-lookup"><span data-stu-id="ef2b3-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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
