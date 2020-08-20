---
title: Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wstrzyknąć programy obsługi wymagań autoryzacji do aplikacji ASP.NET Core przy użyciu iniekcji zależności.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 4bc7eb38262c8a94a84aacc978737a778bfd71a1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632567"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core

<a name="security-authorization-di"></a>

[Procedury obsługi autoryzacji muszą być zarejestrowane](xref:security/authorization/policies#handler-registration) w kolekcji usług podczas konfiguracji (przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection)).

Załóżmy, że masz repozytorium reguł, które chcesz oszacować wewnątrz procedury obsługi autoryzacji i że repozytorium zostało zarejestrowane w kolekcji usług. Autoryzacja zostanie rozwiązany i wstrzyknąć do konstruktora.

Na przykład jeśli chciałeś użyć ASP. Infrastruktura rejestrowania w sieci, którą chcesz wstrzyknąć `ILoggerFactory` do procedury obsługi. Taka procedura obsługi może wyglądać następująco:

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

Procedurę obsługi należy zarejestrować w programie `services.AddSingleton()` :

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Wystąpienie programu obsługi zostanie utworzone, gdy aplikacja zostanie uruchomiona, a funkcja DI sprawdzi zarejestrowane `ILoggerFactory` w konstruktorze.

> [!NOTE]
> Programy obsługi, które używają Entity Framework nie powinny być rejestrowane jako pojedyncze.
