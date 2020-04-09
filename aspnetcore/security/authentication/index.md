---
title: Omówienie uwierzytelniania ASP.NET rdzenia
author: mjrousos
description: Dowiedz się więcej o uwierzytelnianiu w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434333"
---
# <a name="overview-of-aspnet-core-authentication"></a>Omówienie uwierzytelniania ASP.NET Core

Przez [Mike Rousos](https://github.com/mjrousos)

Uwierzytelnianie to proces określania tożsamości użytkownika. [Autoryzacja](xref:security/authorization/introduction) to proces określania, czy użytkownik ma dostęp do zasobu. W ASP.NET Core uwierzytelnianie jest obsługiwane `IAuthenticationService`przez program , który jest używany przez [oprogramowanie pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania . Usługa uwierzytelniania używa zarejestrowanych programów obsługi uwierzytelniania do wykonania akcji związanych z uwierzytelnianiem. Przykłady akcji związanych z uwierzytelnianiem obejmują:

* Uwierzytelnianie użytkownika.
* Odpowiadanie, gdy nieuwierzytywy użytkownik próbuje uzyskać dostęp do zasobu z ograniczeniami.

Zarejestrowane programy obsługi uwierzytelniania i ich opcje konfiguracji są nazywane "schematami".

Schematy uwierzytelniania są określane przez `Startup.ConfigureServices`rejestrowanie usług uwierzytelniania w:

* Wywołując metodę rozszerzenia specyficzne dla schematu `services.AddAuthentication` po `AddJwtBearer` wywołaniu (na przykład lub `AddCookie`, na przykład). Te metody rozszerzenia używają [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) do rejestrowania schematów z odpowiednimi ustawieniami.
* Rzadziej, wywołując [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) bezpośrednio.

Na przykład następujący kod rejestruje usługi uwierzytelniania i programy obsługi dla plików cookie i schematów uwierzytelniania na okaziciela JWT:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

Parametr `AddAuthentication` `JwtBearerDefaults.AuthenticationScheme` jest nazwą schematu, który ma być używany domyślnie, gdy określony schemat nie jest wymagany.

Jeśli używanych jest wiele schematów, zasady autoryzacji (lub atrybuty autoryzacji) mogą [określać schemat uwierzytelniania (lub schematy),](xref:security/authorization/limitingidentitybyscheme) od których zależą do uwierzytelnienia użytkownika. W powyższym przykładzie schemat uwierzytelniania plików cookie może`CookieAuthenticationDefaults.AuthenticationScheme` być używany przez określenie jego nazwy `AddCookie`(domyślnie, chociaż podczas wywoływania można podać inną nazwę).

W niektórych przypadkach `AddAuthentication` wywołanie jest automatycznie dokonywane przez inne metody rozszerzenia. Na przykład podczas korzystania [z ASP.NET podstawowej tożsamości](xref:security/authentication/identity), `AddAuthentication` jest wywoływana wewnętrznie.

Oprogramowanie pośredniczące uwierzytelniania `Startup.Configure` jest <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> dodawane przez wywołanie metody rozszerzenia w aplikacji `IApplicationBuilder`. Wywołanie `UseAuthentication` rejestruje oprogramowanie pośredniczące, które używa wcześniej zarejestrowanych schematów uwierzytelniania. Zadzwoń `UseAuthentication` przed każdym oprogramowaniem pośredniczącym, które zależy od uwierzytelnienia użytkowników. Podczas korzystania z routingu `UseAuthentication` punktu końcowego, wywołanie musi przejść:

* Po `UseRouting`, tak aby informacje o trasie były dostępne dla decyzji o uwierzytelnianiu.
* Przed `UseEndpoints`, tak aby użytkownicy są uwierzytelnione przed dostępem do punktów końcowych.

## <a name="authentication-concepts"></a>Pojęcia dotyczące uwierzytelniania

### <a name="authentication-scheme"></a>Schemat uwierzytelniania

Schemat uwierzytelniania to nazwa odpowiadająca:

* Program obsługi uwierzytelniania.
* Opcje konfigurowania tego określonego wystąpienia programu obsługi.

Schematy są przydatne jako mechanizm odwoływania się do uwierzytelniania, wyzwanie i zabronić zachowań skojarzonego programu obsługi. Na przykład zasady autoryzacji można użyć nazw schematów, aby określić, który schemat uwierzytelniania (lub schematy) powinny być używane do uwierzytelniania użytkownika. Podczas konfigurowania uwierzytelniania często określa się domyślny schemat uwierzytelniania. Schemat domyślny jest używany, chyba że zasób zażąda określonego schematu. Możliwe jest również:

* Określ różne domyślne schematy do uwierzytelniania, kwestionowania i zabroniania akcji.
* Połącz wiele schematów w jeden za pomocą [schematów polityki](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Program obsługi uwierzytelniania

Program obsługi uwierzytelniania:

* Jest typem, który implementuje zachowanie schematu.
* Pochodzi z <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> lub <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.
* Ponosi główną odpowiedzialność za uwierzytelnienie użytkowników.

Na podstawie konfiguracji schematu uwierzytelniania i kontekstu żądania przychodzącego programy obsługi uwierzytelniania:

* Konstruuj <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> obiekty reprezentujące tożsamość użytkownika, jeśli uwierzytelnianie zakończy się pomyślnie.
* Zwraca "brak wyniku" lub "błąd", jeśli uwierzytelnianie nie powiedzie się.
* Mieć metody kwestionowania i zabraniania działań, gdy użytkownicy próbują uzyskać dostęp do zasobów:
  * Są one nieautoryzowane do dostępu (zabronić).
  * Gdy są one nieuwierzycnicjowane (wyzwanie).

### <a name="authenticate"></a>Uwierzytelnianie

Akcja uwierzytelnienia schematu uwierzytelniania jest odpowiedzialna za tworzenie tożsamości użytkownika na podstawie kontekstu żądania. Zwraca wskazujący, <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> czy uwierzytelnianie zakończyło się pomyślnie, a jeśli tak, tożsamość użytkownika w bilecie uwierzytelniania. Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Przykłady uwierzytelniania obejmują:

* Schemat uwierzytelniania plików cookie konstruujący tożsamość użytkownika na podstawie plików cookie.
* Schemat okaziciela JWT deserializacji i sprawdzania poprawności tokenu nośnika JWT do konstruowania tożsamości użytkownika.

### <a name="challenge"></a>Wyzwanie

Wyzwanie uwierzytelniania jest wywoływane przez autoryzację, gdy nieuwierzyty użytkownik żąda punktu końcowego, który wymaga uwierzytelniania. Wyzwanie uwierzytelniania jest wydawane, na przykład, gdy użytkownik anonimowy żąda zasobu z ograniczeniami lub klika łącze logowania. Autoryzacja wywołuje wyzwanie przy użyciu określonego schematu uwierzytelniania lub domyślnie, jeśli nie określono żadnego. Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Przykłady wyzwań związanych z uwierzytelnianiem obejmują:

* Schemat uwierzytelniania plików cookie przekierowujący użytkownika do strony logowania.
* Schemat okaziciela JWT zwracający wynik 401 z nagłówkiem. `www-authenticate: bearer`

Akcja wyzwania powinna poinformować użytkownika, jaki mechanizm uwierzytelniania ma być używany do uzyskiwania dostępu do żądanego zasobu.

### <a name="forbid"></a>Zabraniają

Akcja zabronienia schematu uwierzytelniania jest wywoływana przez autoryzację, gdy uwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu, do których nie ma dostępu. Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Przykłady zabraniające uwierzytelniania obejmują:
* Schemat uwierzytelniania plików cookie przekierowujący użytkownika na stronę wskazującą dostęp był zabroniony.
* Schemat na okaziciela JWT z wynikiem 403.
* Niestandardowy schemat uwierzytelniania przekierowujący do strony, na której użytkownik może zażądać dostępu do zasobu.

Akcja zabronić może poinformować użytkownika:

* Są one uwierzytelnione.
* Nie mogą uzyskać dostępu do żądanego zasobu.

Zobacz następujące linki, aby uzyskać różnice między wyzwaniem a zakazem:

* [Zaprożyj i zabronij z obsługi zasobów operacyjnych](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Różnice między wyzwaniem a zakazem](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Dostawcy uwierzytelniania na dzierżawę

ASP.NET Core framework nie ma wbudowanego rozwiązania do uwierzytelniania wielodostępnego.
Chociaż z pewnością jest to możliwe dla klientów, aby napisać jeden, za pomocą wbudowanych funkcji, zalecamy klientom zajrzeć do [Orchard Core](https://www.orchardcore.net/) w tym celu.

Rdzeń sadu to:

* Modułowa i wielodostępna struktura aplikacji typu open source zbudowana z ASP.NET Core.
* System zarządzania zawartością (CMS) zbudowany na podstawie tej struktury aplikacji.

Zobacz źródło [Orchard Core](https://github.com/OrchardCMS/OrchardCore) na przykład dostawców uwierzytelniania na dzierżawę.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
