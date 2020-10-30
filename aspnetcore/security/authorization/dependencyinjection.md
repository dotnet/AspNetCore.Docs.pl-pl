---
title: Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wstrzyknąć programy obsługi wymagań autoryzacji do aplikacji ASP.NET Core przy użyciu iniekcji zależności.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060264"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Wstrzykiwanie zależności w programach obsługi wymagań w ASP.NET Core

<a name="security-authorization-di"></a>

[Procedury obsługi autoryzacji muszą być zarejestrowane](xref:security/authorization/policies#handler-registration) w kolekcji usług podczas konfiguracji przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).

Załóżmy, że masz repozytorium reguł, które chcesz oszacować wewnątrz procedury obsługi autoryzacji i że repozytorium zostało zarejestrowane w kolekcji usług. Autoryzacja rozwiązuje i wprowadza do konstruktora.

Na przykład, aby użyć ASP. Infrastruktura rejestrowania w sieci, wstrzyknąć `ILoggerFactory` do procedury obsługi. Taka procedura obsługi może wyglądać podobnie do następującego kodu:

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

Poprzednią procedurę obsługi można zarejestrować w każdym [okresie istnienia usługi](/dotnet/core/extensions/dependency-injection#service-lifetimes). Następujący kod używa `AddSingleton` do rejestrowania poprzedniej procedury obsługi:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Wystąpienie procedury obsługi jest tworzone podczas uruchamiania aplikacji i służy do dodawania iniekcji zarejestrowanej `ILoggerFactory` w konstruktorze.

> [!NOTE]
> Programy obsługi, które używają Entity Framework nie powinny być rejestrowane jako pojedyncze.
