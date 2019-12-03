---
title: SameSite
author: rick-anderson
description: Dowiedz się, jak używać programu do SameSite plików cookie w ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2019
uid: security/samesite
ms.openlocfilehash: 91c69c4caf0644c1f40750233175ddb4c02d7cfe
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717466"
---
# <a name="working-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="43e38-103">Praca z plikami cookie SameSite w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43e38-103">Working with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="43e38-104">Autor [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="43e38-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="43e38-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) jest projektem [IETF](https://ietf.org/about/) opracowanym w celu zapewnienia pewnej ochrony przed atakami na żądania bezlokacjowe (CSRF).</span><span class="sxs-lookup"><span data-stu-id="43e38-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) is an [IETF](https://ietf.org/about/) draft designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="43e38-106">[Wersja robocza SameSite 2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span><span class="sxs-lookup"><span data-stu-id="43e38-106">The [SameSite 2019 draft](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span></span>

* <span data-ttu-id="43e38-107">Domyślnie traktuje pliki cookie jako `SameSite=Lax`.</span><span class="sxs-lookup"><span data-stu-id="43e38-107">Treats cookies as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="43e38-108">Stany plików cookie, które jawnie potwierdzają `SameSite=None` w celu włączenia dostarczania między lokacjami, powinny być oznaczone jako `Secure`.</span><span class="sxs-lookup"><span data-stu-id="43e38-108">States cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span>

<span data-ttu-id="43e38-109">`Lax` działa w przypadku większości plików cookie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43e38-109">`Lax` works for most app cookies.</span></span> <span data-ttu-id="43e38-110">Niektóre formy uwierzytelniania, takie jak [OpenID Connect Connect](https://openid.net/connect/) (OIDC) i [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , domyślnie mogą publikować przekierowania na podstawie.</span><span class="sxs-lookup"><span data-stu-id="43e38-110">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="43e38-111">Przekierowania na podstawie wpisu wyzwalają ochronę za pomocą przeglądarki SameSite, więc SameSite jest wyłączona dla tych składników.</span><span class="sxs-lookup"><span data-stu-id="43e38-111">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="43e38-112">Nie ma to wpływ na większość nazw logowania [OAuth](https://oauth.net/) z powodu różnic w sposobie przepływu żądania.</span><span class="sxs-lookup"><span data-stu-id="43e38-112">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="43e38-113">`None` parametr powoduje problemy ze zgodnością z klientami, którzy zaimplementowali poprzednią wersję Standard 2016 (na przykład system iOS 12).</span><span class="sxs-lookup"><span data-stu-id="43e38-113">The `None` parameter causes compatibility problems with clients that implemented the prior 2016 draft standard (for example, iOS 12).</span></span> <span data-ttu-id="43e38-114">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="43e38-114">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="43e38-115">Każdy składnik ASP.NET Core, który emituje pliki cookie, musi zdecydować, czy SameSite jest odpowiednie.</span><span class="sxs-lookup"><span data-stu-id="43e38-115">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="api-usage-with-samesite"></a><span data-ttu-id="43e38-116">Użycie interfejsu API z SameSite</span><span class="sxs-lookup"><span data-stu-id="43e38-116">API usage with SameSite</span></span>

