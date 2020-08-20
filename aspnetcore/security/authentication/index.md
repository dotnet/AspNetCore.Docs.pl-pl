---
title: Omówienie uwierzytelniania ASP.NET Core
author: mjrousos
description: Informacje o uwierzytelnianiu w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632645"
---
# <a name="overview-of-aspnet-core-authentication"></a>Omówienie uwierzytelniania ASP.NET Core

Według [Jan Rousos](https://github.com/mjrousos)

Uwierzytelnianie to proces określania tożsamości użytkownika. [Autoryzacja](xref:security/authorization/introduction) to proces ustalania, czy użytkownik ma dostęp do zasobu. W ASP.NET Core uwierzytelnianie jest obsługiwane przez `IAuthenticationService` program, który jest używany przez [oprogramowanie pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania. Usługa uwierzytelniania używa zarejestrowanych programów obsługi uwierzytelniania do ukończenia akcji związanych z uwierzytelnianiem. Przykłady akcji związanych z uwierzytelnianiem obejmują:

* Uwierzytelnianie użytkownika.
* Odpowiada, gdy nieuwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu z ograniczeniami.

Zarejestrowane programy obsługi uwierzytelniania i ich opcje konfiguracji są nazywane "schematami".

Schematy uwierzytelniania są określane przez zarejestrowanie usług uwierzytelniania w programie `Startup.ConfigureServices` :

* Wywołując metodę rozszerzenia specyficzną dla schematu po wywołaniu metody (np `services.AddAuthentication` `AddJwtBearer` `AddCookie` . lub, na przykład). Te metody rozszerzające używają [AuthenticationBuilder. AddSchema](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) do rejestrowania schematów przy użyciu odpowiednich ustawień.
* Rzadziej, przez wywołanie [AuthenticationBuilder. AddSchema](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) bezpośrednio.

Na przykład poniższy kod rejestruje usługi uwierzytelniania i programy obsługi programu cookie oraz schematy uwierzytelniania okaziciela JWT:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication`Parametr `JwtBearerDefaults.AuthenticationScheme` jest nazwą schematu, który ma być używany domyślnie, gdy określony schemat nie jest wymagany.

W przypadku użycia wielu schematów zasady autoryzacji (lub atrybuty autoryzacji) mogą [określać schemat uwierzytelniania (lub schematy),](xref:security/authorization/limitingidentitybyscheme) od których zależą w celu uwierzytelnienia użytkownika. W powyższym przykładzie cookie schemat uwierzytelniania może być używany przez określenie jego nazwy ( `CookieAuthenticationDefaults.AuthenticationScheme` Domyślnie, chociaż przy wywoływaniu można podać inną nazwę `AddCookie` ).

W niektórych przypadkach wywołanie `AddAuthentication` jest wykonywane automatycznie przez inne metody rozszerzenia. Na przykład podczas korzystania z [ASP.NET Core Identity](xref:security/authentication/identity) programu `AddAuthentication` jest wywoływana wewnętrznie.

Oprogramowanie pośredniczące uwierzytelniania jest dodawane w programie `Startup.Configure` przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metody rozszerzenia w aplikacji `IApplicationBuilder` . Wywołanie `UseAuthentication` rejestruje oprogramowanie pośredniczące, które używa poprzednio zarejestrowanego schematu uwierzytelniania. Wywołaj `UseAuthentication` przed każdym oprogramowanie pośredniczące zależne od użytkowników, którzy są uwierzytelniani. W przypadku korzystania z routingu punktów końcowych wywołanie `UseAuthentication` musi być następujące:

* Po `UseRouting` , tak aby informacje o trasie były dostępne na potrzeby podejmowania decyzji dotyczących uwierzytelniania.
* Wcześniej `UseEndpoints` , aby użytkownicy mieli uwierzytelnieni przed uzyskaniem dostępu do punktów końcowych.

## <a name="authentication-concepts"></a>Pojęcia związane z uwierzytelnianiem

### <a name="authentication-scheme"></a>Schemat uwierzytelniania

Schematem uwierzytelniania jest nazwa, która odnosi się do:

* Procedura obsługi uwierzytelniania.
* Opcje konfigurowania określonego wystąpienia programu obsługi.

Schematy są przydatne jako mechanizm do odwoływania się do zachowań uwierzytelnianie, wyzwanie i Zabroń skojarzonej procedury obsługi. Na przykład zasady autoryzacji mogą używać nazw schematów, aby określić, który schemat uwierzytelniania (lub schematy) powinien zostać użyty do uwierzytelnienia użytkownika. Podczas konfigurowania uwierzytelniania często można określić domyślny schemat uwierzytelniania. Domyślny schemat jest używany, chyba że zasób żąda określonego schematu. Możliwe jest również:

* Określ różne domyślne schematy do użycia w akcjach uwierzytelniania, wyzwania i zabraniania.
* Połącz wiele schematów przy użyciu [schematów zasad](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Procedura obsługi uwierzytelniania

Procedura obsługi uwierzytelniania:

* Jest typem, który implementuje zachowanie schematu.
* Jest pochodną <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> lub <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .
* Ma główną odpowiedzialność za uwierzytelnianie użytkowników.

W oparciu o konfigurację schematu uwierzytelniania i kontekst żądania przychodzącego, programy obsługi uwierzytelniania:

* Konstruowanie <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> obiektów reprezentujących tożsamość użytkownika w przypadku pomyślnego uwierzytelnienia.
* Zwróć "Brak wyniku" lub "Niepowodzenie", jeśli uwierzytelnianie nie powiedzie się.
* Mają metody dla akcji wyzwania i Zabraniaj, gdy użytkownicy próbują uzyskać dostęp do zasobów:
  * Dostęp do nich nie jest autoryzowany (Zabroń).
  * Gdy nie są uwierzytelniane (wyzwanie).

### <a name="authenticate"></a>Uwierzytelnianie

Akcja uwierzytelniania schematu uwierzytelniania jest odpowiedzialna za konstruowanie tożsamości użytkownika na podstawie kontekstu żądania. Zwraca <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> informację o tym, czy uwierzytelnianie zakończyło się pomyślnie, a jeśli tak, tożsamość użytkownika w biletu uwierzytelniania. Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Przykłady uwierzytelniania obejmują:

* cookieSchemat uwierzytelniania, który konstruuje tożsamość użytkownika z cookie s.
* Schemat okaziciela JWT deserializacji i weryfikacji tokenu okaziciela JWT w celu utworzenia tożsamości użytkownika.

### <a name="challenge"></a>Zadanie

Wyzwanie uwierzytelniania jest wywoływane przez autoryzację, gdy nieuwierzytelniony użytkownik żąda punktu końcowego wymagającego uwierzytelniania. Jest wystawiane wyzwanie uwierzytelniania, na przykład gdy użytkownik anonimowy żąda zasobu z ograniczeniami lub klika łącze logowania. Autoryzacja wywołuje wyzwanie przy użyciu określonych schematów uwierzytelniania lub wartość domyślną, jeśli nie została określona. Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Przykłady wyzwania uwierzytelniania obejmują:

* cookieSchemat uwierzytelniania przekierowuje użytkownika do strony logowania.
* Schemat okaziciela JWT zwracający wynik 401 z `www-authenticate: bearer` nagłówkiem.

Akcja wyzwania powinna dać użytkownikowi informacje o mechanizmie uwierzytelniania używanym do uzyskiwania dostępu do żądanego zasobu.

### <a name="forbid"></a>Uniemożliwia

Akcja zabraniania schematu uwierzytelniania jest wywoływana przez autoryzację, gdy uwierzytelniony użytkownik próbuje uzyskać dostęp do zasobu, do którego nie ma dostępu. Zobacz: <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Przykładem zabraniania uwierzytelniania są:
* cookieSchemat uwierzytelniania przekierowuje użytkownika do strony wskazującej dostęp był zabroniony.
* Schemat okaziciela JWT zwracający wynik 403.
* Niestandardowy schemat uwierzytelniania przekierowuje do strony, na której użytkownik może zażądać dostępu do zasobu.

Akcja Zabroń może pozwolić użytkownikowi na:

* Są uwierzytelniane.
* Nie mogą uzyskać dostępu do żądanego zasobu.

Poniżej znajdują się linki dotyczące różnic między wyzwaniem i zabranianiem:

* [Wyzwania i Zabroń z obsługą zasobów operacyjnych](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Różnice między wyzwaniem i zabranianiem](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Dostawcy uwierzytelniania na dzierżawcę

Platforma ASP.NET Core nie ma wbudowanego rozwiązania do uwierzytelniania wielodostępnego.
Chociaż jest to możliwe, aby klienci mogli pisać jedną, przy użyciu wbudowanych funkcji, zalecamy, aby w tym celu przyjrzeć się klientom na [rdzeń](https://www.orchardcore.net/) .

Rdzeń sadu:

* Modularna i wielodostępna platforma aplikacji Open Source skompilowana przy użyciu ASP.NET Core.
* System zarządzania zawartością (CMS) zbudowany na podstawie tej struktury aplikacji.

Zobacz [podstawowe źródło sadu](https://github.com/OrchardCMS/OrchardCore) dla przykładu dostawców uwierzytelniania na dzierżawcę.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [Globalnie Wymagaj uwierzytelnionych użytkowników](xref:security/authorization/secure-data#rau)