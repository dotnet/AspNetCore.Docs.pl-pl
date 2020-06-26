---
title: Bezpieczny ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor aplikacje WebAssemlby jako aplikacje jednostronicowe (aplikacji jednostronicowych).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/index
ms.openlocfilehash: 3fdea9f553cbd37f2c27740487cfe030ebd81937
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402094"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Bezpieczny ASP.NET CoreBlazor WebAssembly

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor WebAssemblyaplikacje są zabezpieczone w taki sam sposób, jak aplikacje jednostronicowe (aplikacji jednostronicowych). Istnieje kilka podejścia do uwierzytelniania użytkowników w aplikacji jednostronicowych, ale najpopularniejsze i kompleksowe podejście polega na użyciu implementacji opartej na [protokole OAuth 2,0](https://oauth.net/), takim jak [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteka uwierzytelniania

Blazor WebAssemblyobsługuje uwierzytelnianie i Autoryzowanie aplikacji przy użyciu programu OIDC za pośrednictwem [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) biblioteki. Biblioteka zawiera zestaw elementów pierwotnych do bezproblemowego uwierzytelniania w odniesieniu do ASP.NET Core. Biblioteka integruje ASP.NET Core Identity z obsługą autoryzacji interfejsu API utworzoną na [ Identity serwerze](https://identityserver.io/). Biblioteka może być uwierzytelniana względem dowolnego dostawcy innej firmy, Identity który obsługuje OIDC, które są nazywane dostawcami OpenID Connect (op).

Obsługa uwierzytelniania w programie Blazor WebAssembly jest oparta na bazie `oidc-client.js` biblioteki, która jest używana do obsługi szczegółowych informacji o podstawowym protokole uwierzytelniania.

Dostępne są inne opcje uwierzytelniania aplikacji jednostronicowych, takie jak używanie plików cookie SameSite. Jednak projekt inżynieryjny Blazor WebAssembly jest rozliczany przy użyciu protokołu OAuth i OIDC jako najlepszą opcję uwierzytelniania w Blazor WebAssembly aplikacjach. [Uwierzytelnianie oparte na tokenach](xref:security/anti-request-forgery#token-based-authentication) oparte na [tokenach sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) zostało wybrane w ramach [uwierzytelniania opartego na plikach cookie](xref:security/anti-request-forgery#cookie-based-authentication) dla powodów funkcjonalnych i zabezpieczeń:

* Użycie protokołu opartego na tokenach oferuje mniejszy obszar obszaru ataków, ponieważ tokeny nie są wysyłane we wszystkich żądaniach.
* Punkty końcowe serwera nie wymagają ochrony przed [fałszerstwem żądania między lokacjami (CSRF)](xref:security/anti-request-forgery) , ponieważ tokeny są wysyłane jawnie. Dzięki temu można hostować Blazor WebAssembly aplikacje obok aplikacji MVC lub Razor Pages.
* Tokeny mają węższe uprawnienia niż pliki cookie. Na przykład tokeny nie mogą być używane do zarządzania kontem użytkownika ani zmieniać hasła użytkownika, chyba że te funkcje są jawnie zaimplementowane.
* Tokeny mają krótki okres istnienia, domyślnie jedną godzinę, która ogranicza okno ataku. Tokeny można również odwołać w dowolnym momencie.
* Samodzielna oferta JWTs gwarantuje klientowi i serwerowi proces uwierzytelniania. Na przykład klient ma środki do wykrycia i sprawdzenia, czy otrzymane tokeny są wiarygodne i są emitowane w ramach danego procesu uwierzytelniania. Jeśli osoba trzecia podejmie próbę przełączenia tokenu w trakcie procesu uwierzytelniania, klient może wykryć przełączony token i uniknąć korzystania z niego.
* Tokeny z uwierzytelnianiem OAuth i OIDC nie polegają na tym, że agent użytkownika działa prawidłowo, aby upewnić się, że aplikacja jest bezpieczna.
* Protokoły oparte na tokenach, takie jak OAuth i OIDC, umożliwiają uwierzytelnianie i Autoryzowanie aplikacji hostowanych i autonomicznych z tym samym zestawem charakterystyk zabezpieczeń.

## <a name="authentication-process-with-oidc"></a>Proces uwierzytelniania za pomocą OIDC

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)Biblioteka zawiera kilka elementów podstawowych, które implementują uwierzytelnianie i autoryzację przy użyciu OIDC. Uwierzytelnianie odbywa się w następujący sposób:

* Gdy użytkownik anonimowy wybierze przycisk Zaloguj lub zażąda strony z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) zastosowanym atrybutem, użytkownik zostanie przekierowany do strony logowania aplikacji ( `/authentication/login` ).
* Na stronie Logowanie Biblioteka uwierzytelniania przygotowuje przekierowanie do punktu końcowego autoryzacji. Punkt końcowy autoryzacji jest poza Blazor WebAssembly aplikacją i może być hostowany w osobnym miejscu pochodzenia. Punkt końcowy jest odpowiedzialny za określenie, czy użytkownik jest uwierzytelniany i w celu wystawiania co najmniej jednego tokenu w odpowiedzi. Biblioteka uwierzytelniania umożliwia wywołanie zwrotne logowania w celu uzyskania odpowiedzi uwierzytelniania.
  * Jeśli użytkownik nie jest uwierzytelniony, użytkownik zostanie przekierowany do podstawowego systemu uwierzytelniania, który jest zwykle ASP.NET Core Identity .
  * Jeśli użytkownik został już uwierzytelniony, punkt końcowy autoryzacji generuje odpowiednie tokeny i przekierowuje przeglądarkę z powrotem do punktu końcowego wywołania zwrotnego logowania ( `/authentication/login-callback` ).
* Gdy Blazor WebAssembly aplikacja ładuje punkt końcowy wywołania zwrotnego logowania ( `/authentication/login-callback` ), odpowiedź uwierzytelniania jest przetwarzana.
  * Jeśli proces uwierzytelniania zakończy się pomyślnie, użytkownik zostanie uwierzytelniony i opcjonalnie zostanie wysłany z powrotem do oryginalnego chronionego adresu URL, którego zażądał użytkownik.
  * Jeśli z jakiegoś powodu proces uwierzytelniania zakończy się niepowodzeniem, użytkownik zostanie wysłany do strony logowania zakończonej niepowodzeniem ( `/authentication/login-failed` ) i zostanie wyświetlony komunikat o błędzie.

## <a name="authorization"></a>Autoryzacja

W Blazor WebAssembly aplikacjach można pominąć sprawdzanie autoryzacji, ponieważ wszystkie kody po stronie klienta mogą być modyfikowane przez użytkowników. Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.

**Zawsze sprawdzaj autoryzację na serwerze w ramach dowolnych punktów końcowych interfejsu API, do których uzyskuje dostęp aplikacja po stronie klienta.**

## <a name="refresh-tokens"></a>Odśwież tokeny

Tokeny odświeżania nie mogą być zabezpieczone po stronie klienta w Blazor WebAssembly aplikacjach. W związku z tym tokeny odświeżania nie powinny być wysyłane do aplikacji do użytku bezpośredniego.

Tokeny odświeżania mogą być obsługiwane i używane przez aplikację po stronie serwera w hostowanym Blazor WebAssembly rozwiązaniu, aby uzyskać dostęp do interfejsów API innych firm. Aby uzyskać więcej informacji, zobacz <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji

Artykuły w ramach tego *omówienia* zawierają informacje dotyczące uwierzytelniania użytkowników w Blazor WebAssembly aplikacjach względem określonych dostawców.

Aplikacje autonomiczne Blazor WebAssembly :

* [Ogólne wskazówki dla dostawców OIDC i biblioteki uwierzytelniania webassembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Konta Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Usługa Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Hostowane Blazor WebAssembly aplikacje:

* [Usługa Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [IdentityServer](xref:blazor/security/webassembly/hosted-with-identity-server)

Dalsze wskazówki dotyczące konfiguracji znajdują się w temacie <xref:blazor/security/webassembly/additional-scenarios> .
