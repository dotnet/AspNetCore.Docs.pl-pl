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
# <a name="access-httpcontext-in-aspnet-core"></a>Dostęp do protokołu HttpContext w ASP.NET Core

ASP.NET dostęp `HttpContext` do aplikacji Core <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> za pośrednictwem <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>interfejsu i jego domyślnej implementacji . Jest to konieczne tylko `IHttpContextAccessor` wtedy, gdy `HttpContext` potrzebujesz dostępu do wewnątrz usługi.

## <a name="use-httpcontext-from-razor-pages"></a>Użyj protokołu HttpContext ze stron Razor

Strony <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor udostępnia <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> właściwość:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Używanie protokołu HttpContext z widoku Razor

Widoki razor `HttpContext` uwidaczniają bezpośrednio za pośrednictwem [właściwości RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) w widoku. Poniższy przykład pobiera bieżącą nazwę użytkownika w aplikacji intranetowej przy użyciu uwierzytelniania systemu Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Używanie protokołu HttpContext z kontrolera

Kontrolery uwidaczniają [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) właściwości:

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

## <a name="use-httpcontext-from-middleware"></a>Użyj protokołu HttpContext z oprogramowania pośredniczącego

Podczas pracy z niestandardowymi `HttpContext` składnikami `Invoke` oprogramowania pośredniczącego, jest przekazywana do metody lub `InvokeAsync` i jest dostępna, gdy oprogramowanie pośredniczące jest skonfigurowane:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Używanie protokołu HttpContext ze składników niestandardowych

Dla innych składników framework i `HttpContext`niestandardowych, które wymagają dostępu do , zalecane podejście jest zarejestrowanie zależności przy użyciu wbudowanego [kontenera iniekcji zależności.](xref:fundamentals/dependency-injection) Kontener iniekcji zależności `IHttpContextAccessor` dostarcza do wszystkich klas, które deklarują go jako zależność w ich konstruktorów:

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

* `UserRepository`deklaruje swoją zależność `IHttpContextAccessor`od .
* Zależność jest dostarczana, gdy iniekcja zależności rozwiązuje `UserRepository`łańcuch zależności i tworzy wystąpienie .

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

## <a name="httpcontext-access-from-a-background-thread"></a>Dostęp httpcontext z wątku tła

`HttpContext`nie jest bezpieczny dla wątków. Odczytywanie lub zapisywanie właściwości `HttpContext` z zewnątrz przetwarzania <xref:System.NullReferenceException>żądania może spowodować .

> [!NOTE]
> Jeśli aplikacja generuje sporadyczne `NullReferenceException` błędy, przejrzyj części kodu, które rozpoczynają przetwarzanie w tle lub które kontynuują przetwarzanie po zakończeniu żądania. Poszukaj błędów, takich jak `async void`definiowanie metody kontrolera jako .

Aby bezpiecznie wykonywać `HttpContext` pracę w tle z danymi:

* Skopiuj wymagane dane podczas przetwarzania żądania.
* Przekaż skopiowane dane do zadania działającego w tle.

Aby uniknąć niebezpiecznego `HttpContext` kodu, nigdy nie należy przekazywać do metody, która wykonuje pracę w tle. Zamiast tego przekaż wymagane dane. W poniższym `SendEmailCore` przykładzie jest wywoływana, aby rozpocząć wysyłanie wiadomości e-mail. Jest `correlationId` przekazywana `SendEmailCore`do `HttpContext`, a nie do . Wykonanie kodu nie czeka `SendEmailCore` na zakończenie:

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
