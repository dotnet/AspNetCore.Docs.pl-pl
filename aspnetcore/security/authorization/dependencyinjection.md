---
title: Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wstrzyknąć programy obsługi wymagań autoryzacji do aplikacji ASP.NET Core przy użyciu iniekcji zależności.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 16285f6f731455d6e45a04f82437793891a77668
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775123"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="5da18-103">Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5da18-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="5da18-104">[Procedury obsługi autoryzacji muszą być zarejestrowane](xref:security/authorization/policies#handler-registration) w kolekcji usług podczas konfiguracji (przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="5da18-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="5da18-105">Załóżmy, że masz repozytorium reguł, które chcesz oszacować wewnątrz procedury obsługi autoryzacji i że repozytorium zostało zarejestrowane w kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="5da18-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="5da18-106">Autoryzacja zostanie rozwiązany i wstrzyknąć do konstruktora.</span><span class="sxs-lookup"><span data-stu-id="5da18-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="5da18-107">Na przykład jeśli chciałeś użyć ASP. Infrastruktura rejestrowania w sieci, którą chcesz wstrzyknąć `ILoggerFactory` do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="5da18-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="5da18-108">Taka procedura obsługi może wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="5da18-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="5da18-109">Procedurę obsługi należy zarejestrować w programie `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="5da18-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="5da18-110">Wystąpienie programu obsługi zostanie utworzone, gdy aplikacja zostanie uruchomiona, a funkcja DI sprawdzi zarejestrowane `ILoggerFactory` w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="5da18-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="5da18-111">Programy obsługi, które używają Entity Framework nie powinny być rejestrowane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="5da18-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
