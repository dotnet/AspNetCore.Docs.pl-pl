---
title: Pracuj z plikami cookie SameSite w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać programu do SameSite plików cookie w ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 3e3c12e17de3e12ead15c405e9339761a3f2f711
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944282"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="7cf4a-103">Pracuj z plikami cookie SameSite w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cf4a-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="7cf4a-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7cf4a-105">SameSite jest projektem standardowym [IETF](https://ietf.org/about/) opracowanym w celu zapewnienia pewnej ochrony przed atakami polegającymi na translokacjach (CSRF).</span><span class="sxs-lookup"><span data-stu-id="7cf4a-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="7cf4a-106">Pierwotnie Sporządzono w [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), projekt Standard został zaktualizowany w [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="7cf4a-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="7cf4a-107">Zaktualizowany standard nie jest zgodny z poprzednią wersją standardową, co poniżej przedstawiono najbardziej zauważalne różnice:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="7cf4a-108">Pliki cookie bez nagłówka SameSite są traktowane `SameSite=Lax` domyślnie jako domyślne.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="7cf4a-109">`SameSite=None`musi być używany do zezwalania na użycie plików cookie między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="7cf4a-110">Pliki cookie, które potwierdzają, `SameSite=None` muszą być również oznaczone jako `Secure` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="7cf4a-111">Aplikacje korzystające [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) z programu mogą napotkać problemy z `sameSite=Lax` `sameSite=Strict` plikami cookie, ponieważ `<iframe>` są traktowane jako scenariusze między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="7cf4a-112">Wartość `SameSite=None` nie jest dozwolona przez [Standard 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) i powoduje, że niektóre implementacje traktują takie pliki cookie jako `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="7cf4a-113">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="7cf4a-114">To `SameSite=Lax` ustawienie działa w przypadku większości plików cookie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="7cf4a-115">Niektóre formy uwierzytelniania, takie jak [OpenID Connect Connect](https://openid.net/connect/) (OIDC) i [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , domyślnie mogą publikować przekierowania na podstawie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="7cf4a-116">Przekierowania na podstawie wpisu wyzwalają ochronę za pomocą przeglądarki SameSite, więc SameSite jest wyłączona dla tych składników.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="7cf4a-117">Nie ma to wpływ na większość nazw logowania [OAuth](https://oauth.net/) z powodu różnic w sposobie przepływu żądania.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="7cf4a-118">Każdy składnik ASP.NET Core, który emituje pliki cookie, musi zdecydować, czy SameSite jest odpowiednie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-identity"></a><span data-ttu-id="7cf4a-119">SameSite iIdentity</span><span class="sxs-lookup"><span data-stu-id="7cf4a-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="7cf4a-120">Przykładowy kod testu SameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="7cf4a-121">Następujące przykłady można pobrać i przetestować:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="7cf4a-122">Przykład</span><span class="sxs-lookup"><span data-stu-id="7cf4a-122">Sample</span></span>               | <span data-ttu-id="7cf4a-123">Dokument</span><span class="sxs-lookup"><span data-stu-id="7cf4a-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="7cf4a-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7cf4a-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="7cf4a-125">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7cf4a-126">Można pobrać i przetestować następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="7cf4a-127">Przykład</span><span class="sxs-lookup"><span data-stu-id="7cf4a-127">Sample</span></span>               | <span data-ttu-id="7cf4a-128">Dokument</span><span class="sxs-lookup"><span data-stu-id="7cf4a-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="7cf4a-129">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="7cf4a-130">Obsługa platformy .NET Core dla atrybutu sameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="7cf4a-131">Program .NET Core 2,2 obsługuje wersję standardową 2019 dla SameSite od momentu wydania aktualizacji w grudniu 2019.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-131">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="7cf4a-132">Deweloperzy mogą programowo kontrolować wartość atrybutu sameSite przy użyciu `HttpCookie.SameSite` właściwości.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="7cf4a-133">Ustawienie `SameSite` właściwości na Strict, swobodny lub None powoduje, że te wartości są zapisywane w sieci przy użyciu pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="7cf4a-134">Ustawienie tej opcji równe (SameSiteMode) (-1) wskazuje, że żaden atrybut sameSite nie powinien być uwzględniony w sieci z plikiem cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-134">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="7cf4a-135">Program .NET Core 3,0 obsługuje zaktualizowane wartości SameSite i dodaje dodatkową wartość enum `SameSiteMode.Unspecified` do `SameSiteMode` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-135">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="7cf4a-136">Ta nowa wartość wskazuje, że żaden sameSite nie powinien być wysyłany z plikiem cookie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="7cf4a-137">Zmiany zachowania poprawek w grudniu</span><span class="sxs-lookup"><span data-stu-id="7cf4a-137">December patch behavior changes</span></span>

<span data-ttu-id="7cf4a-138">Określone zmiany zachowania dla .NET Framework i .NET Core 2,1 to sposób, w jaki `SameSite` Właściwość interpretuje `None` wartość.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="7cf4a-139">Przed poprawką wartość "nie `None` Emituj atrybutu wcale", po zastosowaniu poprawki oznacza "Emituj atrybut wartością `None` ".</span><span class="sxs-lookup"><span data-stu-id="7cf4a-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="7cf4a-140">Po zastosowaniu poprawki `SameSite` wartość `(SameSiteMode)(-1)` powoduje, że atrybut nie jest emitowany.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="7cf4a-141">Domyślna wartość SameSite dla plików cookie związanych z uwierzytelnianiem formularzy i stanem sesji została zmieniona z `None` na `Lax` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="7cf4a-142">Użycie interfejsu API z SameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-142">API usage with SameSite</span></span>

<span data-ttu-id="7cf4a-143">[HttpContext. Response. cookies. Dołącz](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) wartości domyślne do `Unspecified` , co oznacza, że żaden atrybut SameSite nie został dodany do pliku cookie i klient użyje jego domyślnego zachowania (swobodny dla nowych przeglądarek, brak dla starych).</span><span class="sxs-lookup"><span data-stu-id="7cf4a-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="7cf4a-144">Poniższy kod przedstawia sposób zmiany wartości SameSite w pliku cookie na `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="7cf4a-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7cf4a-145">Wszystkie składniki ASP.NET Core, które emitują pliki cookie, zastępują poprzednie wartości domyślne przy użyciu ustawień odpowiednich dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="7cf4a-146">Zastąpione poprzednie wartości domyślne nie zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="7cf4a-147">Składnik</span><span class="sxs-lookup"><span data-stu-id="7cf4a-147">Component</span></span> | <span data-ttu-id="7cf4a-148">plików</span><span class="sxs-lookup"><span data-stu-id="7cf4a-148">cookie</span></span> | <span data-ttu-id="7cf4a-149">Domyślny</span><span class="sxs-lookup"><span data-stu-id="7cf4a-149">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="7cf4a-150">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-150">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="7cf4a-151">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-151">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="7cf4a-152">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-152">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="7cf4a-153">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-153">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="7cf4a-154">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-154">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="7cf4a-155">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-155">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="7cf4a-156"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="7cf4a-156"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="7cf4a-157">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-157">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="7cf4a-158">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="7cf4a-158">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="7cf4a-159">ASP.NET Core 3,1 i nowsze oferują następujące wsparcie SameSite:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-159">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="7cf4a-160">Ponownie definiuje zachowanie programu `SameSiteMode.None` do emisji`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="7cf4a-160">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="7cf4a-161">Dodaje nową wartość, `SameSiteMode.Unspecified` Aby pominąć atrybut SameSite.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-161">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="7cf4a-162">Wszystkie interfejsy API plików cookie domyślnie `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-162">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="7cf4a-163">Niektóre składniki używające plików cookie ustawiają wartości bardziej specyficzne dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-163">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="7cf4a-164">Przykłady można znaleźć w powyższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-164">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7cf4a-165">W ASP.NET Core 3,0 i nowszych wartości domyślnych SameSite zostały zmienione, aby uniknąć konfliktu z niespójnymi wartościami domyślnymi klienta.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-165">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="7cf4a-166">Następujące interfejsy API zmieniły się domyślnie z `SameSiteMode.Lax ` na, aby `-1` uniknąć emitowania atrybutu SameSite dla tych plików cookie:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-166">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="7cf4a-167"><xref:Microsoft.AspNetCore.Http.CookieOptions>używany z obiektem [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-167"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="7cf4a-168"><xref:Microsoft.AspNetCore.Http.CookieBuilder>używany jako fabryka dla`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="7cf4a-168"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="7cf4a-169">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="7cf4a-169">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="7cf4a-170">Historia i zmiany</span><span class="sxs-lookup"><span data-stu-id="7cf4a-170">History and changes</span></span>

<span data-ttu-id="7cf4a-171">Obsługa SameSite została najpierw zaimplementowana w ASP.NET Core w 2,0 przy użyciu [standardowego standardu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="7cf4a-171">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="7cf4a-172">Standard 2016 został zadecydować.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-172">The 2016 standard was opt-in.</span></span> <span data-ttu-id="7cf4a-173">ASP.NET Core wybierz opcję, domyślnie ustawiając kilka plików cookie `Lax` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-173">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="7cf4a-174">Po napotkaniu kilku [problemów](https://github.com/aspnet/Announcements/issues/318) z uwierzytelnianiem większość SameSite zostało [wyłączone](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="7cf4a-174">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="7cf4a-175">[Poprawki](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zostały wydane w listopadzie 2019, aby zaktualizować ze standardu 2016 do normy 2019.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-175">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="7cf4a-176">[Wersja robocza 2019 specyfikacji SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="7cf4a-176">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="7cf4a-177">**Nie** jest wstecznie zgodne z wersją roboczą 2016.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-177">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="7cf4a-178">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-178">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="7cf4a-179">Określa, że pliki cookie są traktowane `SameSite=Lax` domyślnie jako domyślne.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-179">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="7cf4a-180">Określa pliki cookie jawnie potwierdzone `SameSite=None` w celu włączenia dostarczania między lokacjami, które powinny być oznaczone jako `Secure` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-180">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="7cf4a-181">`None`jest nowym wpisem, aby zrezygnować z tej opcji.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-181">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="7cf4a-182">Jest obsługiwane przez poprawki wydane dla ASP.NET Core 2,1, 2,2 i 3,0.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-182">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="7cf4a-183">ASP.NET Core 3,1 oferuje dodatkową pomoc techniczną SameSite.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-183">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="7cf4a-184">Zaplanowano włączenie programu [Chrome](https://chromestatus.com/feature/5088147346030592) domyślnie w [lutym 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="7cf4a-184">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="7cf4a-185">Przeglądarki zaczynają przechodzenie do tego standardu w 2019.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-185">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="7cf4a-186">Interfejsy API, na które wpływem zmiany z standardu 2016 SameSite Draft do wersji Standard 2019</span><span class="sxs-lookup"><span data-stu-id="7cf4a-186">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="7cf4a-187">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="7cf4a-187">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="7cf4a-188">CookieOptions.SameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-188">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="7cf4a-189">CookieBuilder.SameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-189">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="7cf4a-190">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="7cf4a-190">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="7cf4a-191">Obsługa starszych przeglądarek</span><span class="sxs-lookup"><span data-stu-id="7cf4a-191">Supporting older browsers</span></span>

<span data-ttu-id="7cf4a-192">Standard 2016 SameSite, że nieznane wartości muszą być traktowane jako `SameSite=Strict` wartości.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-192">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="7cf4a-193">Aplikacje dostępne ze starszych przeglądarek, które obsługują Standard 2016 SameSite, mogą ulec przerwie, gdy pobierają Właściwość SameSite o wartości `None` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-193">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="7cf4a-194">Aplikacje sieci Web muszą implementować wykrywanie przeglądarki, jeśli chcą obsługiwać starsze przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-194">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="7cf4a-195">ASP.NET Core nie implementuje wykrywania przeglądarki, ponieważ wartości agentów użytkownika są bardzo nietrwałe i często zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-195">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="7cf4a-196">Punkt rozszerzenia w programie <xref:Microsoft.AspNetCore.CookiePolicy> umożliwia podłączanie w ramach logiki specyficznej dla agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-196">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="7cf4a-197">W programie `Startup.Configure` Dodaj kod, który wywołuje <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> przed wywołaniem <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metody lub *dowolną* metodę, która zapisuje pliki cookie:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-197">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="7cf4a-198">W programie `Startup.ConfigureServices` Dodaj kod podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-198">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="7cf4a-199">W powyższym przykładzie `MyUserAgentDetectionLib.DisallowsSameSiteNone` jest biblioteką dostarczoną przez użytkownika, która wykrywa, czy agent użytkownika nie obsługuje SameSite `None` :</span><span class="sxs-lookup"><span data-stu-id="7cf4a-199">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="7cf4a-200">Poniższy kod przedstawia przykładową `DisallowsSameSiteNone` metodę:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-200">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="7cf4a-201">Poniższy kod służy tylko do celów demonstracyjnych:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-201">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="7cf4a-202">Nie powinna być uważana za ukończoną.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-202">It should not be considered complete.</span></span>
> * <span data-ttu-id="7cf4a-203">Nie jest on obsługiwany ani wspierany.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-203">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="7cf4a-204">Testowanie aplikacji pod kątem problemów z SameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-204">Test apps for SameSite problems</span></span>

<span data-ttu-id="7cf4a-205">Aplikacje, które współdziałają z witrynami zdalnymi, takie jak logowanie innych firm, muszą:</span><span class="sxs-lookup"><span data-stu-id="7cf4a-205">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="7cf4a-206">Przetestuj interakcję w wielu przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-206">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="7cf4a-207">Zastosuj [wykrywanie i środki zaradcze w przeglądarce CookiePolicy](#sob) omówione w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-207">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="7cf4a-208">Przetestuj aplikacje sieci Web przy użyciu wersji klienta, która może być zgodą na nowe zachowanie SameSite.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-208">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="7cf4a-209">Przeglądarki Chrome, Firefox i chrom Edge mają nowe flagi funkcji opt, których można użyć do testowania.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-209">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="7cf4a-210">Po zastosowaniu przez aplikację SameSite poprawek przetestuj ją ze starszymi wersjami klienta, szczególnie Safari.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-210">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="7cf4a-211">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-211">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="7cf4a-212">Testowanie za pomocą przeglądarki Chrome</span><span class="sxs-lookup"><span data-stu-id="7cf4a-212">Test with Chrome</span></span>

<span data-ttu-id="7cf4a-213">Chrome 78 + daje mylące wyniki, ponieważ ma tymczasowe środki zaradcze.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-213">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="7cf4a-214">Chrom 78 + tymczasowe środki zaradcze dopuszczają pliki cookie mniejsze niż dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-214">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="7cf4a-215">Program Chrome 76 lub 77 z włączonymi odpowiednimi flagami testu zapewnia dokładniejsze wyniki.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-215">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="7cf4a-216">Aby przetestować nowe zachowanie SameSite, przełącz `chrome://flags/#same-site-by-default-cookies` się na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-216">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="7cf4a-217">Starsze wersje programu Chrome (75 i poniżej) zostały zgłoszone w celu niepowodzenia z nowym `None` ustawieniem.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-217">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="7cf4a-218">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-218">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="7cf4a-219">Firma Google nie udostępnia starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-219">Google does not make older chrome versions available.</span></span> <span data-ttu-id="7cf4a-220">Postępuj zgodnie z instrukcjami podanymi w części [pobieranie chromu](https://www.chromium.org/getting-involved/download-chromium) , aby przetestować starsze wersje programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-220">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="7cf4a-221">**Nie** Pobieraj programu Chrome z linków dostarczonych przez wyszukiwanie starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-221">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="7cf4a-222">Chrom 76 Win64</span><span class="sxs-lookup"><span data-stu-id="7cf4a-222">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="7cf4a-223">Chrom 74 Win64</span><span class="sxs-lookup"><span data-stu-id="7cf4a-223">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="7cf4a-224">Począwszy od wersji Kanaryjskich `80.0.3975.0` , można wyłączyć swobodny + post tymczasowych środków zaradczych do celów testowych przy użyciu nowej flagi, `--enable-features=SameSiteDefaultChecksMethodRigorously` Aby umożliwić Testowanie witryn i usług w stanie końcowym funkcji, w której usunięto środki zaradcze.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-224">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="7cf4a-225">Aby uzyskać więcej informacji, zobacz artykuł dotyczący projektów chrom [SameSite Updates](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-225">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="7cf4a-226">Testowanie przy użyciu przeglądarki Safari</span><span class="sxs-lookup"><span data-stu-id="7cf4a-226">Test with Safari</span></span>

<span data-ttu-id="7cf4a-227">Przeglądarka Safari 12 ściśle wdrożyła poprzednią wersję roboczą i kończy się niepowodzeniem, gdy nowa `None` wartość jest w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-227">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="7cf4a-228">`None`jest to możliwe za pośrednictwem kodu wykrywania przeglądarki [obsługującego starsze przeglądarki](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-228">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="7cf4a-229">Przetestuj logowanie za pomocą przeglądarki Safari 12, Safari 13 i WebKit opartej na programie MSAL, ADAL lub niezależnie od używanej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-229">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="7cf4a-230">Problem jest zależny od podstawowej wersji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-230">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="7cf4a-231">OSX Mojave (10,14) i iOS 12 są znane jako problemy ze zgodnością z nowym zachowaniem SameSite.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-231">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="7cf4a-232">Uaktualnienie systemu operacyjnego do wersji OSX Catalina (10,15) lub iOS 13 rozwiązuje problem.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-232">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="7cf4a-233">Przeglądarka Safari nie ma obecnie flagi zgody na testowanie nowych zachowań specyfikacji.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-233">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="7cf4a-234">Testowanie za pomocą przeglądarki Firefox</span><span class="sxs-lookup"><span data-stu-id="7cf4a-234">Test with Firefox</span></span>

<span data-ttu-id="7cf4a-235">Obsługę programu Firefox dla nowego standardu można przetestować w wersji 68 + przez wypróbowanie jej na `about:config` stronie z flagą funkcji `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="7cf4a-235">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="7cf4a-236">Nie zgłoszono problemów ze zgodnością ze starszymi wersjami programu Firefox.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-236">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="7cf4a-237">Testowanie przy użyciu przeglądarki Edge</span><span class="sxs-lookup"><span data-stu-id="7cf4a-237">Test with Edge browser</span></span>

<span data-ttu-id="7cf4a-238">Program Edge obsługuje stary Standard SameSite.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-238">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="7cf4a-239">Wersja brzegowa 44 nie ma żadnych znanych problemów ze zgodnością z nowym standardem.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-239">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="7cf4a-240">Testowanie przy użyciu krawędzi (chrom)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-240">Test with Edge (Chromium)</span></span>

<span data-ttu-id="7cf4a-241">Flagi SameSite są ustawiane na `edge://flags/#same-site-by-default-cookies` stronie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-241">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="7cf4a-242">Nie odnaleziono problemów ze zgodnością z funkcją chromu.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-242">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="7cf4a-243">Testuj zElectron</span><span class="sxs-lookup"><span data-stu-id="7cf4a-243">Test with Electron</span></span>

<span data-ttu-id="7cf4a-244">Wersje programu Electron obejmują starsze wersje chromu.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-244">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="7cf4a-245">Na przykład wersja Electron użyta przez zespoły to chrom 66, który wykazuje starsze zachowanie.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-245">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="7cf4a-246">Należy przeprowadzić własne testy zgodności z Electron używaną wersją produktu.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-246">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="7cf4a-247">Zapoznaj się z tematem [Obsługa starszych przeglądarek](#sob) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="7cf4a-247">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7cf4a-248">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7cf4a-248">Additional resources</span></span>

* [<span data-ttu-id="7cf4a-249">Blog chromu: deweloperzy: przygotowanie do nowego SameSite = none; Ustawienia bezpiecznego pliku cookie</span><span class="sxs-lookup"><span data-stu-id="7cf4a-249">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="7cf4a-250">Wyjaśniono pliki cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="7cf4a-250">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="7cf4a-251">Poprawki 2019 listopada</span><span class="sxs-lookup"><span data-stu-id="7cf4a-251">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="7cf4a-252">Przykład</span><span class="sxs-lookup"><span data-stu-id="7cf4a-252">Sample</span></span>               | <span data-ttu-id="7cf4a-253">Dokument</span><span class="sxs-lookup"><span data-stu-id="7cf4a-253">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="7cf4a-254">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7cf4a-254">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="7cf4a-255">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-255">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="7cf4a-256">Przykład</span><span class="sxs-lookup"><span data-stu-id="7cf4a-256">Sample</span></span>               | <span data-ttu-id="7cf4a-257">Dokument</span><span class="sxs-lookup"><span data-stu-id="7cf4a-257">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="7cf4a-258">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="7cf4a-258">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
