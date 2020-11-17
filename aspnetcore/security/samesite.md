---
title: Pracuj z SameSite cookie s w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać programu do SameSite cookie s w ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
- Electron
uid: security/samesite
ms.openlocfilehash: f1aa388015bd540a6fda263eac53753ada63bf79
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673994"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="efbad-103">Pracuj z SameSite cookie s w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efbad-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="efbad-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="efbad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="efbad-105">SameSite jest projektem standardowym [IETF](https://ietf.org/about/) opracowanym w celu zapewnienia pewnej ochrony przed atakami polegającymi na translokacjach (CSRF).</span><span class="sxs-lookup"><span data-stu-id="efbad-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="efbad-106">Pierwotnie Sporządzono w [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), projekt Standard został zaktualizowany w [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="efbad-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="efbad-107">Zaktualizowany standard nie jest zgodny z poprzednią wersją standardową, co poniżej przedstawiono najbardziej zauważalne różnice:</span><span class="sxs-lookup"><span data-stu-id="efbad-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="efbad-108">CookienieSameSite nagłówek nie jest domyślnie traktowany `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="efbad-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="efbad-109">`SameSite=None` musi służyć do zezwalania na użycie między lokacjami cookie .</span><span class="sxs-lookup"><span data-stu-id="efbad-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="efbad-110">Cookiewszystkie potwierdzenia `SameSite=None` muszą być również oznaczone jako `Secure` .</span><span class="sxs-lookup"><span data-stu-id="efbad-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="efbad-111">Aplikacje, które używają [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) programu, mogą napotkać problemy z `sameSite=Lax` lub s, `sameSite=Strict` cookie ponieważ `<iframe>` są traktowane jako scenariusze między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="efbad-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="efbad-112">Wartość `SameSite=None` nie jest dozwolona przez [Standard 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) i powoduje, że niektóre implementacje traktują takie klasy cookie jako `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="efbad-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="efbad-113">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="efbad-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="efbad-114">To `SameSite=Lax` ustawienie działa w przypadku większości aplikacji cookie .</span><span class="sxs-lookup"><span data-stu-id="efbad-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="efbad-115">Niektóre formy uwierzytelniania, takie jak [OpenID Connect Connect](https://openid.net/connect/) (OIDC) i [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , domyślnie mogą publikować przekierowania na podstawie.</span><span class="sxs-lookup"><span data-stu-id="efbad-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="efbad-116">Przekierowania na podstawie wpisu wyzwalają ochronę za pomocą przeglądarki SameSite, więc SameSite jest wyłączona dla tych składników.</span><span class="sxs-lookup"><span data-stu-id="efbad-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="efbad-117">Nie ma to wpływ na większość nazw logowania [OAuth](https://oauth.net/) z powodu różnic w sposobie przepływu żądania.</span><span class="sxs-lookup"><span data-stu-id="efbad-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="efbad-118">Każdy składnik ASP.NET Core, który emituje cookie s, musi zdecydować, czy SameSite jest odpowiednie.</span><span class="sxs-lookup"><span data-stu-id="efbad-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="efbad-119">SameSite i Identity</span><span class="sxs-lookup"><span data-stu-id="efbad-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="efbad-120">Przykładowy kod testu SameSite</span><span class="sxs-lookup"><span data-stu-id="efbad-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="efbad-121">Następujące przykłady można pobrać i przetestować:</span><span class="sxs-lookup"><span data-stu-id="efbad-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="efbad-122">Przykład</span><span class="sxs-lookup"><span data-stu-id="efbad-122">Sample</span></span>               | <span data-ttu-id="efbad-123">Dokument</span><span class="sxs-lookup"><span data-stu-id="efbad-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="efbad-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="efbad-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="efbad-125">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="efbad-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="efbad-126">Można pobrać i przetestować następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="efbad-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="efbad-127">Przykład</span><span class="sxs-lookup"><span data-stu-id="efbad-127">Sample</span></span>               | <span data-ttu-id="efbad-128">Dokument</span><span class="sxs-lookup"><span data-stu-id="efbad-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="efbad-129">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="efbad-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="efbad-130">Obsługa platformy .NET Core dla atrybutu sameSite</span><span class="sxs-lookup"><span data-stu-id="efbad-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="efbad-131">Program .NET Core 2,2 i nowszy obsługują wersję 2019 wersji Standard dla SameSite od czasu wydania aktualizacji w grudniu 2019.</span><span class="sxs-lookup"><span data-stu-id="efbad-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="efbad-132">Deweloperzy mogą programowo kontrolować wartość atrybutu sameSite przy użyciu `HttpCookie.SameSite` właściwości.</span><span class="sxs-lookup"><span data-stu-id="efbad-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="efbad-133">Ustawienie `SameSite` właściwości na Strict, swobodny lub None powoduje, że te wartości są zapisywane w sieci przy użyciu cookie .</span><span class="sxs-lookup"><span data-stu-id="efbad-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="efbad-134">Ustawienie równe `(SameSiteMode)(-1)` wskazuje, że żaden atrybut sameSite nie powinien być uwzględniony w sieci z cookie</span><span class="sxs-lookup"><span data-stu-id="efbad-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="efbad-135">Platforma .NET Core 3,0 i nowsze obsługują zaktualizowane wartości SameSite i dodaje dodatkową wartość enum `SameSiteMode.Unspecified` do `SameSiteMode` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="efbad-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="efbad-136">Ta nowa wartość wskazuje, że żaden sameSite nie powinien być wysyłany z cookie .</span><span class="sxs-lookup"><span data-stu-id="efbad-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="efbad-137">Zmiany zachowania poprawek w grudniu</span><span class="sxs-lookup"><span data-stu-id="efbad-137">December patch behavior changes</span></span>

<span data-ttu-id="efbad-138">Określone zmiany zachowania dla .NET Framework i .NET Core 2,1 to sposób, w jaki `SameSite` Właściwość interpretuje `None` wartość.</span><span class="sxs-lookup"><span data-stu-id="efbad-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="efbad-139">Przed poprawką wartość "nie `None` Emituj atrybutu wcale", po zastosowaniu poprawki oznacza "Emituj atrybut wartością `None` ".</span><span class="sxs-lookup"><span data-stu-id="efbad-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="efbad-140">Po zastosowaniu poprawki `SameSite` wartość `(SameSiteMode)(-1)` powoduje, że atrybut nie jest emitowany.</span><span class="sxs-lookup"><span data-stu-id="efbad-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="efbad-141">Domyślna wartość SameSite dla uwierzytelniania formularzy i stanu sesji cookie s została zmieniona z `None` na `Lax` .</span><span class="sxs-lookup"><span data-stu-id="efbad-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="efbad-142">Użycie interfejsu API z SameSite</span><span class="sxs-lookup"><span data-stu-id="efbad-142">API usage with SameSite</span></span>

<span data-ttu-id="efbad-143">[HttpContext. Response. Cookie s. Dołącz](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) wartości domyślne do `Unspecified` , co oznacza, że żaden atrybut SameSite nie został dodany do cookie i klient będzie używać jego domyślnego zachowania (swobodny dla nowych przeglądarek, brak dla starych).</span><span class="sxs-lookup"><span data-stu-id="efbad-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="efbad-144">Poniższy kod pokazuje, jak zmienić cookie wartość SameSite na `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="efbad-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="efbad-145">Wszystkie składniki ASP.NET Core, które emitują cookie elementy s, zastępują poprzednie wartości domyślne przy użyciu ustawień odpowiednich dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="efbad-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="efbad-146">Zastąpione poprzednie wartości domyślne nie zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="efbad-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="efbad-147">Składnik</span><span class="sxs-lookup"><span data-stu-id="efbad-147">Component</span></span> | cookie | <span data-ttu-id="efbad-148">Domyślne</span><span class="sxs-lookup"><span data-stu-id="efbad-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="efbad-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="efbad-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="efbad-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="efbad-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="efbad-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="efbad-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="efbad-152">[Cookie Ponowne](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="efbad-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="efbad-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="efbad-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="efbad-154">[TwitterOptions. State Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="efbad-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="efbad-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. KorelacjaCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="efbad-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="efbad-156">[OpenIdConnectOptions. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="efbad-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="efbad-157">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="efbad-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="efbad-158">ASP.NET Core 3,1 i nowsze oferują następujące wsparcie SameSite:</span><span class="sxs-lookup"><span data-stu-id="efbad-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="efbad-159">Ponownie definiuje zachowanie programu `SameSiteMode.None` do emisji `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="efbad-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="efbad-160">Dodaje nową wartość, `SameSiteMode.Unspecified` Aby pominąć atrybut SameSite.</span><span class="sxs-lookup"><span data-stu-id="efbad-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="efbad-161">Wszystkie cookie interfejsy API s domyślnie `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="efbad-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="efbad-162">Niektóre składniki, które używają wartości, są cookie ustawiane bardziej specyficzne dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="efbad-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="efbad-163">Przykłady można znaleźć w powyższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="efbad-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="efbad-164">W ASP.NET Core 3,0 i nowszych wartości domyślnych SameSite zostały zmienione, aby uniknąć konfliktu z niespójnymi wartościami domyślnymi klienta.</span><span class="sxs-lookup"><span data-stu-id="efbad-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="efbad-165">Następujące interfejsy API zmieniły się domyślnie z `SameSiteMode.Lax ` na, aby `-1` uniknąć emitowania atrybutu SameSite dla tych cookie s:</span><span class="sxs-lookup"><span data-stu-id="efbad-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="efbad-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> używany z obiektem [HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="efbad-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="efbad-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  używany jako fabryka dla `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="efbad-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="efbad-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="efbad-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="efbad-169">Historia i zmiany</span><span class="sxs-lookup"><span data-stu-id="efbad-169">History and changes</span></span>

<span data-ttu-id="efbad-170">Obsługa SameSite została najpierw zaimplementowana w ASP.NET Core w 2,0 przy użyciu [standardowego standardu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="efbad-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="efbad-171">Standard 2016 został zadecydować.</span><span class="sxs-lookup"><span data-stu-id="efbad-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="efbad-172">ASP.NET Core domyślnie, ustawiając kilka cookie s do `Lax` domyślnie.</span><span class="sxs-lookup"><span data-stu-id="efbad-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="efbad-173">Po napotkaniu kilku [problemów](https://github.com/aspnet/Announcements/issues/318) z uwierzytelnianiem większość SameSite zostało [wyłączone](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="efbad-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="efbad-174">[Poprawki](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zostały wydane w listopadzie 2019, aby zaktualizować ze standardu 2016 do normy 2019.</span><span class="sxs-lookup"><span data-stu-id="efbad-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="efbad-175">[Wersja robocza 2019 specyfikacji SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="efbad-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="efbad-176">**Nie** jest wstecznie zgodne z wersją roboczą 2016.</span><span class="sxs-lookup"><span data-stu-id="efbad-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="efbad-177">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="efbad-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="efbad-178">Określenie cookie s jest traktowane jako `SameSite=Lax` domyślne.</span><span class="sxs-lookup"><span data-stu-id="efbad-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="efbad-179">Określa cookie , że jawne potwierdzenie `SameSite=None` w celu włączenia dostarczania między lokacjami powinno być oznaczone jako `Secure` .</span><span class="sxs-lookup"><span data-stu-id="efbad-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="efbad-180">`None` jest nowym wpisem, aby zrezygnować z tej opcji.</span><span class="sxs-lookup"><span data-stu-id="efbad-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="efbad-181">Jest obsługiwane przez poprawki wydane dla ASP.NET Core 2,1, 2,2 i 3,0.</span><span class="sxs-lookup"><span data-stu-id="efbad-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="efbad-182">ASP.NET Core 3,1 oferuje dodatkową pomoc techniczną SameSite.</span><span class="sxs-lookup"><span data-stu-id="efbad-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="efbad-183">Zaplanowano włączenie programu [Chrome](https://chromestatus.com/feature/5088147346030592) domyślnie w [lutym 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="efbad-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="efbad-184">Przeglądarki zaczynają przechodzenie do tego standardu w 2019.</span><span class="sxs-lookup"><span data-stu-id="efbad-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="efbad-185">Interfejsy API, na które wpływem zmiany z standardu 2016 SameSite Draft do wersji Standard 2019</span><span class="sxs-lookup"><span data-stu-id="efbad-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="efbad-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="efbad-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="efbad-187">[CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="efbad-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="efbad-188">[CookieKonstruktor. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="efbad-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="efbad-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="efbad-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="efbad-190">Obsługa starszych przeglądarek</span><span class="sxs-lookup"><span data-stu-id="efbad-190">Supporting older browsers</span></span>

<span data-ttu-id="efbad-191">Standard 2016 SameSite, że nieznane wartości muszą być traktowane jako `SameSite=Strict` wartości.</span><span class="sxs-lookup"><span data-stu-id="efbad-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="efbad-192">Aplikacje dostępne ze starszych przeglądarek, które obsługują Standard 2016 SameSite, mogą ulec przerwie, gdy pobierają Właściwość SameSite o wartości `None` .</span><span class="sxs-lookup"><span data-stu-id="efbad-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="efbad-193">Aplikacje sieci Web muszą implementować wykrywanie przeglądarki, jeśli chcą obsługiwać starsze przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="efbad-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="efbad-194">ASP.NET Core nie implementuje wykrywania przeglądarki, ponieważ wartości User-Agents są bardzo trwałe i często zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="efbad-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="efbad-195">Punkt rozszerzenia w programie <xref:Microsoft.AspNetCore.CookiePolicy> umożliwia podłączanie w User-Agent określonej logiki.</span><span class="sxs-lookup"><span data-stu-id="efbad-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="efbad-196">W programie `Startup.Configure` Dodaj kod, który wywołuje <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> przed wywołaniem <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metody lub *dowolną* metodę, która zapisuje cookie :</span><span class="sxs-lookup"><span data-stu-id="efbad-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="efbad-197">W programie `Startup.ConfigureServices` Dodaj kod podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="efbad-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="efbad-198">W powyższym przykładzie `MyUserAgentDetectionLib.DisallowsSameSiteNone` jest biblioteką dostarczoną przez użytkownika, która wykrywa, czy agent użytkownika nie obsługuje SameSite `None` :</span><span class="sxs-lookup"><span data-stu-id="efbad-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="efbad-199">Poniższy kod przedstawia przykładową `DisallowsSameSiteNone` metodę:</span><span class="sxs-lookup"><span data-stu-id="efbad-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="efbad-200">Poniższy kod służy tylko do celów demonstracyjnych:</span><span class="sxs-lookup"><span data-stu-id="efbad-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="efbad-201">Nie powinna być uważana za ukończoną.</span><span class="sxs-lookup"><span data-stu-id="efbad-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="efbad-202">Nie jest on obsługiwany ani wspierany.</span><span class="sxs-lookup"><span data-stu-id="efbad-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="efbad-203">Testowanie aplikacji pod kątem problemów z SameSite</span><span class="sxs-lookup"><span data-stu-id="efbad-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="efbad-204">Aplikacje, które współdziałają z witrynami zdalnymi, takie jak logowanie innych firm, muszą:</span><span class="sxs-lookup"><span data-stu-id="efbad-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="efbad-205">Przetestuj interakcję w wielu przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="efbad-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="efbad-206">Zastosuj [ Cookie wykrywanie i rozwiązywanie problemów przeglądarki zasad](#sob) omówione w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="efbad-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="efbad-207">Przetestuj aplikacje sieci Web przy użyciu wersji klienta, która może być zgodą na nowe zachowanie SameSite.</span><span class="sxs-lookup"><span data-stu-id="efbad-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="efbad-208">Przeglądarki Chrome, Firefox i chrom Edge mają nowe flagi funkcji opt, których można użyć do testowania.</span><span class="sxs-lookup"><span data-stu-id="efbad-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="efbad-209">Po zastosowaniu przez aplikację SameSite poprawek przetestuj ją ze starszymi wersjami klienta, szczególnie Safari.</span><span class="sxs-lookup"><span data-stu-id="efbad-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="efbad-210">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="efbad-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="efbad-211">Testowanie za pomocą przeglądarki Chrome</span><span class="sxs-lookup"><span data-stu-id="efbad-211">Test with Chrome</span></span>

<span data-ttu-id="efbad-212">Chrome 78 + daje mylące wyniki, ponieważ ma tymczasowe środki zaradcze.</span><span class="sxs-lookup"><span data-stu-id="efbad-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="efbad-213">Program Chrome 78 + tymczasowe środki zaradcze dopuszczają cookie niedawne dwa minuty.</span><span class="sxs-lookup"><span data-stu-id="efbad-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="efbad-214">Program Chrome 76 lub 77 z włączonymi odpowiednimi flagami testu zapewnia dokładniejsze wyniki.</span><span class="sxs-lookup"><span data-stu-id="efbad-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="efbad-215">Aby przetestować nowe zachowanie SameSite, przełącz `chrome://flags/#same-site-by-default-cookies` się na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="efbad-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="efbad-216">Starsze wersje programu Chrome (75 i poniżej) zostały zgłoszone w celu niepowodzenia z nowym `None` ustawieniem.</span><span class="sxs-lookup"><span data-stu-id="efbad-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="efbad-217">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="efbad-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="efbad-218">Firma Google nie udostępnia starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="efbad-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="efbad-219">Postępuj zgodnie z instrukcjami podanymi w części [pobieranie chromu](https://www.chromium.org/getting-involved/download-chromium) , aby przetestować starsze wersje programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="efbad-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="efbad-220">**Nie** Pobieraj programu Chrome z linków dostarczonych przez wyszukiwanie starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="efbad-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="efbad-221">Chrom 76 Win64</span><span class="sxs-lookup"><span data-stu-id="efbad-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="efbad-222">Chrom 74 Win64</span><span class="sxs-lookup"><span data-stu-id="efbad-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="efbad-223">Począwszy od wersji Kanaryjskich `80.0.3975.0` , można wyłączyć swobodny + post tymczasowych środków zaradczych do celów testowych przy użyciu nowej flagi, `--enable-features=SameSiteDefaultChecksMethodRigorously` Aby umożliwić Testowanie witryn i usług w stanie końcowym funkcji, w której usunięto środki zaradcze.</span><span class="sxs-lookup"><span data-stu-id="efbad-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="efbad-224">Aby uzyskać więcej informacji, zobacz artykuł dotyczący projektów chrom [SameSite Updates](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="efbad-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="efbad-225">Testowanie przy użyciu przeglądarki Safari</span><span class="sxs-lookup"><span data-stu-id="efbad-225">Test with Safari</span></span>

<span data-ttu-id="efbad-226">Przeglądarka Safari 12 ściśle wdrożyła poprzednią wersję roboczą i kończy się niepowodzeniem, gdy nowa `None` wartość jest w cookie .</span><span class="sxs-lookup"><span data-stu-id="efbad-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="efbad-227">`None` jest to możliwe za pośrednictwem kodu wykrywania przeglądarki [obsługującego starsze przeglądarki](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="efbad-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="efbad-228">Przetestuj logowanie za pomocą przeglądarki Safari 12, Safari 13 i WebKit opartej na programie MSAL, ADAL lub niezależnie od używanej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="efbad-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="efbad-229">Problem jest zależny od podstawowej wersji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="efbad-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="efbad-230">OSX Mojave (10,14) i iOS 12 są znane jako problemy ze zgodnością z nowym zachowaniem SameSite.</span><span class="sxs-lookup"><span data-stu-id="efbad-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="efbad-231">Uaktualnienie systemu operacyjnego do wersji OSX Catalina (10,15) lub iOS 13 rozwiązuje problem.</span><span class="sxs-lookup"><span data-stu-id="efbad-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="efbad-232">Przeglądarka Safari nie ma obecnie flagi zgody na testowanie nowych zachowań specyfikacji.</span><span class="sxs-lookup"><span data-stu-id="efbad-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="efbad-233">Testowanie za pomocą przeglądarki Firefox</span><span class="sxs-lookup"><span data-stu-id="efbad-233">Test with Firefox</span></span>

<span data-ttu-id="efbad-234">Obsługę programu Firefox dla nowego standardu można przetestować w wersji 68 + przez wypróbowanie jej na `about:config` stronie z flagą funkcji `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="efbad-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="efbad-235">Nie zgłoszono problemów ze zgodnością ze starszymi wersjami programu Firefox.</span><span class="sxs-lookup"><span data-stu-id="efbad-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="efbad-236">Testowanie przy użyciu przeglądarki Edge</span><span class="sxs-lookup"><span data-stu-id="efbad-236">Test with Edge browser</span></span>

<span data-ttu-id="efbad-237">Program Edge obsługuje stary Standard SameSite.</span><span class="sxs-lookup"><span data-stu-id="efbad-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="efbad-238">Wersja brzegowa 44 nie ma żadnych znanych problemów ze zgodnością z nowym standardem.</span><span class="sxs-lookup"><span data-stu-id="efbad-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="efbad-239">Testowanie przy użyciu krawędzi (chrom)</span><span class="sxs-lookup"><span data-stu-id="efbad-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="efbad-240">Flagi SameSite są ustawiane na `edge://flags/#same-site-by-default-cookies` stronie.</span><span class="sxs-lookup"><span data-stu-id="efbad-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="efbad-241">Nie odnaleziono problemów ze zgodnością z funkcją chromu.</span><span class="sxs-lookup"><span data-stu-id="efbad-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="efbad-242">Testuj z Electron</span><span class="sxs-lookup"><span data-stu-id="efbad-242">Test with Electron</span></span>

<span data-ttu-id="efbad-243">Wersje programu Electron obejmują starsze wersje chromu.</span><span class="sxs-lookup"><span data-stu-id="efbad-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="efbad-244">Na przykład wersja Electron użyta przez zespoły to chrom 66, który wykazuje starsze zachowanie.</span><span class="sxs-lookup"><span data-stu-id="efbad-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="efbad-245">Należy przeprowadzić własne testy zgodności z Electron używaną wersją produktu.</span><span class="sxs-lookup"><span data-stu-id="efbad-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="efbad-246">Zapoznaj się z tematem [Obsługa starszych przeglądarek](#sob) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="efbad-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="efbad-247">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="efbad-247">Additional resources</span></span>

* [<span data-ttu-id="efbad-248">Blog chromu: deweloperzy: przygotowanie do nowego SameSite = none; Cookie Ustawienia zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="efbad-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="efbad-249">[SameSite cookie s](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="efbad-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="efbad-250">Poprawki 2019 listopada</span><span class="sxs-lookup"><span data-stu-id="efbad-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="efbad-251">Przykład</span><span class="sxs-lookup"><span data-stu-id="efbad-251">Sample</span></span>               | <span data-ttu-id="efbad-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="efbad-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="efbad-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="efbad-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="efbad-254">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="efbad-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="efbad-255">Przykład</span><span class="sxs-lookup"><span data-stu-id="efbad-255">Sample</span></span>               | <span data-ttu-id="efbad-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="efbad-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="efbad-257">[Strony .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="efbad-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
