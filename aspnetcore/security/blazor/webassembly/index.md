---
title: Bezpieczny ASP.NET Core Blazor zestaw Webassembly
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor aplikacje WebAssemlby jako aplikacje jednostronicowe (aplikacji jednostronicowych).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: ce780afe3e69d2526698cf3694fe70394fc17847
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84253477"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Bezpieczny ASP.NET Core Blazor zestaw Webassembly

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

BlazorAplikacje webassembly są zabezpieczone w taki sam sposób, jak aplikacje jednostronicowe (aplikacji jednostronicowych). Istnieje kilka podejścia do uwierzytelniania użytkowników w aplikacji jednostronicowych, ale najpopularniejsze i kompleksowe podejście polega na użyciu implementacji opartej na [protokole OAuth 2,0](https://oauth.net/), takim jak [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteka uwierzytelniania

BlazorZestaw webassembly obsługuje uwierzytelnianie i Autoryzowanie aplikacji przy użyciu usługi OIDC za pośrednictwem biblioteki [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Biblioteka zawiera zestaw elementów pierwotnych do bezproblemowego uwierzytelniania w odniesieniu do ASP.NET Core. Biblioteka integruje ASP.NET Core Identity z obsługą autoryzacji interfejsu API utworzoną na [ Identity serwerze](https://identityserver.io/). Biblioteka może być uwierzytelniana względem dowolnego dostawcy innej firmy, Identity który obsługuje OIDC, które są nazywane dostawcami OpenID Connect (op).

Obsługa uwierzytelniania w Blazor zestawie webassembly jest oparta na bibliotece *OIDC-Client. js* , która jest używana do obsługi szczegółowych informacji o podstawowym protokole uwierzytelniania.

Dostępne są inne opcje uwierzytelniania aplikacji jednostronicowych, takie jak używanie plików cookie SameSite. Jednak projekt inżynieryjny Blazor zestawu webassembly jest rozliczany przy użyciu protokołu OAuth i OIDC jako najlepszą opcję uwierzytelniania w Blazor aplikacjach webassembly. [Uwierzytelnianie oparte na tokenach](xref:security/anti-request-forgery#token-based-authentication) oparte na [tokenach sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) zostało wybrane w ramach [uwierzytelniania opartego na plikach cookie](xref:security/anti-request-forgery#cookie-based-authentication) dla powodów funkcjonalnych i zabezpieczeń:

* Użycie protokołu opartego na tokenach oferuje mniejszy obszar obszaru ataków, ponieważ tokeny nie są wysyłane we wszystkich żądaniach.
* Punkty końcowe serwera nie wymagają ochrony przed [fałszerstwem żądania między lokacjami (CSRF)](xref:security/anti-request-forgery) , ponieważ tokeny są wysyłane jawnie. Dzięki temu można hostować Blazor aplikacje webassembly obok aplikacji MVC lub Razor Pages.
* Tokeny mają węższe uprawnienia niż pliki cookie. Na przykład tokeny nie mogą być używane do zarządzania kontem użytkownika ani zmieniać hasła użytkownika, chyba że te funkcje są jawnie zaimplementowane.
* Tokeny mają krótki okres istnienia, domyślnie jedną godzinę, która ogranicza okno ataku. Tokeny można również odwołać w dowolnym momencie.
* Samodzielna oferta JWTs gwarantuje klientowi i serwerowi proces uwierzytelniania. Na przykład klient ma środki do wykrycia i sprawdzenia, czy otrzymane tokeny są wiarygodne i są emitowane w ramach danego procesu uwierzytelniania. Jeśli osoba trzecia podejmie próbę przełączenia tokenu w trakcie procesu uwierzytelniania, klient może wykryć przełączony token i uniknąć korzystania z niego.
* Tokeny z uwierzytelnianiem OAuth i OIDC nie polegają na tym, że agent użytkownika działa prawidłowo, aby upewnić się, że aplikacja jest bezpieczna.
* Protokoły oparte na tokenach, takie jak OAuth i OIDC, umożliwiają uwierzytelnianie i Autoryzowanie aplikacji hostowanych i autonomicznych z tym samym zestawem charakterystyk zabezpieczeń.

## <a name="authentication-process-with-oidc"></a>Proces uwierzytelniania za pomocą OIDC

Biblioteka [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) oferuje kilka pierwotnych implementacji uwierzytelniania i autoryzacji przy użyciu OIDC. Uwierzytelnianie odbywa się w następujący sposób:

* Gdy użytkownik anonimowy wybierze przycisk Zaloguj lub zażąda strony z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) zastosowanym atrybutem, użytkownik zostanie przekierowany do strony logowania aplikacji ( `/authentication/login` ).
* Na stronie Logowanie Biblioteka uwierzytelniania przygotowuje przekierowanie do punktu końcowego autoryzacji. Punkt końcowy autoryzacji jest poza Blazor aplikacją webassembly i może być hostowany w osobnym miejscu pochodzenia. Punkt końcowy jest odpowiedzialny za określenie, czy użytkownik jest uwierzytelniany i w celu wystawiania co najmniej jednego tokenu w odpowiedzi. Biblioteka uwierzytelniania umożliwia wywołanie zwrotne logowania w celu uzyskania odpowiedzi uwierzytelniania.
  * Jeśli użytkownik nie jest uwierzytelniony, użytkownik zostanie przekierowany do podstawowego systemu uwierzytelniania, który jest zwykle ASP.NET Core Identity .
  * Jeśli użytkownik został już uwierzytelniony, punkt końcowy autoryzacji generuje odpowiednie tokeny i przekierowuje przeglądarkę z powrotem do punktu końcowego wywołania zwrotnego logowania ( `/authentication/login-callback` ).
* Gdy Blazor aplikacja webassembly ładuje punkt końcowy wywołania zwrotnego logowania ( `/authentication/login-callback` ), odpowiedź uwierzytelniania jest przetwarzana.
  * Jeśli proces uwierzytelniania zakończy się pomyślnie, użytkownik zostanie uwierzytelniony i opcjonalnie zostanie wysłany z powrotem do oryginalnego chronionego adresu URL, którego zażądał użytkownik.
  * Jeśli z jakiegoś powodu proces uwierzytelniania zakończy się niepowodzeniem, użytkownik zostanie wysłany do strony logowania zakończonej niepowodzeniem ( `/authentication/login-failed` ) i zostanie wyświetlony komunikat o błędzie.

## <a name="authorization"></a>Autoryzacja

W Blazor aplikacjach webassembly sprawdzanie autoryzacji można obejść, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników. Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.

**Zawsze sprawdzaj autoryzację na serwerze w ramach dowolnych punktów końcowych interfejsu API, do których uzyskuje dostęp aplikacja po stronie klienta.**

## <a name="refresh-tokens"></a>Odśwież tokeny

Tokeny odświeżania nie mogą być zabezpieczone po stronie klienta w Blazor aplikacjach webassembly. W związku z tym tokeny odświeżania nie powinny być wysyłane do aplikacji do użytku bezpośredniego.

Tokeny odświeżania mogą być obsługiwane i używane przez aplikację po stronie serwera w hostowanym Blazor rozwiązaniu Webassembly, aby uzyskać dostęp do interfejsów API innych firm. Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji

Artykuły w ramach tego *omówienia* zawierają informacje dotyczące uwierzytelniania użytkowników w Blazor aplikacjach zestawu webassembly dla określonych dostawców.

Autonomiczne Blazor aplikacje webassembly:

* [Ogólne wskazówki dla dostawców OIDC i biblioteki uwierzytelniania webassembly](xref:security/blazor/webassembly/standalone-with-authentication-library)
* [Konta Microsoft](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [Usługa Azure Active Directory (AAD)](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:security/blazor/webassembly/standalone-with-azure-active-directory-b2c)

Hostowane Blazor aplikacje webassembly:

* [Usługa Azure Active Directory (AAD)](xref:security/blazor/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:security/blazor/webassembly/hosted-with-azure-active-directory-b2c)
* [IdentityServer](xref:security/blazor/webassembly/hosted-with-identity-server)

Dalsze wskazówki dotyczące konfiguracji znajdują się w temacie <xref:security/blazor/webassembly/additional-scenarios> .