<span data-ttu-id="43e38-117">[HttpContext. Response. cookies. Dołącz](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) wartości domyślne do `Unspecified`, co oznacza, że żaden atrybut SameSite nie został dodany do pliku cookie, a klient użyje jego domyślnego zachowania (swobodny dla nowych przeglądarek, brak dla starych).</span><span class="sxs-lookup"><span data-stu-id="43e38-117">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="43e38-118">Poniższy kod przedstawia sposób zmiany wartości SameSite w pliku cookie na `SameSiteMode.Lax`:</span><span class="sxs-lookup"><span data-stu-id="43e38-118">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="43e38-119">Wszystkie składniki ASP.NET Core, które emitują pliki cookie, zastępują poprzednie wartości domyślne przy użyciu ustawień odpowiednich dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="43e38-119">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="43e38-120">Zastąpione poprzednie wartości domyślne nie zostały zmienione.</span><span class="sxs-lookup"><span data-stu-id="43e38-120">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="43e38-121">Składnik</span><span class="sxs-lookup"><span data-stu-id="43e38-121">Component</span></span> | <span data-ttu-id="43e38-122">plików</span><span class="sxs-lookup"><span data-stu-id="43e38-122">cookie</span></span> | <span data-ttu-id="43e38-123">Domyślny</span><span class="sxs-lookup"><span data-stu-id="43e38-123">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="43e38-124">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="43e38-124">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="43e38-125">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="43e38-125">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="43e38-126">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="43e38-126">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="43e38-127">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="43e38-127">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="43e38-128">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="43e38-128">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="43e38-129">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="43e38-129">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="43e38-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="43e38-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="43e38-131">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="43e38-131">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="43e38-132">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="43e38-132">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="43e38-133">ASP.NET Core 3,1 i nowsze oferują następujące wsparcie SameSite:</span><span class="sxs-lookup"><span data-stu-id="43e38-133">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="43e38-134">Ponownie definiuje zachowanie `SameSiteMode.None`, aby emitować `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="43e38-134">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="43e38-135">Dodaje nową wartość `SameSiteMode.Unspecified`, aby pominąć atrybut SameSite.</span><span class="sxs-lookup"><span data-stu-id="43e38-135">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="43e38-136">Wszystkie interfejsy API plików cookie są domyślnie `Unspecified`.</span><span class="sxs-lookup"><span data-stu-id="43e38-136">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="43e38-137">Niektóre składniki używające plików cookie ustawiają wartości bardziej specyficzne dla ich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="43e38-137">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="43e38-138">Przykłady można znaleźć w powyższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="43e38-138">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43e38-139">W ASP.NET Core 3,0 i nowszych wartości domyślnych SameSite zostały zmienione, aby uniknąć konfliktu z niespójnymi wartościami domyślnymi klienta.</span><span class="sxs-lookup"><span data-stu-id="43e38-139">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="43e38-140">Następujące interfejsy API zmieniły się domyślnie z `SameSiteMode.Lax ` na `-1`, aby uniknąć emitowania atrybutu SameSite dla tych plików cookie:</span><span class="sxs-lookup"><span data-stu-id="43e38-140">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="43e38-141"><xref:Microsoft.AspNetCore.Http.CookieOptions> używany z obiektem [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="43e38-141"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="43e38-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder> używany jako fabryka dla `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="43e38-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="43e38-143">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="43e38-143">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="43e38-144">Historia i zmiany</span><span class="sxs-lookup"><span data-stu-id="43e38-144">History and changes</span></span>

