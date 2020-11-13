---
title: Wymuś zasady zabezpieczeń zawartości dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak używać zasad zabezpieczeń zawartości (CSP) z Blazor aplikacjami ASP.NET Core, aby chronić przed atakami na skrypty krzyżowe (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570123"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a>Wymuś zasady zabezpieczeń zawartości dla ASP.NET Core Blazor

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

[Skrypt między lokacjami (XSS)](xref:security/cross-site-scripting) to luka w zabezpieczeniach, w której osoba atakująca umieszcza jeden lub więcej złośliwych skryptów po stronie klienta w renderowanej zawartości aplikacji. Zasady zabezpieczeń zawartości (CSP) pomagają chronić przed atakami typu XSS, informując przeglądarkę o prawidłowym:

* Źródła dla załadowanej zawartości, w tym skrypty, arkusze stylów i obrazy.
* Akcje podejmowane przez stronę, określając dozwolone docelowe adresy URL formularzy.
* Wtyczki, które mogą zostać załadowane.

Aby zastosować dostawcę CSP do aplikacji, deweloper określa kilka *dyrektyw* dotyczących zabezpieczeń zawartości dostawcy CSP w co najmniej jednym `Content-Security-Policy` nagłówku lub `<meta>` znaczniku.

Zasady są oceniane przez przeglądarkę podczas ładowania strony. Przeglądarka sprawdzi źródła strony i określi, czy spełniają one wymagania dyrektyw dotyczących zabezpieczeń zawartości. Gdy dyrektywy zasad nie są spełnione dla zasobu, przeglądarka nie załaduje zasobu. Rozważmy na przykład zasady, które nie zezwalają na wykonywanie skryptów innych firm. Gdy strona zawiera `<script>` tag z pochodzeniem innej firmy w `src` atrybucie, przeglądarka uniemożliwi ładowanie skryptu.

Dostawca CSP jest obsługiwany w większości nowoczesnych przeglądarek klasycznych i mobilnych, takich jak Chrome, Edge, Firefox, operac i Safari. Dostawca CSP jest zalecany w przypadku Blazor aplikacji.

## <a name="policy-directives"></a>Dyrektywy zasad

Określ w minimalny sposób następujące dyrektywy i źródła dla Blazor aplikacji. Dodaj dodatkowe dyrektywy i źródła zgodnie z wymaganiami. Poniższe dyrektywy są używane w sekcji [stosowanie zasad](#apply-the-policy) w tym artykule, w której znajdują się przykładowe zasady zabezpieczeń dla Blazor WebAssembly i Blazor Server :

* [Base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ogranicza adresy URL `<base>` tagu strony. Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.
* [Zablokuj wszystko-zawartość](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): zapobiega ładowaniu mieszanej zawartości http i https.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): wskazuje rezerwę dla dyrektyw źródłowych, które nie są jawnie określone przez zasady. Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): wskazuje prawidłowe źródła obrazów.
  * Określ `data:` , aby zezwolić na ładowanie obrazów z `data:` adresów URL.
  * Określ `https:` , aby zezwolić na ładowanie obrazów z punktów końcowych https.
* [obiekt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): wskazuje prawidłowe źródła dla `<object>` tagów, `<embed>` i `<applet>` . Określ `none` , aby uniemożliwić wszystkie źródła adresów URL.
* [skrypt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): wskazuje prawidłowe źródła dla skryptów.
  * Określ `https://stackpath.bootstrapcdn.com/` Źródło hosta dla skryptów Bootstrap.
  * Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.
  * W Blazor WebAssembly aplikacji:
    * Określ wartości skrótów, aby zezwolić na ładowanie wymaganych skryptów.
    * Określ `unsafe-eval` użycie `eval()` i metody tworzenia kodu z ciągów.
  * W Blazor Server aplikacji Określ wartości skrótów, aby zezwolić na ładowanie wymaganych skryptów.
* [styl-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): wskazuje prawidłowe źródła dla arkuszy stylów.
  * Określ `https://stackpath.bootstrapcdn.com/` Źródło hosta dla arkuszy stylów ładowania początkowego.
  * Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.
  * Określ `unsafe-inline` , aby zezwolić na używanie wbudowanych stylów. Deklaracja wbudowana jest wymagana dla interfejsu użytkownika w Blazor Server aplikacjach do ponownego połączenia klienta i serwera po początkowym żądaniu. W przyszłej wersji style wbudowane mogą zostać usunięte, aby `unsafe-inline` nie były już wymagane.
* [uaktualnienie-niezabezpieczone-żądania](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): wskazuje, że adresy URL zawartości z źródeł niezabezpieczonych (http) powinny zostać bezpiecznie pobrane za pośrednictwem protokołu HTTPS.

Powyższe dyrektywy są obsługiwane przez wszystkie przeglądarki z wyjątkiem programu Microsoft Internet Explorer.

Aby uzyskać skróty SHA dla dodatkowych skryptów wbudowanych:

* Zastosuj dostawcę CSP pokazany w sekcji [Zastosuj zasady](#apply-the-policy) .
* Uzyskaj dostęp do konsoli narzędzia deweloperskie w przeglądarce podczas lokalnego uruchamiania aplikacji. Przeglądarka oblicza i wyświetla skróty dla zablokowanych skryptów, gdy istnieje nagłówek lub tag dostawcy CSP `meta` .
* Skopiuj skróty udostępnione przez przeglądarkę do `script-src` źródeł. Używaj pojedynczych cudzysłowów wokół każdego skrótu.

Aby uzyskać informacje na temat obsługi macierzy dla poziomu zasad zabezpieczeń zawartości w przeglądarce, zobacz [: Czy można korzystać z poziomu zasad zabezpieczeń zawartości 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Zastosowanie zasad

Użyj `<meta>` znacznika, aby zastosować zasady:

* Ustaw wartość `http-equiv` atrybutu na `Content-Security-Policy` .
* Umieść dyrektywy w `content` wartości atrybutu. Oddziel dyrektywy średnikami ( `;` ).
* Zawsze umieszczaj `meta` tag w `<head>` zawartości.

W poniższych sekcjach przedstawiono przykładowe zasady dla Blazor WebAssembly i Blazor Server . Te przykłady dotyczą wersji tego artykułu dla każdej wersji programu Blazor . Aby użyć wersji odpowiedniej dla wersji, wybierz wersję dokumentu z selektorem listy rozwijanej **wersji** na tej stronie sieci Web.

### Blazor WebAssembly

W obszarze `<head>` zawartość `wwwroot/index.html` strony hosta Zastosuj dyrektywy opisane w sekcji [dyrektywy zasad](#policy-directives) :

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

Dodawanie dodatkowych `script-src` i `style-src` skrótów zgodnie z wymaganiami aplikacji. Podczas programowania Użyj narzędzia online lub narzędzi programistycznych przeglądarki, aby wyliczyć skróty. Na przykład następujący komunikat o błędzie konsoli narzędzia przeglądarki zgłasza skrót dla wymaganego skryptu nieobjętego zasadami:

> Odmówiono wykonania skryptu wbudowanego, ponieważ narusza on następującą dyrektywę zasad zabezpieczeń zawartości: "... ". Słowo kluczowe "UNSAFE-inline", skrót ("SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA =") lub identyfikator jednorazowy ("nonce-...") jest wymagany do włączenia wykonywania wbudowanego.

Konkretny skrypt skojarzony z tym błędem jest wyświetlany w konsoli obok błędu.

### Blazor Server

W obszarze `<head>` zawartość `Pages/_Host.cshtml` strony hosta Zastosuj dyrektywy opisane w sekcji [dyrektywy zasad](#policy-directives) :

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

Dodawanie dodatkowych `script-src` i `style-src` skrótów zgodnie z wymaganiami aplikacji. Podczas programowania Użyj narzędzia online lub narzędzi programistycznych przeglądarki, aby wyliczyć skróty. Na przykład następujący komunikat o błędzie konsoli narzędzia przeglądarki zgłasza skrót dla wymaganego skryptu nieobjętego zasadami:

> Odmówiono wykonania skryptu wbudowanego, ponieważ narusza on następującą dyrektywę zasad zabezpieczeń zawartości: "... ". Słowo kluczowe "UNSAFE-inline", skrót ("SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA =") lub identyfikator jednorazowy ("nonce-...") jest wymagany do włączenia wykonywania wbudowanego.

Konkretny skrypt skojarzony z tym błędem jest wyświetlany w konsoli obok błędu.

## <a name="meta-tag-limitations"></a>Ograniczenia tagów Meta

`<meta>`Zasady dotyczące tagów nie obsługują następujących dyrektyw:

* [Ramka — elementy nadrzędne](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [Zgłoś do](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [Raport — identyfikator URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [rozwiązania](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Aby zapewnić obsługę powyższych dyrektyw, użyj nagłówka o nazwie `Content-Security-Policy` . Ciąg dyrektywy jest wartością nagłówka.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testowanie zasad i otrzymywanie raportów o naruszeniu

Testowanie pomaga upewnić się, że skrypty innych firm nie są przypadkowo blokowane podczas kompilowania zasad początkowych.

Aby przetestować zasady w określonym czasie bez wymuszania dyrektyw zasad, ustaw `<meta>` `http-equiv` atrybut lub nazwę nagłówka dla zasad opartych na nagłówkach na `Content-Security-Policy-Report-Only` . Raporty o błędach są wysyłane jako dokumenty JSON do określonego adresu URL. Aby uzyskać więcej informacji, zobacz [powiadomienia MDN Web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Aby zgłaszać naruszenia zasad, gdy zasady są aktywne, zobacz następujące artykuły:

* [Zgłoś do](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [Raport — identyfikator URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Chociaż `report-uri` nie jest już zalecane do użycia, obie dyrektywy należy stosować do momentu, `report-to` w którym wszystkie główne przeglądarki są obsługiwane. Nie używaj wyłącznie `report-uri` , ponieważ obsługa programu `report-uri` podlega porzucaniu *w dowolnym momencie* w przeglądarkach. Usuń obsługę programu `report-uri` w ramach zasad, gdy `report-to` jest w pełni obsługiwany. Aby śledzić przyjęcie programu `report-to` , zobacz temat [czy można użyć: raport-do](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Testowanie i aktualizowanie zasad aplikacji w każdej wersji.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

* Błędy są wyświetlane w konsoli narzędzia deweloperskie w przeglądarce. Przeglądarki zawierają informacje o:
  * Elementy, które nie są zgodne z zasadami.
  * Jak zmodyfikować zasady, aby umożliwić zablokowanie elementu.
* Zasady są całkowicie skuteczne tylko wtedy, gdy przeglądarka klienta obsługuje wszystkie dyrektywy dołączone. Aby zapoznać się z bieżącą matrycą obsługi, zobacz temat [: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [POWIADOMIENIA MDN Web docs: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Poziom zabezpieczeń zawartości 2](https://www.w3.org/TR/CSP2/)
* [Ewaluatora usługi Google CSP](https://csp-evaluator.withgoogle.com/)
