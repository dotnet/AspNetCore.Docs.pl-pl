---
title: Wymuś zasady zabezpieczeń zawartości dla ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak używać zasad zabezpieczeń zawartości (CSP) z Blazor aplikacjami ASP.NET Core, aby chronić przed atakami na skrypty krzyżowe (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 360fff9383e25a6b5b9308cfebd397f7f4ee31a6
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242982"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="32733-103">Wymuś zasady zabezpieczeń zawartości dla ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="32733-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="32733-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="32733-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="32733-105">[Skrypt między lokacjami (XSS)](xref:security/cross-site-scripting) to luka w zabezpieczeniach, w której osoba atakująca umieszcza jeden lub więcej złośliwych skryptów po stronie klienta w renderowanej zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32733-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="32733-106">Zasady zabezpieczeń zawartości (CSP) pomagają chronić przed atakami typu XSS, informując przeglądarkę o prawidłowym:</span><span class="sxs-lookup"><span data-stu-id="32733-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="32733-107">Źródła dla załadowanej zawartości, w tym skrypty, arkusze stylów i obrazy.</span><span class="sxs-lookup"><span data-stu-id="32733-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="32733-108">Akcje podejmowane przez stronę, określając dozwolone docelowe adresy URL formularzy.</span><span class="sxs-lookup"><span data-stu-id="32733-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="32733-109">Wtyczki, które mogą zostać załadowane.</span><span class="sxs-lookup"><span data-stu-id="32733-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="32733-110">Aby zastosować dostawcę CSP do aplikacji, deweloper określa kilka *dyrektyw* dotyczących zabezpieczeń zawartości dostawcy CSP w co najmniej jednym `Content-Security-Policy` nagłówku lub `<meta>` znaczniku.</span><span class="sxs-lookup"><span data-stu-id="32733-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="32733-111">Zasady są oceniane przez przeglądarkę podczas ładowania strony.</span><span class="sxs-lookup"><span data-stu-id="32733-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="32733-112">Przeglądarka sprawdzi źródła strony i określi, czy spełniają one wymagania dyrektyw dotyczących zabezpieczeń zawartości.</span><span class="sxs-lookup"><span data-stu-id="32733-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="32733-113">Gdy dyrektywy zasad nie są spełnione dla zasobu, przeglądarka nie załaduje zasobu.</span><span class="sxs-lookup"><span data-stu-id="32733-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="32733-114">Rozważmy na przykład zasady, które nie zezwalają na wykonywanie skryptów innych firm.</span><span class="sxs-lookup"><span data-stu-id="32733-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="32733-115">Gdy strona zawiera `<script>` tag z pochodzeniem innej firmy w `src` atrybucie, przeglądarka uniemożliwi ładowanie skryptu.</span><span class="sxs-lookup"><span data-stu-id="32733-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="32733-116">Dostawca CSP jest obsługiwany w większości nowoczesnych przeglądarek klasycznych i mobilnych, takich jak Chrome, Edge, Firefox, operac i Safari.</span><span class="sxs-lookup"><span data-stu-id="32733-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="32733-117">Dostawca CSP jest zalecany w przypadku Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32733-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="32733-118">Dyrektywy zasad</span><span class="sxs-lookup"><span data-stu-id="32733-118">Policy directives</span></span>

<span data-ttu-id="32733-119">Określ w minimalny sposób następujące dyrektywy i źródła dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32733-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="32733-120">Dodaj dodatkowe dyrektywy i źródła zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="32733-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="32733-121">Poniższe dyrektywy są używane w sekcji [stosowanie zasad](#apply-the-policy) w tym artykule, w której znajdują się przykładowe zasady zabezpieczeń dla Blazor zestawu webassembly i Blazor serwera:</span><span class="sxs-lookup"><span data-stu-id="32733-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="32733-122">[Base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ogranicza adresy URL `<base>` tagu strony.</span><span class="sxs-lookup"><span data-stu-id="32733-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="32733-123">Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.</span><span class="sxs-lookup"><span data-stu-id="32733-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="32733-124">[Zablokuj wszystko-zawartość](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): zapobiega ładowaniu mieszanej zawartości http i https.</span><span class="sxs-lookup"><span data-stu-id="32733-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="32733-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): wskazuje rezerwę dla dyrektyw źródłowych, które nie są jawnie określone przez zasady.</span><span class="sxs-lookup"><span data-stu-id="32733-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="32733-126">Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.</span><span class="sxs-lookup"><span data-stu-id="32733-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="32733-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): wskazuje prawidłowe źródła obrazów.</span><span class="sxs-lookup"><span data-stu-id="32733-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="32733-128">Określ `data:` , aby zezwolić na ładowanie obrazów z `data:` adresów URL.</span><span class="sxs-lookup"><span data-stu-id="32733-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="32733-129">Określ `https:` , aby zezwolić na ładowanie obrazów z punktów końcowych https.</span><span class="sxs-lookup"><span data-stu-id="32733-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="32733-130">[obiekt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): wskazuje prawidłowe źródła dla `<object>` tagów, `<embed>` i `<applet>` .</span><span class="sxs-lookup"><span data-stu-id="32733-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="32733-131">Określ `none` , aby uniemożliwić wszystkie źródła adresów URL.</span><span class="sxs-lookup"><span data-stu-id="32733-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="32733-132">[skrypt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): wskazuje prawidłowe źródła dla skryptów.</span><span class="sxs-lookup"><span data-stu-id="32733-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="32733-133">Określ `https://stackpath.bootstrapcdn.com/` Źródło hosta dla skryptów Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="32733-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="32733-134">Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.</span><span class="sxs-lookup"><span data-stu-id="32733-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="32733-135">W Blazor aplikacji webassembly:</span><span class="sxs-lookup"><span data-stu-id="32733-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="32733-136">Określ następujące skróty, aby zezwolić na ładowanie wymaganych Blazor skryptów wbudowanych webassembly:</span><span class="sxs-lookup"><span data-stu-id="32733-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="32733-137">Określ `unsafe-eval` użycie `eval()` i metody tworzenia kodu z ciągów.</span><span class="sxs-lookup"><span data-stu-id="32733-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="32733-138">W Blazor aplikacji serwera określ `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` skrót dla skryptu wbudowanego, który wykonuje wykrywanie powrotu dla arkuszy stylów.</span><span class="sxs-lookup"><span data-stu-id="32733-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="32733-139">[styl-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): wskazuje prawidłowe źródła dla arkuszy stylów.</span><span class="sxs-lookup"><span data-stu-id="32733-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="32733-140">Określ `https://stackpath.bootstrapcdn.com/` Źródło hosta dla arkuszy stylów ładowania początkowego.</span><span class="sxs-lookup"><span data-stu-id="32733-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="32733-141">Określ, `self` Aby wskazać, że pochodzenie aplikacji, w tym schemat i numer portu, jest prawidłowym źródłem.</span><span class="sxs-lookup"><span data-stu-id="32733-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="32733-142">Określ `unsafe-inline` , aby zezwolić na używanie wbudowanych stylów.</span><span class="sxs-lookup"><span data-stu-id="32733-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="32733-143">Deklaracja wbudowana jest wymagana dla interfejsu użytkownika w Blazor aplikacjach serwerowych do ponownego połączenia klienta i serwera po początkowym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="32733-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="32733-144">W przyszłej wersji style wbudowane mogą zostać usunięte, aby `unsafe-inline` nie były już wymagane.</span><span class="sxs-lookup"><span data-stu-id="32733-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="32733-145">[uaktualnienie-niezabezpieczone-żądania](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): wskazuje, że adresy URL zawartości z źródeł niezabezpieczonych (http) powinny zostać bezpiecznie pobrane za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="32733-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="32733-146">Powyższe dyrektywy są obsługiwane przez wszystkie przeglądarki z wyjątkiem programu Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="32733-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="32733-147">Aby uzyskać skróty SHA dla dodatkowych skryptów wbudowanych:</span><span class="sxs-lookup"><span data-stu-id="32733-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="32733-148">Zastosuj dostawcę CSP pokazany w sekcji [Zastosuj zasady](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="32733-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="32733-149">Uzyskaj dostęp do konsoli narzędzia deweloperskie w przeglądarce podczas lokalnego uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32733-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="32733-150">Przeglądarka oblicza i wyświetla skróty dla zablokowanych skryptów, gdy istnieje nagłówek lub tag dostawcy CSP `meta` .</span><span class="sxs-lookup"><span data-stu-id="32733-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="32733-151">Skopiuj skróty udostępnione przez przeglądarkę do `script-src` źródeł.</span><span class="sxs-lookup"><span data-stu-id="32733-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="32733-152">Używaj pojedynczych cudzysłowów wokół każdego skrótu.</span><span class="sxs-lookup"><span data-stu-id="32733-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="32733-153">Aby uzyskać informacje na temat obsługi macierzy dla poziomu zasad zabezpieczeń zawartości w przeglądarce, zobacz [: Czy można korzystać z poziomu zasad zabezpieczeń zawartości 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="32733-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="32733-154">Zastosowanie zasad</span><span class="sxs-lookup"><span data-stu-id="32733-154">Apply the policy</span></span>

<span data-ttu-id="32733-155">Użyj `<meta>` znacznika, aby zastosować zasady:</span><span class="sxs-lookup"><span data-stu-id="32733-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="32733-156">Ustaw wartość `http-equiv` atrybutu na `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="32733-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="32733-157">Umieść dyrektywy w `content` wartości atrybutu.</span><span class="sxs-lookup"><span data-stu-id="32733-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="32733-158">Oddziel dyrektywy średnikami ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="32733-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="32733-159">Zawsze umieszczaj `meta` tag w `<head>` zawartości.</span><span class="sxs-lookup"><span data-stu-id="32733-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="32733-160">W poniższych sekcjach przedstawiono przykładowe zasady dla Blazor zestawu webassembly i Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="32733-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="32733-161">Te przykłady dotyczą wersji tego artykułu dla każdej wersji programu Blazor .</span><span class="sxs-lookup"><span data-stu-id="32733-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="32733-162">Aby użyć wersji odpowiedniej dla wersji, wybierz wersję dokumentu z selektorem listy rozwijanej **wersji** na tej stronie sieci Web.</span><span class="sxs-lookup"><span data-stu-id="32733-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="32733-163">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="32733-163"> WebAssembly</span></span>

<span data-ttu-id="32733-164">W obszarze `<head>` zawartość `wwwroot/index.html` strony hosta Zastosuj dyrektywy opisane w sekcji [dyrektywy zasad](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="32733-164">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="32733-165">Server</span><span class="sxs-lookup"><span data-stu-id="32733-165"> Server</span></span>

<span data-ttu-id="32733-166">W obszarze `<head>` zawartość `Pages/_Host.cshtml` strony hosta Zastosuj dyrektywy opisane w sekcji [dyrektywy zasad](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="32733-166">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="32733-167">Ograniczenia tagów Meta</span><span class="sxs-lookup"><span data-stu-id="32733-167">Meta tag limitations</span></span>

<span data-ttu-id="32733-168">`<meta>`Zasady dotyczące tagów nie obsługują następujących dyrektyw:</span><span class="sxs-lookup"><span data-stu-id="32733-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="32733-169">Ramka — elementy nadrzędne</span><span class="sxs-lookup"><span data-stu-id="32733-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="32733-170">Zgłoś do</span><span class="sxs-lookup"><span data-stu-id="32733-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="32733-171">Raport — identyfikator URI</span><span class="sxs-lookup"><span data-stu-id="32733-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="32733-172">rozwiązania</span><span class="sxs-lookup"><span data-stu-id="32733-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="32733-173">Aby zapewnić obsługę powyższych dyrektyw, użyj nagłówka o nazwie `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="32733-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="32733-174">Ciąg dyrektywy jest wartością nagłówka.</span><span class="sxs-lookup"><span data-stu-id="32733-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="32733-175">Testowanie zasad i otrzymywanie raportów o naruszeniu</span><span class="sxs-lookup"><span data-stu-id="32733-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="32733-176">Testowanie pomaga upewnić się, że skrypty innych firm nie są przypadkowo blokowane podczas kompilowania zasad początkowych.</span><span class="sxs-lookup"><span data-stu-id="32733-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="32733-177">Aby przetestować zasady w określonym czasie bez wymuszania dyrektyw zasad, ustaw `<meta>` `http-equiv` atrybut lub nazwę nagłówka dla zasad opartych na nagłówkach na `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="32733-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="32733-178">Raporty o błędach są wysyłane jako dokumenty JSON do określonego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="32733-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="32733-179">Aby uzyskać więcej informacji, zobacz [powiadomienia MDN Web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="32733-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="32733-180">Aby zgłaszać naruszenia zasad, gdy zasady są aktywne, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="32733-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="32733-181">Zgłoś do</span><span class="sxs-lookup"><span data-stu-id="32733-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="32733-182">Raport — identyfikator URI</span><span class="sxs-lookup"><span data-stu-id="32733-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="32733-183">Chociaż `report-uri` nie jest już zalecane do użycia, obie dyrektywy należy stosować do momentu, `report-to` w którym wszystkie główne przeglądarki są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="32733-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="32733-184">Nie używaj wyłącznie `report-uri` , ponieważ obsługa programu `report-uri` podlega porzucaniu *w dowolnym momencie* w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="32733-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="32733-185">Usuń obsługę programu `report-uri` w ramach zasad, gdy `report-to` jest w pełni obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="32733-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="32733-186">Aby śledzić przyjęcie programu `report-to` , zobacz temat [czy można użyć: raport-do](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="32733-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="32733-187">Testowanie i aktualizowanie zasad aplikacji w każdej wersji.</span><span class="sxs-lookup"><span data-stu-id="32733-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="32733-188">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="32733-188">Troubleshoot</span></span>

* <span data-ttu-id="32733-189">Błędy są wyświetlane w konsoli narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="32733-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="32733-190">Przeglądarki zawierają informacje o:</span><span class="sxs-lookup"><span data-stu-id="32733-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="32733-191">Elementy, które nie są zgodne z zasadami.</span><span class="sxs-lookup"><span data-stu-id="32733-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="32733-192">Jak zmodyfikować zasady, aby umożliwić zablokowanie elementu.</span><span class="sxs-lookup"><span data-stu-id="32733-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="32733-193">Zasady są całkowicie skuteczne tylko wtedy, gdy przeglądarka klienta obsługuje wszystkie dyrektywy dołączone.</span><span class="sxs-lookup"><span data-stu-id="32733-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="32733-194">Aby zapoznać się z bieżącą matrycą obsługi, zobacz temat [: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="32733-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32733-195">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="32733-195">Additional resources</span></span>

* [<span data-ttu-id="32733-196">POWIADOMIENIA MDN Web docs: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="32733-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="32733-197">Poziom zabezpieczeń zawartości 2</span><span class="sxs-lookup"><span data-stu-id="32733-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="32733-198">Ewaluatora usługi Google CSP</span><span class="sxs-lookup"><span data-stu-id="32733-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
