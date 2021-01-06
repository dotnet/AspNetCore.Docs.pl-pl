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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506997"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Dostęp do obiektu HttpContext w ASP.NET Core

Dostęp do aplikacji ASP.NET Core `HttpContext` za pomocą <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interfejsu i jego domyślnej implementacji <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> . Jest to konieczne tylko wtedy, `IHttpContextAccessor` gdy potrzebujesz dostępu do `HttpContext` wnętrza usługi.

## <a name="use-httpcontext-from-no-locrazor-pages"></a>Użyj kontekstu HttpContext ze Razor stron

RazorStrony <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> uwidaczniają <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> Właściwość:

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

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>Używanie elementu HttpContext z Razor widoku

Razorwidoki uwidaczniają `HttpContext` bezpośrednio przez właściwość [ Razor Page. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) w widoku. Poniższy przykład pobiera bieżącą nazwę użytkownika w aplikacji intranetowej przy użyciu uwierzytelniania systemu Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Używanie elementu HttpContext z poziomu kontrolera

Kontrolery uwidaczniają Właściwość [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :

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

## <a name="use-httpcontext-from-middleware"></a>Używanie obiektu HttpContext w oprogramowaniu pośredniczącym

Podczas pracy z niestandardowymi składnikami oprogramowania pośredniczącego `HttpContext` jest przenoszona do `Invoke` `InvokeAsync` metody lub i można uzyskać do niej dostęp po skonfigurowaniu oprogramowania pośredniczącego:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Używanie obiektu HttpContext ze składników niestandardowych

W przypadku innych platform i składników niestandardowych, które wymagają dostępu do `HttpContext` , zalecane podejście polega na zarejestrowaniu zależności przy użyciu wbudowanego kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) . Kontener iniekcji zależności dostarcza `IHttpContextAccessor` klasy do wszystkich klas, które deklarują ją jako zależność w konstruktorach:

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

W poniższym przykładzie:

* `UserRepository` deklaruje swoją zależność od `IHttpContextAccessor` .
* Zależność jest podawana, gdy iniekcja zależności rozpoznaje łańcuch zależności i tworzy wystąpienie `UserRepository` .

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

## <a name="httpcontext-access-from-a-background-thread"></a>Dostęp do obiektu HttpContext z wątku w tle

`HttpContext` nie jest bezpieczny wątkowo. Odczyt lub zapis właściwości `HttpContext` poza przetwarzaniem żądania może skutkować <xref:System.NullReferenceException> .

> [!NOTE]
> Jeśli aplikacja generuje sporadyczne `NullReferenceException` błędy, zapoznaj się z częścią kodu, który rozpoczyna przetwarzanie w tle, lub Kontynuuj przetwarzanie po zakończeniu żądania. Poszukaj błędów, takich jak Definiowanie metody kontrolera jako `async void` .

Aby bezpiecznie wykonać prace w tle z `HttpContext` danymi:

* Skopiuj wymagane dane podczas przetwarzania żądania.
* Przekaż skopiowane dane do zadania w tle.

Aby uniknąć niebezpiecznego kodu, nigdy nie przekazuj `HttpContext` do metody, która wykonuje działania w tle. Zamiast tego Przekaż wymagane dane. W poniższym przykładzie `SendEmailCore` jest wywoływana w celu rozpoczęcia wysyłania wiadomości e-mail. `correlationId`Jest przenoszona do `SendEmailCore` , nie jest `HttpContext` . Wykonanie kodu nie oczekuje na `SendEmailCore` zakończenie:

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

## <a name="no-locblazor-and-shared-state"></a>Blazor i udostępniony stan

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
