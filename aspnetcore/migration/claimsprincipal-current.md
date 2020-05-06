---
title: Migruj z ClaimsPrincipal. Current
author: mjrousos
description: Dowiedz się, jak przeprowadzić migrację z ClaimsPrincipal. Current, aby pobrać tożsamość i oświadczenia uwierzytelnionego użytkownika w ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776093"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migruj z ClaimsPrincipal. Current

W projektach ASP.NET 4. x często było używane [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) do pobrania tożsamości i oświadczeń aktualnie uwierzytelnionego użytkownika. W ASP.NET Core ta właściwość nie jest już ustawiona. Kod, w zależności od tego, musi zostać zaktualizowany, aby można było uzyskać aktualną tożsamość uwierzytelnionego użytkownika w inny sposób.

## <a name="context-specific-data-instead-of-static-data"></a>Dane specyficzne dla kontekstu zamiast danych statycznych

W przypadku korzystania z ASP.NET Core wartości obu `ClaimsPrincipal.Current` i `Thread.CurrentPrincipal` nie są ustawiane. Te właściwości reprezentują stan statyczny, który ASP.NET Core zwykle unika. Zamiast tego architektura ASP.NET Core ma pobierać zależności (takie jak tożsamość bieżącego użytkownika) z kolekcji usług specyficznych dla kontekstu (przy użyciu modelu [iniekcji zależności](xref:fundamentals/dependency-injection) (di)). Co więcej, `Thread.CurrentPrincipal` jest statycznego wątku, dlatego może nie utrwalać zmian w niektórych scenariuszach asynchronicznych ( `ClaimsPrincipal.Current` i po `Thread.CurrentPrincipal` prostu wywołuje domyślnie).

Aby zrozumieć, jakie problemy członkowie statyczni mogą prowadzić do scenariuszy asynchronicznych, należy wziąć pod uwagę następujący fragment kodu:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

Poprzedni przykładowy kod ustawia `Thread.CurrentPrincipal` i sprawdza swoją wartość przed i po oczekiwaniu na wywołanie asynchroniczne. `Thread.CurrentPrincipal`jest specyficzny dla *wątku* , w którym jest ustawiony, a metoda może wznowić wykonywanie w innym wątku po oczekiwania. W związku z `Thread.CurrentPrincipal` tym, jest obecny, gdy jest on sprawdzany po raz pierwszy, ale `await Task.Yield()`ma wartość null po wywołaniu.

Uzyskanie informacji o tożsamości bieżącego użytkownika z kolekcji DI Service jest bardziej weryfikowalnee, ponieważ tożsamości testowe mogą być łatwo wstrzykiwane.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Pobierz bieżącego użytkownika w aplikacji ASP.NET Core

Istnieje kilka opcji pobierania bieżącego uwierzytelnionego użytkownika `ClaimsPrincipal` w ASP.NET Core zamiast: `ClaimsPrincipal.Current`

* **ControllerBase. User**. Kontrolery MVC mogą uzyskać dostęp do bieżącego uwierzytelnionego użytkownika przy użyciu ich właściwości [użytkownika](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Składniki z dostępem do bieżącego `HttpContext` (na przykład oprogramowania pośredniczącego) mogą pobrać bieżącego użytkownika z elementu `ClaimsPrincipal` [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Przeszedł z elementu wywołującego**. Biblioteki bez dostępu do bieżącej `HttpContext` są często wywoływane z poziomu kontrolerów lub składników pośredniczących, a tożsamość bieżącego użytkownika jest przenoszona jako argument.
* **IHttpContextAccessor**. Projekt migrowany do ASP.NET Core może być zbyt duży, aby można było łatwo przekazać tożsamość bieżącego użytkownika do wszystkich potrzebnych lokalizacji. W takich przypadkach [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) może służyć jako obejście problemu. `IHttpContextAccessor`jest w stanie uzyskać dostęp do `HttpContext` bieżącego (jeśli istnieje). Jeśli jest używana, zobacz <xref:fundamentals/httpcontext>. Krótkoterminowe rozwiązanie do uzyskiwania informacji o tożsamości bieżącego użytkownika w kodzie, który nie został jeszcze zaktualizowany do pracy z ASP.NET Core architekturą o regulowanej mocy, będzie:

  * Udostępnij `IHttpContextAccessor` w kontenerze di przez wywołanie [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) w `Startup.ConfigureServices`.
  * Pobierz wystąpienie `IHttpContextAccessor` podczas uruchamiania i Zapisz je w zmiennej statycznej. Wystąpienie jest udostępniane dla kodu, który wcześniej pobiera bieżącego użytkownika z właściwości statycznej.
  * Pobierz `ClaimsPrincipal` użycie `HttpContextAccessor.HttpContext?.User`bieżącego użytkownika. Jeśli ten kod jest używany poza kontekstem żądania HTTP, `HttpContext` ma wartość null.

Ostatnia opcja, przy użyciu `IHttpContextAccessor` wystąpienia przechowywanego w zmiennej statycznej, jest sprzeczna z zasadami ASP.NET Core (przed przyłożeniem pożądanych zależności do zależności statycznych). Zaplanuj ostatecznie `IHttpContextAccessor` pobranie wystąpień z iniekcji zależności. Pomocnik statyczny może być przydatnego mostka, chociaż podczas migrowania dużych istniejących aplikacji ASP.NET, które były `ClaimsPrincipal.Current`wcześniej używane.
