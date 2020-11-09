---
title: Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wstrzyknąć programy obsługi wymagań autoryzacji do aplikacji ASP.NET Core przy użyciu iniekcji zależności.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060264"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="d492b-103">Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d492b-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="d492b-104">[Procedury obsługi autoryzacji muszą być zarejestrowane](xref:security/authorization/policies#handler-registration) w kolekcji usług podczas konfiguracji przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d492b-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d492b-105">Załóżmy, że masz repozytorium reguł, które chcesz oszacować wewnątrz procedury obsługi autoryzacji i że repozytorium zostało zarejestrowane w kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="d492b-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="d492b-106">Autoryzacja rozwiązuje i wprowadza do konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d492b-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="d492b-107">Na przykład, aby użyć ASP. Infrastruktura rejestrowania w sieci, wstrzyknąć `ILoggerFactory` do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="d492b-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="d492b-108">Taka procedura obsługi może wyglądać podobnie do następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="d492b-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="d492b-109">Poprzednią procedurę obsługi można zarejestrować w każdym [okresie istnienia usługi](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d492b-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d492b-110">Następujący kod używa `AddSingleton` do rejestrowania poprzedniej procedury obsługi:</span><span class="sxs-lookup"><span data-stu-id="d492b-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="d492b-111">Wystąpienie procedury obsługi jest tworzone podczas uruchamiania aplikacji i służy do dodawania iniekcji zarejestrowanej `ILoggerFactory` w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="d492b-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="d492b-112">Programy obsługi, które używają Entity Framework nie powinny być rejestrowane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="d492b-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
