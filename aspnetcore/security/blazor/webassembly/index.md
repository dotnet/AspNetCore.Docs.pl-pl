---
title: Bezpieczny ASP.NET Core Blazor zestaw webassembly
author: guardrex
description: Dowiedz się, Blazor jak zabezpieczyć aplikacje WebAssemlby jako aplikacje jednostronicowe (aplikacji jednostronicowych).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: c096419f4866ea2f1db135594c4b88c89c7c90d1
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138420"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Bezpieczny ASP.NET Core Blazor zestaw webassembly

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorAplikacje webassembly są zabezpieczone w taki sam sposób, jak aplikacje jednostronicowe (aplikacji jednostronicowych). Istnieje kilka podejścia do uwierzytelniania użytkowników w aplikacji jednostronicowych, ale najpopularniejsze i kompleksowe podejście polega na użyciu implementacji opartej na [protokole OAuth 2,0](https://oauth.net/), takim jak [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteka uwierzytelniania

BlazorZestaw webassembly obsługuje uwierzytelnianie i Autoryzowanie aplikacji przy użyciu programu OIDC `Microsoft.AspNetCore.Components.WebAssembly.Authentication` za pośrednictwem biblioteki. Biblioteka zawiera zestaw elementów pierwotnych do bezproblemowego uwierzytelniania w odniesieniu do ASP.NET Core. Biblioteka integruje tożsamość ASP.NET Core z obsługą autoryzacji interfejsu API wbudowaną na [serwerze tożsamości](https://identityserver.io/). Biblioteka może być uwierzytelniana względem dowolnego innego dostawcy tożsamości (IP), który obsługuje OIDC, które są nazywane dostawcami OpenID Connect (OP).

Obsługa uwierzytelniania w Blazor zestawie webassembly jest oparta na bibliotece *OIDC-Client. js* , która jest używana do obsługi szczegółowych informacji o podstawowym protokole uwierzytelniania.

Dostępne są inne opcje uwierzytelniania aplikacji jednostronicowych, takie jak używanie plików cookie SameSite. Jednak projekt Blazor inżynieryjny zestawu webassembly jest rozliczany przy użyciu protokołu OAuth i OIDC jako najlepszą opcję uwierzytelniania w Blazor aplikacjach webassembly. [Uwierzytelnianie oparte na tokenach](xref:security/anti-request-forgery#token-based-authentication) oparte na [tokenach sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) zostało wybrane w ramach [uwierzytelniania opartego na plikach cookie](xref:security/anti-request-forgery#cookie-based-authentication) dla powodów funkcjonalnych i zabezpieczeń:

* Użycie protokołu opartego na tokenach oferuje mniejszy obszar obszaru ataków, ponieważ tokeny nie są wysyłane we wszystkich żądaniach.
* Punkty końcowe serwera nie wymagają ochrony przed [fałszerstwem żądania między lokacjami (CSRF)](xref:security/anti-request-forgery) , ponieważ tokeny są wysyłane jawnie. Dzięki temu można hostować Blazor aplikacje webassembly w aplikacjach MVC lub Razor.
* Tokeny mają węższe uprawnienia niż pliki cookie. Na przykład tokeny nie mogą być używane do zarządzania kontem użytkownika ani zmieniać hasła użytkownika, chyba że te funkcje są jawnie zaimplementowane.
* Tokeny mają krótki okres istnienia, domyślnie jedną godzinę, która ogranicza okno ataku. Tokeny można również odwołać w dowolnym momencie.
* Samodzielna oferta JWTs gwarantuje klientowi i serwerowi proces uwierzytelniania. Na przykład klient ma środki do wykrycia i sprawdzenia, czy otrzymane tokeny są wiarygodne i są emitowane w ramach danego procesu uwierzytelniania. Jeśli osoba trzecia podejmie próbę przełączenia tokenu w trakcie procesu uwierzytelniania, klient może wykryć przełączony token i uniknąć korzystania z niego.
* Tokeny z uwierzytelnianiem OAuth i OIDC nie polegają na tym, że agent użytkownika działa prawidłowo, aby upewnić się, że aplikacja jest bezpieczna.
* Protokoły oparte na tokenach, takie jak OAuth i OIDC, umożliwiają uwierzytelnianie i Autoryzowanie aplikacji hostowanych i autonomicznych z tym samym zestawem charakterystyk zabezpieczeń.

## <a name="authentication-process-with-oidc"></a>Proces uwierzytelniania za pomocą OIDC

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` Biblioteka zawiera kilka elementów podstawowych, które implementują uwierzytelnianie i autoryzację przy użyciu OIDC. Uwierzytelnianie odbywa się w następujący sposób:

* Gdy użytkownik anonimowy wybierze przycisk Zaloguj lub zażąda strony z zastosowanym [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem, użytkownik zostanie przekierowany do strony logowania aplikacji (`/authentication/login`).
* Na stronie Logowanie Biblioteka uwierzytelniania przygotowuje przekierowanie do punktu końcowego autoryzacji. Punkt końcowy autoryzacji jest poza Blazor aplikacją webassembly i może być hostowany w osobnym miejscu pochodzenia. Punkt końcowy jest odpowiedzialny za określenie, czy użytkownik jest uwierzytelniany i w celu wystawiania co najmniej jednego tokenu w odpowiedzi. Biblioteka uwierzytelniania umożliwia wywołanie zwrotne logowania w celu uzyskania odpowiedzi uwierzytelniania.
  * Jeśli użytkownik nie jest uwierzytelniony, użytkownik zostanie przekierowany do podstawowego systemu uwierzytelniania, który jest zwykle ASP.NET Core tożsamości.
  * Jeśli użytkownik został już uwierzytelniony, punkt końcowy autoryzacji generuje odpowiednie tokeny i przekierowuje przeglądarkę z powrotem do punktu końcowego wywołania zwrotnego logowania (`/authentication/login-callback`).
* Blazor Gdy aplikacja webassembly ładuje punkt końcowy wywołania zwrotnego logowania`/authentication/login-callback`(), odpowiedź uwierzytelniania jest przetwarzana.
  * Jeśli proces uwierzytelniania zakończy się pomyślnie, użytkownik zostanie uwierzytelniony i opcjonalnie zostanie wysłany z powrotem do oryginalnego chronionego adresu URL, którego zażądał użytkownik.
  * Jeśli z jakiegoś powodu proces uwierzytelniania zakończy się niepowodzeniem, użytkownik zostanie wysłany do strony logowania zakończonej niepowodzeniem (`/authentication/login-failed`) i zostanie wyświetlony komunikat o błędzie.

## <a name="additional-resources"></a>Dodatkowe zasoby

* Artykuły w ramach tego *omówienia* zawierają informacje dotyczące uwierzytelniania użytkowników w Blazor aplikacjach zestawu webassembly dla określonych dostawców.
* <xref:security/blazor/webassembly/additional-scenarios>
