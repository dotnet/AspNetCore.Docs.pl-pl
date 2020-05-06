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

Procedurę obsługi należy zarejestrować w programie `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Wystąpienie programu obsługi zostanie utworzone, gdy aplikacja zostanie uruchomiona, a funkcja DI sprawdzi zarejestrowane `ILoggerFactory` w konstruktorze.

> [!NOTE]
> Programy obsługi, które używają Entity Framework nie powinny być rejestrowane jako pojedyncze.