<span data-ttu-id="43e38-145">Obsługa SameSite została najpierw zaimplementowana w ASP.NET Core w 2,0 przy użyciu [standardowego standardu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="43e38-145">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="43e38-146">Standard 2016 został zadecydować.</span><span class="sxs-lookup"><span data-stu-id="43e38-146">The 2016 standard was opt-in.</span></span> <span data-ttu-id="43e38-147">ASP.NET Core wybierz opcję, ustawiając kilka plików cookie do `Lax` domyślnie.</span><span class="sxs-lookup"><span data-stu-id="43e38-147">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="43e38-148">Po napotkaniu kilku [problemów](https://github.com/aspnet/Announcements/issues/318) z uwierzytelnianiem większość SameSite zostało [wyłączone](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="43e38-148">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="43e38-149">Poprawki zostały wydane w listopadzie 2019, aby zaktualizować ze standardu 2016 do normy 2019.</span><span class="sxs-lookup"><span data-stu-id="43e38-149">Patches were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="43e38-150">[Wersja robocza 2019 specyfikacji SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="43e38-150">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="43e38-151">**Nie** jest wstecznie zgodne z wersją roboczą 2016.</span><span class="sxs-lookup"><span data-stu-id="43e38-151">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="43e38-152">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="43e38-152">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="43e38-153">Określa, że pliki cookie są domyślnie traktowane jako `SameSite=Lax`.</span><span class="sxs-lookup"><span data-stu-id="43e38-153">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="43e38-154">Określa pliki cookie, które jawnie potwierdzają `SameSite=None` w celu włączenia dostarczania między lokacjami, powinny być oznaczone jako `Secure`.</span><span class="sxs-lookup"><span data-stu-id="43e38-154">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="43e38-155">`None` to nowy wpis do rezygnacji.</span><span class="sxs-lookup"><span data-stu-id="43e38-155">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="43e38-156">Jest obsługiwane przez poprawki wydane dla ASP.NET Core 2,1, 2,2 i 3,0.</span><span class="sxs-lookup"><span data-stu-id="43e38-156">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="43e38-157">ASP.NET Core 3,1 oferuje dodatkową pomoc techniczną SameSite.</span><span class="sxs-lookup"><span data-stu-id="43e38-157">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="43e38-158">Zaplanowano włączenie programu [Chrome](https://chromestatus.com/feature/5088147346030592) domyślnie w [lutym 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="43e38-158">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="43e38-159">Przeglądarki zaczynają przechodzenie do tego standardu w 2019.</span><span class="sxs-lookup"><span data-stu-id="43e38-159">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="43e38-160">Interfejsy API, na które wpływem zmiany z standardu 2016 SameSite Draft do wersji Standard 2019</span><span class="sxs-lookup"><span data-stu-id="43e38-160">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="43e38-161">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="43e38-161">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="43e38-162">CookieOptions.SameSite</span><span class="sxs-lookup"><span data-stu-id="43e38-162">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="43e38-163">CookieBuilder.SameSite</span><span class="sxs-lookup"><span data-stu-id="43e38-163">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="43e38-164">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="43e38-164">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="43e38-165">Obsługa starszych przeglądarek</span><span class="sxs-lookup"><span data-stu-id="43e38-165">Supporting older browsers</span></span>

<span data-ttu-id="43e38-166">Standard 2016 SameSite, że nieznane wartości muszą być traktowane jako wartości `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="43e38-166">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="43e38-167">Aplikacje dostępne ze starszych przeglądarek, które obsługują Standard 2016 SameSite, mogą ulec przerwie, gdy pobierają Właściwość SameSite o wartości `None`.</span><span class="sxs-lookup"><span data-stu-id="43e38-167">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="43e38-168">Aplikacje sieci Web muszą implementować wykrywanie przeglądarki, jeśli chcą obsługiwać starsze przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="43e38-168">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="43e38-169">ASP.NET Core nie implementuje wykrywania przeglądarki, ponieważ wartości agentów użytkownika są bardzo nietrwałe i często zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="43e38-169">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="43e38-170">Punkt rozszerzenia w <xref:Microsoft.AspNetCore.CookiePolicy> umożliwia podłączanie w logice określonej przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43e38-170">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="43e38-171">W `Startup.Configure`Dodaj kod, który wywołuje <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> przed wywołaniem <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> lub *dowolnej* metody, która zapisuje pliki cookie:</span><span class="sxs-lookup"><span data-stu-id="43e38-171">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="43e38-172">W `Startup.ConfigureServices`Dodaj kod podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="43e38-172">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="43e38-173">W powyższym przykładzie `MyUserAgentDetectionLib.DisallowsSameSiteNone` jest biblioteką dostarczoną przez użytkownika, która wykrywa, czy agent użytkownika nie obsługuje SameSite `None`:</span><span class="sxs-lookup"><span data-stu-id="43e38-173">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="43e38-174">Poniższy kod przedstawia przykładową metodę `DisallowsSameSiteNone`:</span><span class="sxs-lookup"><span data-stu-id="43e38-174">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="43e38-175">Poniższy kod służy tylko do celów demonstracyjnych:</span><span class="sxs-lookup"><span data-stu-id="43e38-175">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="43e38-176">Nie powinna być uważana za ukończoną.</span><span class="sxs-lookup"><span data-stu-id="43e38-176">It should not be considered complete.</span></span>
> * <span data-ttu-id="43e38-177">Nie jest on obsługiwany ani wspierany.</span><span class="sxs-lookup"><span data-stu-id="43e38-177">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="43e38-178">Testowanie aplikacji pod kątem problemów z SameSite</span><span class="sxs-lookup"><span data-stu-id="43e38-178">Test apps for SameSite problems</span></span>

<span data-ttu-id="43e38-179">Aplikacje, które współdziałają z witrynami zdalnymi, takie jak logowanie innych firm, muszą:</span><span class="sxs-lookup"><span data-stu-id="43e38-179">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="43e38-180">Przetestuj interakcję w wielu przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="43e38-180">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="43e38-181">Zastosuj [wykrywanie i środki zaradcze w przeglądarce CookiePolicy](#sob) omówione w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="43e38-181">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="43e38-182">Przetestuj aplikacje sieci Web przy użyciu wersji klienta, która może być zgodą na nowe zachowanie SameSite.</span><span class="sxs-lookup"><span data-stu-id="43e38-182">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="43e38-183">Przeglądarki Chrome, Firefox i chrom Edge mają nowe flagi funkcji opt, których można użyć do testowania.</span><span class="sxs-lookup"><span data-stu-id="43e38-183">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="43e38-184">Po zastosowaniu przez aplikację SameSite poprawek przetestuj ją ze starszymi wersjami klienta, szczególnie Safari.</span><span class="sxs-lookup"><span data-stu-id="43e38-184">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="43e38-185">Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="43e38-185">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="43e38-186">Testowanie za pomocą przeglądarki Chrome</span><span class="sxs-lookup"><span data-stu-id="43e38-186">Test with Chrome</span></span>

<span data-ttu-id="43e38-187">Chrome 78 + daje mylące wyniki, ponieważ ma tymczasowe środki zaradcze.</span><span class="sxs-lookup"><span data-stu-id="43e38-187">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="43e38-188">Chrom 78 + tymczasowe środki zaradcze dopuszczają pliki cookie mniejsze niż dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="43e38-188">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="43e38-189">Program Chrome 76 lub 77 z włączonymi odpowiednimi flagami testu zapewnia dokładniejsze wyniki.</span><span class="sxs-lookup"><span data-stu-id="43e38-189">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="43e38-190">Aby przetestować nowe zachowanie SameSite, przełącz `chrome://flags/#same-site-by-default-cookies` na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="43e38-190">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="43e38-191">Starsze wersje programu Chrome (75 i poniżej) zostały zgłoszone w celu niepowodzenia z nowym ustawieniem `None`.</span><span class="sxs-lookup"><span data-stu-id="43e38-191">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="43e38-192">Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="43e38-192">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="43e38-193">Firma Google nie udostępnia starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="43e38-193">Google does not make older chrome versions available.</span></span> <span data-ttu-id="43e38-194">Postępuj zgodnie z instrukcjami podanymi w części [pobieranie chromu](https://www.chromium.org/getting-involved/download-chromium) , aby przetestować starsze wersje programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="43e38-194">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="43e38-195">**Nie** Pobieraj programu Chrome z linków dostarczonych przez wyszukiwanie starszych wersji programu Chrome.</span><span class="sxs-lookup"><span data-stu-id="43e38-195">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="43e38-196">Chrom 76 Win64</span><span class="sxs-lookup"><span data-stu-id="43e38-196">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="43e38-197">Chrom 74 Win64</span><span class="sxs-lookup"><span data-stu-id="43e38-197">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a><span data-ttu-id="43e38-198">Testowanie przy użyciu przeglądarki Safari</span><span class="sxs-lookup"><span data-stu-id="43e38-198">Test with Safari</span></span>

<span data-ttu-id="43e38-199">Przeglądarka Safari 12 ściśle wdrożyła poprzednią wersję roboczą i kończy się niepowodzeniem, gdy nowa wartość `None` jest w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="43e38-199">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="43e38-200">`None` jest możliwe za pośrednictwem kodu wykrywania przeglądarki [obsługującego starsze przeglądarki](#sob) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="43e38-200">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="43e38-201">Przetestuj logowanie za pomocą przeglądarki Safari 12, Safari 13 i WebKit opartej na programie MSAL, ADAL lub niezależnie od używanej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="43e38-201">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="43e38-202">Problem jest zależny od podstawowej wersji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="43e38-202">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="43e38-203">OSX Mojave (10,14) i iOS 12 są znane jako problemy ze zgodnością z nowym zachowaniem SameSite.</span><span class="sxs-lookup"><span data-stu-id="43e38-203">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="43e38-204">Uaktualnienie systemu operacyjnego do wersji OSX Catalina (10,15) lub iOS 13 rozwiązuje problem.</span><span class="sxs-lookup"><span data-stu-id="43e38-204">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="43e38-205">Przeglądarka Safari nie ma obecnie flagi zgody na testowanie nowych zachowań specyfikacji.</span><span class="sxs-lookup"><span data-stu-id="43e38-205">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="43e38-206">Testowanie za pomocą przeglądarki Firefox</span><span class="sxs-lookup"><span data-stu-id="43e38-206">Test with Firefox</span></span>

<span data-ttu-id="43e38-207">Obsługę programu Firefox dla nowego standardu można przetestować w wersji 68 + przez wypróbowanie na stronie `about:config` z flagą funkcji `network.cookie.sameSite.laxByDefault`.</span><span class="sxs-lookup"><span data-stu-id="43e38-207">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="43e38-208">Nie zgłoszono problemów ze zgodnością ze starszymi wersjami programu Firefox.</span><span class="sxs-lookup"><span data-stu-id="43e38-208">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="43e38-209">Testowanie przy użyciu przeglądarki Edge</span><span class="sxs-lookup"><span data-stu-id="43e38-209">Test with Edge browser</span></span>

<span data-ttu-id="43e38-210">Program Edge obsługuje stary Standard SameSite.</span><span class="sxs-lookup"><span data-stu-id="43e38-210">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="43e38-211">Wersja brzegowa 44 nie ma żadnych znanych problemów ze zgodnością z nowym standardem.</span><span class="sxs-lookup"><span data-stu-id="43e38-211">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="43e38-212">Testowanie przy użyciu krawędzi (chrom)</span><span class="sxs-lookup"><span data-stu-id="43e38-212">Test with Edge (Chromium)</span></span>

<span data-ttu-id="43e38-213">Flagi SameSite są ustawiane na stronie `edge://flags/#same-site-by-default-cookies`.</span><span class="sxs-lookup"><span data-stu-id="43e38-213">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="43e38-214">Nie odnaleziono problemów ze zgodnością z funkcją chromu.</span><span class="sxs-lookup"><span data-stu-id="43e38-214">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="43e38-215">Testuj przy użyciu elektronu</span><span class="sxs-lookup"><span data-stu-id="43e38-215">Test with Electron</span></span>

<span data-ttu-id="43e38-216">Wersje elektronów obejmują starsze wersje chromu.</span><span class="sxs-lookup"><span data-stu-id="43e38-216">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="43e38-217">Na przykład wersja elektronów używana przez zespoły to chrom 66, który wykazuje starsze zachowanie.</span><span class="sxs-lookup"><span data-stu-id="43e38-217">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="43e38-218">Należy przeprowadzić własne testy zgodności z wersją elektronów używaną przez produkt.</span><span class="sxs-lookup"><span data-stu-id="43e38-218">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="43e38-219">Zapoznaj się z tematem [Obsługa starszych przeglądarek](#sob) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="43e38-219">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43e38-220">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="43e38-220">Additional resources</span></span>

* [<span data-ttu-id="43e38-221">Blog chromu: deweloperzy: przygotowanie do nowego SameSite = none; Ustawienia bezpiecznego pliku cookie</span><span class="sxs-lookup"><span data-stu-id="43e38-221">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="43e38-222">Wyjaśniono pliki cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="43e38-222">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)