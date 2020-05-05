---
title: Pracuj z plikami cookie SameSite w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać programu do SameSite plików cookie w ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 43d5a3dbc5e202688e006355e0b105a86d721460
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775105"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>Pracuj z plikami cookie SameSite w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

SameSite jest projektem standardowym [IETF](https://ietf.org/about/) opracowanym w celu zapewnienia pewnej ochrony przed atakami polegającymi na translokacjach (CSRF). Pierwotnie Sporządzono w [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), projekt Standard został zaktualizowany w [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00). Zaktualizowany standard nie jest zgodny z poprzednią wersją standardową, co poniżej przedstawiono najbardziej zauważalne różnice:

* Pliki cookie bez nagłówka SameSite są traktowane `SameSite=Lax` domyślnie jako domyślne.
* `SameSite=None`musi być używany do zezwalania na użycie plików cookie między lokacjami.
* Pliki cookie, `SameSite=None` które potwierdzają, muszą `Secure`być również oznaczone jako.
* Aplikacje korzystające [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) z programu mogą napotkać `sameSite=Strict` problemy z `<iframe>` `sameSite=Lax` plikami cookie, ponieważ są traktowane jako scenariusze między lokacjami.
* Wartość `SameSite=None` nie jest dozwolona przez [Standard 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) i powoduje, że niektóre implementacje traktują takie pliki `SameSite=Strict`cookie jako. Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.

To `SameSite=Lax` ustawienie działa w przypadku większości plików cookie aplikacji. Niektóre formy uwierzytelniania, takie jak [OpenID Connect Connect](https://openid.net/connect/) (OIDC) i [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , domyślnie mogą publikować przekierowania na podstawie. Przekierowania na podstawie wpisu wyzwalają ochronę za pomocą przeglądarki SameSite, więc SameSite jest wyłączona dla tych składników. Nie ma to wpływ na większość nazw logowania [OAuth](https://oauth.net/) z powodu różnic w sposobie przepływu żądania.

Każdy składnik ASP.NET Core, który emituje pliki cookie, musi zdecydować, czy SameSite jest odpowiednie.

## <a name="samesite-test-sample-code"></a>Przykładowy kod testu SameSite

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Następujące przykłady można pobrać i przetestować:

| Przykład               | Dokument |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Można pobrać i przetestować następujący przykład:


| Przykład               | Dokument |
| ----------------- | ------------ |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>Obsługa platformy .NET Core dla atrybutu sameSite

Program .NET Core 2,2 obsługuje wersję standardową 2019 dla SameSite od momentu wydania aktualizacji w grudniu 2019. Deweloperzy mogą programowo kontrolować wartość atrybutu sameSite przy użyciu `HttpCookie.SameSite` właściwości. Ustawienie `SameSite` właściwości na Strict, swobodny lub None powoduje, że te wartości są zapisywane w sieci przy użyciu pliku cookie. Ustawienie tej opcji równe (SameSiteMode) (-1) wskazuje, że żaden atrybut sameSite nie powinien być uwzględniony w sieci z plikiem cookie

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

Program `SameSiteMode.Unspecified` .net Core 3,0 obsługuje zaktualizowane wartości SameSite i dodaje dodatkową wartość enum do `SameSiteMode` wyliczenia.
Ta nowa wartość wskazuje, że żaden sameSite nie powinien być wysyłany z plikiem cookie.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>Zmiany zachowania poprawek w grudniu

Określone zmiany zachowania dla .NET Framework i .NET Core 2,1 to sposób, w `SameSite` jaki Właściwość interpretuje `None` wartość. Przed poprawką wartość `None` "nie emituj atrybutu wcale", po zastosowaniu poprawki oznacza "Emituj atrybut wartością `None`". Po zastosowaniu poprawki `SameSite` wartość `(SameSiteMode)(-1)` powoduje, że atrybut nie jest emitowany.

Domyślna wartość SameSite dla plików cookie związanych z uwierzytelnianiem formularzy i stanem `None` sesji `Lax`została zmieniona z na.

::: moniker-end

## <a name="api-usage-with-samesite"></a>Użycie interfejsu API z SameSite

[HttpContext. Response. cookies. Dołącz](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) wartości domyślne `Unspecified`do, co oznacza, że żaden atrybut SameSite nie został dodany do pliku cookie i klient użyje jego domyślnego zachowania (swobodny dla nowych przeglądarek, brak dla starych). Poniższy kod przedstawia sposób zmiany wartości SameSite w pliku cookie na `SameSiteMode.Lax`:

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Wszystkie składniki ASP.NET Core, które emitują pliki cookie, zastępują poprzednie wartości domyślne przy użyciu ustawień odpowiednich dla ich scenariuszy. Zastąpione poprzednie wartości domyślne nie zostały zmienione.

| Składnik | plików | Domyślne |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions. cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions. cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions. cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Uwierzytelnianie plików cookie](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions. cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions.StateCookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3,1 i nowsze oferują następujące wsparcie SameSite:

* Ponownie definiuje zachowanie programu `SameSiteMode.None` do emisji`SameSite=None`
* Dodaje nową wartość `SameSiteMode.Unspecified` , aby pominąć atrybut SameSite.
* Wszystkie interfejsy API plików cookie `Unspecified`domyślnie. Niektóre składniki używające plików cookie ustawiają wartości bardziej specyficzne dla ich scenariuszy. Przykłady można znaleźć w powyższej tabeli.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

W ASP.NET Core 3,0 i nowszych wartości domyślnych SameSite zostały zmienione, aby uniknąć konfliktu z niespójnymi wartościami domyślnymi klienta. Następujące interfejsy API zmieniły się domyślnie z `SameSiteMode.Lax ` na `-1` , aby uniknąć emitowania atrybutu SameSite dla tych plików cookie:

* <xref:Microsoft.AspNetCore.Http.CookieOptions>używany z obiektem [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder>używany jako fabryka dla`CookieOptions`
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Historia i zmiany

Obsługa SameSite została najpierw zaimplementowana w ASP.NET Core w 2,0 przy użyciu [standardowego standardu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1). Standard 2016 został zadecydować. ASP.NET Core wybierz opcję `Lax` , domyślnie ustawiając kilka plików cookie. Po napotkaniu kilku [problemów](https://github.com/aspnet/Announcements/issues/318) z uwierzytelnianiem większość SameSite zostało [wyłączone](https://github.com/aspnet/Announcements/issues/348).

[Poprawki](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zostały wydane w listopadzie 2019, aby zaktualizować ze standardu 2016 do normy 2019. [Wersja robocza 2019 specyfikacji SameSite](https://github.com/aspnet/Announcements/issues/390):

* **Nie** jest wstecznie zgodne z wersją roboczą 2016. Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.
* Określa, że pliki cookie `SameSite=Lax` są traktowane domyślnie jako domyślne.
* Określa pliki cookie jawnie potwierdzone `SameSite=None` w celu włączenia dostarczania między lokacjami, które powinny być oznaczone `Secure`jako. `None`jest nowym wpisem, aby zrezygnować z tej opcji.
* Jest obsługiwane przez poprawki wydane dla ASP.NET Core 2,1, 2,2 i 3,0. ASP.NET Core 3,1 oferuje dodatkową pomoc techniczną SameSite.
* Zaplanowano włączenie programu [Chrome](https://chromestatus.com/feature/5088147346030592) domyślnie w [lutym 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html). Przeglądarki zaczynają przechodzenie do tego standardu w 2019.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>Interfejsy API, na które wpływem zmiany z standardu 2016 SameSite Draft do wersji Standard 2019

* [Http. SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Obsługa starszych przeglądarek

Standard 2016 SameSite, że nieznane wartości muszą być traktowane jako `SameSite=Strict` wartości. Aplikacje dostępne ze starszych przeglądarek, które obsługują Standard 2016 SameSite, mogą ulec przerwie, gdy pobierają Właściwość SameSite o `None`wartości. Aplikacje sieci Web muszą implementować wykrywanie przeglądarki, jeśli chcą obsługiwać starsze przeglądarki. ASP.NET Core nie implementuje wykrywania przeglądarki, ponieważ wartości agentów użytkownika są bardzo nietrwałe i często zmieniają się. Punkt rozszerzenia w programie <xref:Microsoft.AspNetCore.CookiePolicy> umożliwia podłączanie w ramach logiki specyficznej dla agenta użytkownika.

W `Startup.Configure`programie Dodaj kod, który <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> wywołuje przed <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> wywołaniem metody lub *dowolną* metodę, która zapisuje pliki cookie:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

W `Startup.ConfigureServices`programie Dodaj kod podobny do poniższego:

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

W powyższym przykładzie `MyUserAgentDetectionLib.DisallowsSameSiteNone` jest biblioteką dostarczoną przez użytkownika, która wykrywa, czy agent użytkownika `None`nie obsługuje SameSite:

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

Poniższy kod przedstawia przykładową `DisallowsSameSiteNone` metodę:

> [!WARNING]
> Poniższy kod służy tylko do celów demonstracyjnych:
> * Nie powinna być uważana za ukończoną.
> * Nie jest on obsługiwany ani wspierany.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Testowanie aplikacji pod kątem problemów z SameSite

Aplikacje, które współdziałają z witrynami zdalnymi, takie jak logowanie innych firm, muszą:

* Przetestuj interakcję w wielu przeglądarkach.
* Zastosuj [wykrywanie i środki zaradcze w przeglądarce CookiePolicy](#sob) omówione w tym dokumencie.

Przetestuj aplikacje sieci Web przy użyciu wersji klienta, która może być zgodą na nowe zachowanie SameSite. Przeglądarki Chrome, Firefox i chrom Edge mają nowe flagi funkcji opt, których można użyć do testowania. Po zastosowaniu przez aplikację SameSite poprawek przetestuj ją ze starszymi wersjami klienta, szczególnie Safari. Aby uzyskać więcej informacji, zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.

### <a name="test-with-chrome"></a>Testowanie za pomocą przeglądarki Chrome

Chrome 78 + daje mylące wyniki, ponieważ ma tymczasowe środki zaradcze. Chrom 78 + tymczasowe środki zaradcze dopuszczają pliki cookie mniejsze niż dwie minuty. Program Chrome 76 lub 77 z włączonymi odpowiednimi flagami testu zapewnia dokładniejsze wyniki. Aby przetestować nowe zachowanie SameSite, przełącz `chrome://flags/#same-site-by-default-cookies` się na **włączone**. Starsze wersje programu Chrome (75 i poniżej) zostały zgłoszone w celu niepowodzenia `None` z nowym ustawieniem. Zobacz [Obsługa starszych przeglądarek](#sob) w tym dokumencie.

Firma Google nie udostępnia starszych wersji programu Chrome. Postępuj zgodnie z instrukcjami podanymi w części [pobieranie chromu](https://www.chromium.org/getting-involved/download-chromium) , aby przetestować starsze wersje programu Chrome. **Nie** Pobieraj programu Chrome z linków dostarczonych przez wyszukiwanie starszych wersji programu Chrome.

* [Chrom 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chrom 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

Począwszy od wersji `80.0.3975.0`Kanaryjskich, można wyłączyć swobodny + post tymczasowych środków zaradczych do celów testowych przy użyciu nowej flagi `--enable-features=SameSiteDefaultChecksMethodRigorously` , aby umożliwić Testowanie witryn i usług w stanie końcowym funkcji, w której usunięto środki zaradcze. Aby uzyskać więcej informacji, zobacz artykuł dotyczący projektów chrom [SameSite Updates](https://www.chromium.org/updates/same-site)

### <a name="test-with-safari"></a>Testowanie przy użyciu przeglądarki Safari

Przeglądarka Safari 12 ściśle wdrożyła poprzednią wersję roboczą i `None` kończy się niepowodzeniem, gdy nowa wartość jest w pliku cookie. `None`jest to możliwe za pośrednictwem kodu wykrywania przeglądarki [obsługującego starsze przeglądarki](#sob) w tym dokumencie. Przetestuj logowanie za pomocą przeglądarki Safari 12, Safari 13 i WebKit opartej na programie MSAL, ADAL lub niezależnie od używanej biblioteki. Problem jest zależny od podstawowej wersji systemu operacyjnego. OSX Mojave (10,14) i iOS 12 są znane jako problemy ze zgodnością z nowym zachowaniem SameSite. Uaktualnienie systemu operacyjnego do wersji OSX Catalina (10,15) lub iOS 13 rozwiązuje problem. Przeglądarka Safari nie ma obecnie flagi zgody na testowanie nowych zachowań specyfikacji.

### <a name="test-with-firefox"></a>Testowanie za pomocą przeglądarki Firefox

Obsługę programu Firefox dla nowego standardu można przetestować w wersji 68 + przez wypróbowanie jej `about:config` na stronie z flagą `network.cookie.sameSite.laxByDefault`funkcji. Nie zgłoszono problemów ze zgodnością ze starszymi wersjami programu Firefox.

### <a name="test-with-edge-browser"></a>Testowanie przy użyciu przeglądarki Edge

Program Edge obsługuje stary Standard SameSite. Wersja brzegowa 44 nie ma żadnych znanych problemów ze zgodnością z nowym standardem.

### <a name="test-with-edge-chromium"></a>Testowanie przy użyciu krawędzi (chrom)

Flagi SameSite są ustawiane na `edge://flags/#same-site-by-default-cookies` stronie. Nie odnaleziono problemów ze zgodnością z funkcją chromu.

### <a name="test-with-electron"></a>Testuj przy użyciu elektronu

Wersje elektronów obejmują starsze wersje chromu. Na przykład wersja elektronów używana przez zespoły to chrom 66, który wykazuje starsze zachowanie. Należy przeprowadzić własne testy zgodności z wersją elektronów używaną przez produkt. Zapoznaj się z tematem [Obsługa starszych przeglądarek](#sob) w następnej sekcji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Blog chromu: deweloperzy: przygotowanie do nowego SameSite = none; Ustawienia bezpiecznego pliku cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [Wyjaśniono pliki cookie SameSite](https://web.dev/samesite-cookies-explained/)
* [Poprawki 2019 listopada](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| Przykład               | Dokument |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| Przykład               | Dokument |
| ----------------- | ------------ |
| [Strony .NET Razor Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
