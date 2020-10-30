---
title: Migruj z ClaimsPrincipal. Current
author: mjrousos
description: Dowiedz się, jak przeprowadzić migrację z ClaimsPrincipal. Current, aby pobrać tożsamość i oświadczenia uwierzytelnionego użytkownika w ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 3aa0adb299789efbb071cdb934d43832a84cf540
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059770"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migruj z ClaimsPrincipal. Current

W projektach ASP.NET 4. x często było używane [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) do pobrania tożsamości i oświadczeń aktualnie uwierzytelnionego użytkownika. W ASP.NET Core ta właściwość nie jest już ustawiona. Kod, w zależności od tego, musi zostać zaktualizowany, aby można było uzyskać aktualną tożsamość uwierzytelnionego użytkownika w inny sposób.

## <a name="context-specific-state-instead-of-static-state"></a>Stan specyficzny dla kontekstu zamiast stanu statycznego

W przypadku korzystania z ASP.NET Core wartości obu `ClaimsPrincipal.Current` i `Thread.CurrentPrincipal` nie są ustawiane. Te właściwości reprezentują stan statyczny, który ASP.NET Core zwykle unika. Zamiast tego ASP.NET Core używa [iniekcji zależności](xref:fundamentals/dependency-injection) (di) w celu zapewnienia zależności, takich jak tożsamość bieżącego użytkownika. Pobieranie tożsamości bieżącego użytkownika z programu DI jest również bardziej weryfikowalne, ponieważ tożsamości testowe mogą być łatwo wstrzykiwane.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Pobierz bieżącego użytkownika w aplikacji ASP.NET Core

Istnieje kilka opcji pobierania bieżącego uwierzytelnionego użytkownika `ClaimsPrincipal` w ASP.NET Core zamiast `ClaimsPrincipal.Current` :

* **ControllerBase. User** . Kontrolery MVC mogą uzyskać dostęp do bieżącego uwierzytelnionego użytkownika przy użyciu ich właściwości [użytkownika](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User** . Składniki z dostępem do bieżącego `HttpContext` (na przykład oprogramowania pośredniczącego) mogą pobrać bieżącego użytkownika `ClaimsPrincipal` z elementu [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Przeszedł z elementu wywołującego** . Biblioteki bez dostępu do bieżącej `HttpContext` są często wywoływane z poziomu kontrolerów lub składników pośredniczących, a tożsamość bieżącego użytkownika jest przenoszona jako argument.
* **IHttpContextAccessor** . Projekt migrowany do ASP.NET Core może być zbyt duży, aby można było łatwo przekazać tożsamość bieżącego użytkownika do wszystkich potrzebnych lokalizacji. W takich przypadkach [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) może służyć jako obejście problemu. `IHttpContextAccessor` jest w stanie uzyskać dostęp do bieżącego `HttpContext` (jeśli istnieje). Jeśli jest używana, zobacz <xref:fundamentals/httpcontext> . Krótkoterminowe rozwiązanie do uzyskiwania informacji o tożsamości bieżącego użytkownika w kodzie, który nie został jeszcze zaktualizowany do pracy z ASP.NET Core architekturą o regulowanej mocy, będzie:

  * Udostępnij `IHttpContextAccessor` w kontenerze di przez wywołanie [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) w `Startup.ConfigureServices` .
  * Pobierz wystąpienie `IHttpContextAccessor` podczas uruchamiania i Zapisz je w zmiennej statycznej. Wystąpienie jest udostępniane dla kodu, który wcześniej pobiera bieżącego użytkownika z właściwości statycznej.
  * Pobierz użycie bieżącego użytkownika `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User` . Jeśli ten kod jest używany poza kontekstem żądania HTTP, `HttpContext` ma wartość null.

Końcowa opcja, przy użyciu `IHttpContextAccessor` wystąpienia przechowywanego w zmiennej statycznej, jest sprzeczna z zasadą ASP.NET Core zalecanym z tytułu odroczonych zależności do zależności statycznych. Zaplanuj, aby ostatecznie pobrać `IHttpContextAccessor` wystąpienia z programu. Pomocnik statyczny może być przydatnego mostka, chociaż podczas migrowania dużych istniejących aplikacji ASP.NET `ClaimsPrincipal.Current` .
