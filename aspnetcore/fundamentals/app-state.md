---
title: Sesja w ASP.NET Core
author: rick-anderson
description: Odkryj podejścia do zachowywania sesji między żądaniami.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: c11b748f9d79235b14c9541019da6e1fb3428af6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051411"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Zarządzanie sesjami i stanami na platformie ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Dianę LaRose](https://github.com/DianaLaRose)

HTTP jest bezstanowy protokół. Domyślnie żądania HTTP są niezależnymi komunikatami, które nie zachowują wartości użytkownika. W tym artykule opisano kilka metod zachowywania danych użytkownika między żądaniami.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Zarządzanie stanem

Stan może być przechowywany przy użyciu kilku metod. Każde podejście zostało opisane w dalszej części tego tematu.

| Podejście do magazynu | Mechanizm magazynu |
| ---------------- | ----------------- |
| [Cookiewolumin](#cookies) | HTTP cookie s. Mogą zawierać dane przechowywane przy użyciu kodu aplikacji po stronie serwera. |
| [Stan sesji](#session-state) | HTTP cookie s i kod aplikacji po stronie serwera |
| [TempData](#tempdata) | cookieStan sesji http s lub |
| [Ciągi zapytań](#query-strings) | Ciągi zapytań HTTP |
| [Ukryte pola](#hidden-fields) | Pola formularza HTTP |
| [HttpContext. Items](#httpcontextitems) | Kod aplikacji po stronie serwera |
| [Cache](#cache) | Kod aplikacji po stronie serwera |

## <a name="no-loccookies"></a>Cookiewolumin

Cookies Przechowuj dane między żądaniami. Ze względu na cookie to, że s są wysyłane przy każdym żądaniu, ich rozmiar powinien być minimalny. W idealnym przypadku tylko identyfikator powinien być przechowywany w elemencie cookie z danymi przechowywanymi w aplikacji. Większość przeglądarek ogranicza cookie rozmiar do 4096 bajtów. cookieDla każdej domeny są dostępne tylko ograniczoną liczbę s.

Ponieważ cookie s podlegają naruszeniu, muszą one być zweryfikowane przez aplikację. Cookieelementy s mogą zostać usunięte przez użytkowników i wygasnąć na klientach. Jednakże cookie s generalnie najbardziej trwała forma trwałości danych na kliencie.

Cookies są często używane do personalizacji, gdzie zawartość jest dostosowywana dla znanego użytkownika. Użytkownik jest identyfikowany i nie jest uwierzytelniany w większości przypadków. cookieMoże przechowywać nazwę użytkownika, nazwę konta lub unikatowy identyfikator użytkownika, na przykład identyfikator GUID. cookieMoże służyć do uzyskiwania dostępu do spersonalizowanych ustawień użytkownika, takich jak preferowany kolor tła witryny sieci Web.

Zapoznaj się z [ogólnymi przepisami Unii Europejskiej dotyczącej ochrony danych (Rodo)](https://ec.europa.eu/info/law/law-topic/data-protection) podczas wystawiania cookie i rozwiązywania problemów z ochroną prywatności. Aby uzyskać więcej informacji, zobacz temat [obsługa ogólne rozporządzenie o ochronie danych (Rodo) w programie ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stan sesji

Stan sesji to ASP.NET Core scenariusz przechowywania danych użytkownika podczas przeglądania aplikacji sieci Web przez użytkownika. Stan sesji używa magazynu obsługiwanego przez aplikację w celu utrwalania danych między żądaniami od klienta. Dane sesji są obsługiwane przez pamięć podręczną i uznawane za dane tymczasowe. Lokacja powinna nadal działać bez danych sesji. Krytyczne dane aplikacji powinny być przechowywane w bazie danych użytkownika i buforowane w sesji tylko jako Optymalizacja wydajności.

Sesja nie jest obsługiwana w [SignalR](xref:signalr/index) aplikacjach, ponieważ [ SignalR koncentrator](xref:signalr/hubs) może działać niezależnie od kontekstu http. Na przykład może się to zdarzyć, gdy długotrwałe żądanie sondowania jest przechowywane przez centrum poza okresem istnienia kontekstu HTTP żądania.

ASP.NET Core utrzymuje stan sesji przez udostępnienie cookie klientowi, który zawiera identyfikator sesji. cookieIdentyfikator sesji:

* Jest wysyłany do aplikacji przy użyciu każdego żądania.
* Jest używana przez aplikację do pobierania danych sesji.

Stan sesji wykazuje następujące zachowania:

* Sesja cookie jest specyficzna dla przeglądarki. Sesje nie są współużytkowane przez przeglądarki.
* Sesja cookie s jest usuwana po zakończeniu sesji przeglądarki.
* Jeśli cookie zostanie odebrana sesja wygasła, zostanie utworzona nowa sesja, która używa tej samej sesji cookie .
* Puste sesje nie są zachowywane. Sesja musi mieć ustawioną co najmniej jedną wartość, aby zachować sesję między żądaniami. Gdy sesja nie jest zachowywana, dla każdego nowego żądania jest generowany nowy identyfikator sesji.
* Aplikacja zachowuje sesję przez ograniczony czas po ostatnim żądaniu. Aplikacja ustawia limit czasu sesji lub używa wartości domyślnej wynoszącej 20 minut. Stan sesji jest idealnym rozwiązaniem do przechowywania danych użytkownika:
  * Jest to specyficzne dla konkretnej sesji.
  * Miejsce, w którym dane nie wymagają stałego magazynu między sesjami.
* Dane sesji są usuwane w przypadku <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> wywołania implementacji lub czasu wygaśnięcia sesji.
* Nie istnieje domyślny mechanizm do powiadamiania kodu aplikacji o zamknięciu przeglądarki klienta lub po cookie usunięciu lub wygaśnięciu sesji na kliencie.
* Stan sesji cookie s nie jest domyślnie oznaczony jako istotny. Stan sesji nie działa, chyba że śledzenie jest dozwolone przez odwiedzających witrynę. Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Nie należy przechowywać poufnych danych w stanie sesji. Użytkownik może nie zamknąć przeglądarki i wyczyścić tę sesję cookie . Niektóre przeglądarki utrzymują prawidłowe sesje cookie w oknach przeglądarki. Sesja może nie być ograniczona do pojedynczego użytkownika. Następny użytkownik może kontynuować przeglądanie aplikacji z tą samą sesją cookie .

Dostawca pamięci podręcznej w pamięci przechowuje dane sesji w pamięci serwera, na którym znajduje się aplikacja. W scenariuszu farmy serwerów:

* Użyj *sesji programu Sticky* , aby powiązać każdą sesję z określonym wystąpieniem aplikacji na pojedynczym serwerze. [Azure App Service](https://azure.microsoft.com/services/app-service/) używa [routingu żądań aplikacji (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) do wymuszania sesji usługi Sticky one domyślnie. Jednak sesje programu Sticky Notes mogą mieć wpływ na skalowalność i komplikują aktualizacje aplikacji sieci Web. Lepszym rozwiązaniem jest użycie rozproszonej pamięci podręcznej Redis lub SQL Server, która nie wymaga sesji programu Sticky Notes. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Sesja cookie jest szyfrowana za pośrednictwem programu <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . Ochrona danych musi być poprawnie skonfigurowana do odczytu sesji cookie s na poszczególnych komputerach. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction> i [dostawcy magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurowanie stanu sesji

Pakiet [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) :

* Jest dołączony niejawnie przez platformę.
* Zapewnia oprogramowanie pośredniczące do zarządzania stanem sesji.

Aby włączyć oprogramowanie pośredniczące sesji, `Startup` musi zawierać następujące polecenie:

* Dowolna pamięć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> podręczna pamięci. `IDistributedCache`Implementacja jest używana jako magazyn zapasowy dla sesji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Wywołanie <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> w `ConfigureServices` .
* Wywołanie <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> w `Configure` .

Poniższy kod pokazuje, jak skonfigurować dostawcę sesji w pamięci z domyślną implementacją w pamięci `IDistributedCache` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Poprzedni kod ustawia krótki limit czasu, aby uprościć testowanie.

Kolejność oprogramowania pośredniczącego jest ważna.  Wywołanie `UseSession` After `UseRouting` i Before `UseEndpoints` . Zobacz [porządkowanie oprogramowania pośredniczącego](xref:fundamentals/middleware/index#order).

Element [HttpContext. Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) jest dostępny po skonfigurowaniu stanu sesji.

`HttpContext.Session` nie można uzyskać dostępu przed `UseSession` wywołaniem.

Nie można utworzyć nowej sesji z nową sesją cookie , gdy aplikacja rozpoczęła zapisywanie w strumieniu odpowiedzi. Wyjątek jest rejestrowany w dzienniku serwera sieci Web i nie jest wyświetlany w przeglądarce.

### <a name="load-session-state-asynchronously"></a>Załaduj stan sesji asynchronicznie

Domyślny dostawca sesji w ASP.NET Core ładuje rekordy sesji z bazowego <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> magazynu zapasowego tylko wtedy, gdy <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> Metoda jest jawnie wywoływana przed <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> metodami, lub <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . Jeśli `LoadAsync` nie zostanie najpierw wywołana, źródłowy rekord sesji jest ładowany synchronicznie, co może spowodować spadek wydajności na dużą skalę.

Aby aplikacje wymuszają ten wzorzec, zawiń <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> <xref:Microsoft.AspNetCore.Session.DistributedSession> implementacje i w wersji, które zgłaszają wyjątek, jeśli `LoadAsync` Metoda nie jest wywoływana przed `TryGetValue` , `Set` , lub `Remove` . Zarejestruj opakowane wersje w kontenerze usługi.

### <a name="session-options"></a>Opcje sesji

Aby zastąpić wartości domyślne sesji, użyj <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Określa ustawienia używane do tworzenia cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> wartość domyślna to <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> wartość domyślna to <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> wartość domyślna to <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> wartość domyślna to `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> wartość domyślna to `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout`Wskazuje, jak długo sesja może być bezczynna, zanim jej zawartość zostanie porzucona. Każdy dostęp do sesji resetuje limit czasu. To ustawienie dotyczy tylko zawartości sesji, a nie cookie . Wartość domyślna to 20 minut. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Maksymalna ilość czasu, którą można załadować sesji ze sklepu lub zatwierdzić ją z powrotem do magazynu. To ustawienie może dotyczyć tylko operacji asynchronicznych. Ten limit czasu można wyłączyć za pomocą polecenia <xref:System.Threading.Timeout.InfiniteTimeSpan> . Wartość domyślna to 1 minuta. |

Sesja służy cookie do śledzenia i identyfikowania żądań z pojedynczej przeglądarki. Domyślnie cookie jest to nazwa `.AspNetCore.Session` i używa ścieżki `/` . Ponieważ cookie domyślnie nie określono domeny, nie jest ona dostępna dla skryptu po stronie klienta na stronie (ponieważ <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> Domyślnie jest to `true` ).

Aby zastąpić cookie wartości domyślne sesji, użyj <xref:Microsoft.AspNetCore.Builder.SessionOptions> :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Aplikacja używa właściwości, <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> Aby określić, jak długo sesja może być bezczynna, zanim jej zawartość w pamięci podręcznej serwera zostanie porzucona. Ta właściwość jest niezależna od cookie daty wygaśnięcia. Każde żądanie przechodzące przez [oprogramowanie pośredniczące sesji](xref:Microsoft.AspNetCore.Session.SessionMiddleware) resetuje limit czasu.

Stan sesji to *nie jest blokowanie* . Jeśli dwa żądania jednocześnie próbują zmodyfikować zawartość sesji, ostatnie żądanie zastępuje pierwsze. `Session` Program jest implementowany jako *spójna sesja* , co oznacza, że cała zawartość jest przechowywana razem. Gdy dwa żądania poszukują modyfikacji różnych wartości sesji, ostatnie żądanie może zastąpić zmiany sesji wykonane przez pierwsze.

### <a name="set-and-get-session-values"></a>Ustawianie i pobieranie wartości sesji

Stan sesji jest dostępny z Razor klasy Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC <xref:Microsoft.AspNetCore.Mvc.Controller> klasy z <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Ta właściwość jest <xref:Microsoft.AspNetCore.Http.ISession> implementacją.

`ISession`Implementacja zawiera kilka metod rozszerzających, które umożliwiają ustawianie i pobieranie wartości całkowitych i ciągów. Metody rozszerzające znajdują się w <xref:Microsoft.AspNetCore.Http> przestrzeni nazw.

`ISession` metody rozszerzające:

* [Get (ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32 (ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString (ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32 (ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString (ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Poniższy przykład pobiera wartość sesji dla `IndexModel.SessionKeyName` klucza ( `_Name` w przykładowej aplikacji) na Razor stronie stron:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Poniższy przykład pokazuje, jak ustawić i pobrać liczbę całkowitą i ciąg:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Wszystkie dane sesji muszą być serializowane w celu włączenia scenariusza rozproszonej pamięci podręcznej, nawet w przypadku korzystania z pamięci podręcznej w pamięci. Serializatory ciągów i liczb całkowitych są udostępniane przez metody rozszerzające <xref:Microsoft.AspNetCore.Http.ISession> . Typy złożone muszą być serializowane przez użytkownika przy użyciu innego mechanizmu, takiego jak JSON.

Użyj następującego przykładowego kodu do serializacji obiektów:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Poniższy przykład pokazuje, jak ustawić i pobrać obiekt możliwy do serializacji z `SessionExtensions` klasą:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core udostępnia Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) lub kontroler stron <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Ta właściwość przechowuje dane, dopóki nie zostanie odczytany w innym żądaniu. Metody [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) i [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) mogą służyć do badania danych bez usuwania na końcu żądania. [Pozostaw](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) Oznacz wszystkie elementy w słowniku do przechowywania. `TempData` była

* Przydatne w przypadku przekierowania, gdy dane są wymagane dla więcej niż jednego żądania.
* Zaimplementowane przez `TempData` dostawców przy użyciu cookie stanu s lub sesji.

## <a name="tempdata-samples"></a>Przykłady TempData

Rozważmy następującą stronę, która tworzy klienta:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Zostanie wyświetlona następująca strona `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

W powyższym znaczniku na końcu żądania `TempData["Message"]` **nie** jest usuwany, ponieważ `Peek` jest używany. Odświeżenie strony powoduje wyświetlenie zawartości `TempData["Message"]` .

Poniższe znaczniki przypominają poprzedni kod, ale używają `Keep` do zachowywania danych na końcu żądania:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Nawigowanie po stronach *IndexPeek* i *IndexKeep* nie zostanie usunięte `TempData["Message"]` .

Poniższy kod wyświetla `TempData["Message"]` , ale na końcu żądania, `TempData["Message"]` jest usuwany:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Dostawcy TempData

cookieDostawca TempData oparty na protokole jest używany domyślnie do przechowywania TempData w cookie s.

cookieDane są szyfrowane przy użyciu <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> kodowania z <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> , a następnie fragmenty. Maksymalny cookie rozmiar jest mniejszy niż [4096 bajtów](http://www.faqs.org/rfcs/rfc2965.html) z powodu szyfrowania i fragmentacji. cookieDane nie są kompresowane, ponieważ kompresowanie zaszyfrowanych danych może prowadzić do problemów z zabezpieczeniami, takich jak ataki w ramach [przestępczości](https://wikipedia.org/wiki/CRIME_(security_exploit)) i [naruszenia](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Aby uzyskać więcej informacji na temat cookie dostawcy TempData opartego na systemie, zobacz <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Wybierz dostawcę TempData

Wybór dostawcy TempData obejmuje kilka zagadnień, takich jak:

* Czy aplikacja już używa stanu sesji? Jeśli tak, użycie dostawcy TempData stanu sesji nie ma dodatkowych kosztów dla aplikacji poza rozmiarem danych.
* Czy aplikacja będzie używać TempData tylko dla stosunkowo małych ilości danych, do 500 bajtów? W takim przypadku cookie dostawca TempData dodaje niewielki koszt do każdego żądania, które przenosi TempData. W przeciwnym razie dostawca TempData stanu sesji może być korzystne, aby uniknąć jednoczesnego wyzwolenia dużej ilości danych w każdym żądaniu do momentu użycia TempData.
* Czy aplikacja działa w farmie serwerów na wielu serwerach? W takim przypadku nie jest wymagana dodatkowa konfiguracja do korzystania z cookie dostawcy TempData poza ochroną danych (zobacz <xref:security/data-protection/introduction> i [dostawcy magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers)).

Większość klientów sieci Web, takich jak przeglądarki sieci Web, wymusza limity maksymalnego rozmiaru każdej z nich cookie i łączną liczbę cookie s. W przypadku korzystania z cookie dostawcy TempData upewnij się, że aplikacja nie przekroczy [tych limitów](http://www.faqs.org/rfcs/rfc2965.html). Należy wziąć pod uwagę łączny rozmiar danych. Konto zwiększające cookie rozmiar ze względu na szyfrowanie i rozdzielenie.

### <a name="configure-the-tempdata-provider"></a>Konfigurowanie dostawcy TempData

cookieDostawca TempData oparty na programie jest domyślnie włączony.

Aby włączyć dostawcę TempData opartego na sesji, należy użyć <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> metody rozszerzenia. Tylko jedno wywołanie `AddSessionStateTempDataProvider` jest wymagane:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Ciągi zapytań

Ograniczoną ilość danych można przekazywać z jednego żądania do innego przez dodanie go do ciągu zapytania nowego żądania. Jest to przydatne do przechwytywania stanu w sposób trwały, który umożliwia udostępnianie linków ze stanem osadzonym za pośrednictwem poczty e-mail lub sieci społecznościowych. Ponieważ ciągi zapytań URL są publiczne, nigdy nie używaj ciągów zapytań do poufnych danych.

Oprócz niezamierzonego udostępniania, w tym danych w ciągach zapytań, można uwidocznić aplikację w atakach na [żądania Bezwitrynowe (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) . Każdy zachowany stan sesji musi chronić przed atakami CSRF. Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Ukryte pola

Dane można zapisywać w ukrytych polach formularzy i ogłaszane przy użyciu następnego żądania. Jest to typowe w formularzach wielostronicowych. Ponieważ klient może potencjalnie naruszać dane, aplikacja musi zawsze ponownie sprawdzić poprawność danych przechowywanych w ukrytych polach.

## <a name="httpcontextitems"></a>HttpContext. Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>Kolekcja jest używana do przechowywania danych podczas przetwarzania pojedynczego żądania. Zawartość kolekcji jest odrzucana po przetworzeniu żądania. `Items`Kolekcja jest często używana do zezwalania składnikom lub oprogramowaniu pośredniczącemu na komunikowanie się, gdy pracują w różnych punktach w czasie w trakcie żądania i nie mają bezpośredniego sposobu przekazywania parametrów.

W poniższym przykładzie [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) dodaje `isVerified` do `Items` kolekcji:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

W przypadku oprogramowania pośredniczącego, które jest używane tylko w pojedynczej aplikacji, stałe `string` klucze są akceptowalne. Oprogramowanie pośredniczące udostępnione między aplikacjami powinno używać unikatowych kluczy obiektów, aby uniknąć kolizji kluczy. Poniższy przykład pokazuje, jak używać unikatowego klucza obiektu zdefiniowanego w klasie pośredniczącej:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Inny kod może uzyskać dostęp do wartości przechowywanej `HttpContext.Items` przy użyciu klucza uwidacznianego przez klasę pośredniczącą:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Takie podejście ma również zalety wyeliminowania używania ciągów kluczy w kodzie.

## <a name="cache"></a>Pamięć podręczna

Buforowanie jest wydajnym sposobem przechowywania i pobierania danych. Aplikacja może kontrolować okres istnienia elementów w pamięci podręcznej. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response>.

Dane w pamięci podręcznej nie są skojarzone z określonym żądaniem, użytkownikiem lub sesją. **Nie Buforuj danych specyficznych dla użytkownika, które mogą być pobierane przez inne żądania użytkownika.**

Aby buforować dane na poziomie aplikacji, zobacz <xref:performance/caching/memory> .

## <a name="common-errors"></a>Typowe błędy

* "Nie można rozpoznać usługi dla typu" Microsoft. Extensions. buforowania. Distributed. IDistributedCache "podczas próby aktywowania elementu" Microsoft. AspNetCore. Session. DistributedSessionStore "."

  Zwykle jest to spowodowane błędem konfiguracji co najmniej jednej `IDistributedCache` implementacji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed> i <xref:performance/caching/memory>.

Jeśli oprogramowanie pośredniczące sesji nie może utrwalać sesji:

* Oprogramowanie pośredniczące rejestruje wyjątek, a żądanie jest zwykle kontynuowane.
* Prowadzi to do nieprzewidywalnego zachowania.

Oprogramowanie pośredniczące sesji może nie być w stanie przerwać sesji, jeśli magazyn zapasowy nie jest dostępny. Na przykład użytkownik przechowuje koszyk w sesji. Użytkownik dodaje element do koszyka, ale zatwierdzanie kończy się niepowodzeniem. Aplikacja nie wie o niepowodzeniu, dlatego zgłasza użytkownikowi informacje o tym, że element został dodany do swojego koszyka, co nie jest prawdziwe.

Zalecane podejście do sprawdzenia pod kątem błędów jest wywoływane `await feature.Session.CommitAsync` , gdy aplikacja jest gotowa do zapisu w sesji. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> zgłasza wyjątek, jeśli magazyn zapasowy jest niedostępny. Jeśli `CommitAsync` to się nie powiedzie, aplikacja może przetworzyć wyjątek. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> zgłasza w tych samych warunkach, gdy magazyn danych jest niedostępny.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR i stan sesji

SignalR aplikacje nie powinny używać stanu sesji do przechowywania informacji. SignalR aplikacje mogą przechowywać stan dla połączenia w `Context.Items` centrum. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Dodatkowe zasoby

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Dianę LaRose](https://github.com/DianaLaRose)i [Luke Latham](https://github.com/guardrex)

HTTP jest bezstanowy protokół. Bez podejmowania dodatkowych kroków żądania HTTP są niezależnymi komunikatami, które nie zachowują wartości użytkownika lub stanu aplikacji. W tym artykule opisano kilka metod zachowywania danych użytkownika i stanu aplikacji między żądaniami.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Zarządzanie stanem

Stan może być przechowywany przy użyciu kilku metod. Każde podejście zostało opisane w dalszej części tego tematu.

| Podejście do magazynu | Mechanizm magazynu |
| ---------------- | ----------------- |
| [Cookiewolumin](#cookies) | HTTP cookie s (mogą zawierać dane przechowywane przy użyciu kodu aplikacji po stronie serwera) |
| [Stan sesji](#session-state) | HTTP cookie s i kod aplikacji po stronie serwera |
| [TempData](#tempdata) | cookieStan sesji http s lub |
| [Ciągi zapytań](#query-strings) | Ciągi zapytań HTTP |
| [Ukryte pola](#hidden-fields) | Pola formularza HTTP |
| [HttpContext. Items](#httpcontextitems) | Kod aplikacji po stronie serwera |
| [Cache](#cache) | Kod aplikacji po stronie serwera |
| [Wstrzykiwanie zależności](#dependency-injection) | Kod aplikacji po stronie serwera |

## <a name="no-loccookies"></a>Cookiewolumin

Cookies Przechowuj dane między żądaniami. Ze względu na cookie to, że s są wysyłane przy każdym żądaniu, ich rozmiar powinien być minimalny. W idealnym przypadku tylko identyfikator powinien być przechowywany w elemencie cookie z danymi przechowywanymi w aplikacji. Większość przeglądarek ogranicza cookie rozmiar do 4096 bajtów. cookieDla każdej domeny są dostępne tylko ograniczoną liczbę s.

Ponieważ cookie s podlegają naruszeniu, muszą one być zweryfikowane przez aplikację. Cookieelementy s mogą zostać usunięte przez użytkowników i wygasnąć na klientach. Jednakże cookie s generalnie najbardziej trwała forma trwałości danych na kliencie.

Cookies są często używane do personalizacji, gdzie zawartość jest dostosowywana dla znanego użytkownika. Użytkownik jest identyfikowany i nie jest uwierzytelniany w większości przypadków. cookieMoże przechowywać nazwę użytkownika, nazwę konta lub unikatowy identyfikator użytkownika (na przykład identyfikator GUID). Następnie można użyć programu, cookie Aby uzyskać dostęp do spersonalizowanych ustawień użytkownika, takich jak preferowany kolor tła witryny sieci Web.

Należy zastanowić się w trosce o [Ogólne przepisy dotyczące ochrony danych w Unii Europejskiej (Rodo)](https://ec.europa.eu/info/law/law-topic/data-protection) podczas wystawiania cookie i rozwiązywania problemów z ochroną prywatności. Aby uzyskać więcej informacji, zobacz temat [obsługa ogólne rozporządzenie o ochronie danych (Rodo) w programie ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stan sesji

Stan sesji to ASP.NET Core scenariusz przechowywania danych użytkownika podczas przeglądania aplikacji sieci Web przez użytkownika. Stan sesji używa magazynu obsługiwanego przez aplikację w celu utrwalania danych między żądaniami od klienta. Dane sesji są obsługiwane przez pamięć podręczną i uznawane za dane tymczasowe &mdash; , a lokacja powinna nadal działać bez danych sesji. Krytyczne dane aplikacji powinny być przechowywane w bazie danych użytkownika i buforowane w sesji tylko jako Optymalizacja wydajności.

> [!NOTE]
> Sesja nie jest obsługiwana w [SignalR](xref:signalr/index) aplikacjach, ponieważ [ SignalR koncentrator](xref:signalr/hubs) może działać niezależnie od kontekstu http. Na przykład może się to zdarzyć, gdy długotrwałe żądanie sondowania jest przechowywane przez centrum poza okresem istnienia kontekstu HTTP żądania.

ASP.NET Core utrzymuje stan sesji przez udostępnienie cookie klientowi, który zawiera identyfikator sesji, który jest wysyłany do aplikacji przy użyciu każdego żądania. Aplikacja używa identyfikatora sesji w celu pobrania danych sesji.

Stan sesji wykazuje następujące zachowania:

* Ponieważ sesja cookie jest specyficzna dla przeglądarki, sesje nie są współużytkowane przez przeglądarki.
* Sesja cookie s jest usuwana po zakończeniu sesji przeglądarki.
* Jeśli cookie zostanie odebrana sesja wygasła, zostanie utworzona nowa sesja, która używa tej samej sesji cookie .
* Puste sesje nie są zachowywane &mdash; , sesja musi mieć ustawioną co najmniej jedną wartość, aby zachować sesję między żądaniami. Gdy sesja nie jest zachowywana, dla każdego nowego żądania jest generowany nowy identyfikator sesji.
* Aplikacja zachowuje sesję przez ograniczony czas po ostatnim żądaniu. Aplikacja ustawia limit czasu sesji lub używa wartości domyślnej wynoszącej 20 minut. Stan sesji jest idealnym rozwiązaniem do przechowywania danych użytkownika, które są specyficzne dla określonej sesji, ale w przypadku, gdy dane nie wymagają stałego magazynu między sesjami.
* Dane sesji są usuwane w przypadku <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> wywołania implementacji lub czasu wygaśnięcia sesji.
* Nie istnieje domyślny mechanizm do powiadamiania kodu aplikacji o zamknięciu przeglądarki klienta lub po cookie usunięciu lub wygaśnięciu sesji na kliencie.
* Szablony ASP.NET Core MVC i Razor Pages zawierają obsługę ogólne rozporządzenie o ochronie danych (Rodo). Stan sesji cookie s nie jest domyślnie oznaczony jako istotny, więc stan sesji nie działa, jeśli śledzenie nie jest dozwolone przez odwiedzających witrynę. Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Nie należy przechowywać poufnych danych w stanie sesji. Użytkownik może nie zamknąć przeglądarki i wyczyścić tę sesję cookie . Niektóre przeglądarki utrzymują prawidłowe sesje cookie w oknach przeglądarki. Sesja może nie być ograniczona do pojedynczego użytkownika &mdash; , a następnym użytkownikiem może nadal przeglądać aplikację za pomocą tej samej sesji cookie .

Dostawca pamięci podręcznej w pamięci przechowuje dane sesji w pamięci serwera, na którym znajduje się aplikacja. W scenariuszu farmy serwerów:

* Użyj *sesji programu Sticky* , aby powiązać każdą sesję z określonym wystąpieniem aplikacji na pojedynczym serwerze. [Azure App Service](https://azure.microsoft.com/services/app-service/) używa [routingu żądań aplikacji (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) do wymuszania sesji usługi Sticky one domyślnie. Jednak sesje programu Sticky Notes mogą mieć wpływ na skalowalność i komplikują aktualizacje aplikacji sieci Web. Lepszym rozwiązaniem jest użycie rozproszonej pamięci podręcznej Redis lub SQL Server, która nie wymaga sesji programu Sticky Notes. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Sesja cookie jest szyfrowana za pośrednictwem programu <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . Ochrona danych musi być poprawnie skonfigurowana do odczytu sesji cookie s na poszczególnych komputerach. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction> i [dostawcy magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurowanie stanu sesji

Pakiet [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) , który jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), zapewnia oprogramowanie pośredniczące do zarządzania stanem sesji. Aby włączyć oprogramowanie pośredniczące sesji, `Startup` musi zawierać następujące polecenie:

* Dowolna pamięć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> podręczna pamięci. `IDistributedCache`Implementacja jest używana jako magazyn zapasowy dla sesji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Wywołanie <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> w `ConfigureServices` .
* Wywołanie <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> w `Configure` .

Poniższy kod pokazuje, jak skonfigurować dostawcę sesji w pamięci z domyślną implementacją w pamięci `IDistributedCache` :

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Kolejność oprogramowania pośredniczącego jest ważna. W poprzednim przykładzie `InvalidOperationException` występuje wyjątek, gdy `UseSession` jest wywoływany po `UseMvc` . Aby uzyskać więcej informacji, zobacz [porządkowanie oprogramowania pośredniczącego](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> jest dostępny po skonfigurowaniu stanu sesji.

`HttpContext.Session` nie można uzyskać dostępu przed `UseSession` wywołaniem.

Nie można utworzyć nowej sesji z nową sesją cookie , gdy aplikacja rozpoczęła zapisywanie w strumieniu odpowiedzi. Wyjątek jest rejestrowany w dzienniku serwera sieci Web i nie jest wyświetlany w przeglądarce.

### <a name="load-session-state-asynchronously"></a>Załaduj stan sesji asynchronicznie

Domyślny dostawca sesji w ASP.NET Core ładuje rekordy sesji z bazowego <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> magazynu zapasowego tylko wtedy, gdy <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> Metoda jest jawnie wywoływana przed <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> metodami, lub <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . Jeśli `LoadAsync` nie zostanie najpierw wywołana, źródłowy rekord sesji jest ładowany synchronicznie, co może spowodować spadek wydajności na dużą skalę.

Aby aplikacje wymuszają ten wzorzec, zawiń <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> <xref:Microsoft.AspNetCore.Session.DistributedSession> implementacje i w wersji, które zgłaszają wyjątek, jeśli `LoadAsync` Metoda nie jest wywoływana przed `TryGetValue` , `Set` , lub `Remove` . Zarejestruj opakowane wersje w kontenerze usługi.

### <a name="session-options"></a>Opcje sesji

Aby zastąpić wartości domyślne sesji, użyj <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Określa ustawienia używane do tworzenia cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> wartość domyślna to <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> wartość domyślna to <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> wartość domyślna to <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> wartość domyślna to `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> wartość domyślna to `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout`Wskazuje, jak długo sesja może być bezczynna, zanim jej zawartość zostanie porzucona. Każdy dostęp do sesji resetuje limit czasu. To ustawienie dotyczy tylko zawartości sesji, a nie cookie . Wartość domyślna to 20 minut. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Maksymalna ilość czasu, którą można załadować sesji ze sklepu lub zatwierdzić ją z powrotem do magazynu. To ustawienie może dotyczyć tylko operacji asynchronicznych. Ten limit czasu można wyłączyć za pomocą polecenia <xref:System.Threading.Timeout.InfiniteTimeSpan> . Wartość domyślna to 1 minuta. |

Sesja służy cookie do śledzenia i identyfikowania żądań z pojedynczej przeglądarki. Domyślnie cookie jest to nazwa `.AspNetCore.Session` i używa ścieżki `/` . Ponieważ cookie domyślnie nie określono domeny, nie jest ona dostępna dla skryptu po stronie klienta na stronie (ponieważ <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> Domyślnie jest to `true` ).

Aby zastąpić cookie wartości domyślne sesji, użyj `SessionOptions` :

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Aplikacja używa właściwości, <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> Aby określić, jak długo sesja może być bezczynna, zanim jej zawartość w pamięci podręcznej serwera zostanie porzucona. Ta właściwość jest niezależna od cookie daty wygaśnięcia. Każde żądanie przechodzące przez [oprogramowanie pośredniczące sesji](xref:Microsoft.AspNetCore.Session.SessionMiddleware) resetuje limit czasu.

Stan sesji to *nie jest blokowanie* . Jeśli dwa żądania jednocześnie próbują zmodyfikować zawartość sesji, ostatnie żądanie zastępuje pierwsze. `Session` Program jest implementowany jako *spójna sesja* , co oznacza, że cała zawartość jest przechowywana razem. Gdy dwa żądania poszukują modyfikacji różnych wartości sesji, ostatnie żądanie może zastąpić zmiany sesji wykonane przez pierwsze.

### <a name="set-and-get-session-values"></a>Ustawianie i pobieranie wartości sesji

Stan sesji jest dostępny z Razor klasy Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC <xref:Microsoft.AspNetCore.Mvc.Controller> klasy z <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Ta właściwość jest <xref:Microsoft.AspNetCore.Http.ISession> implementacją.

`ISession`Implementacja zawiera kilka metod rozszerzających, które umożliwiają ustawianie i pobieranie wartości całkowitych i ciągów. Metody rozszerzające znajdują się w <xref:Microsoft.AspNetCore.Http> przestrzeni nazw (Dodaj `using Microsoft.AspNetCore.Http;` instrukcję w celu uzyskania dostępu do metod rozszerzenia), gdy do projektu jest przywoływany pakiet [Microsoft. AspNetCore. http. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) . Oba pakiety są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

`ISession` metody rozszerzające:

* [Get (ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32 (ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString (ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32 (ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString (ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Poniższy przykład pobiera wartość sesji dla `IndexModel.SessionKeyName` klucza ( `_Name` w przykładowej aplikacji) na Razor stronie stron:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Poniższy przykład pokazuje, jak ustawić i pobrać liczbę całkowitą i ciąg:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Wszystkie dane sesji muszą być serializowane w celu włączenia scenariusza rozproszonej pamięci podręcznej, nawet w przypadku korzystania z pamięci podręcznej w pamięci. Serializatory ciągów i liczb całkowitych są udostępniane przez metody rozszerzające <xref:Microsoft.AspNetCore.Http.ISession> ). Typy złożone muszą być serializowane przez użytkownika przy użyciu innego mechanizmu, takiego jak JSON.

Dodaj następujące metody rozszerzające, aby ustawić i pobrać możliwe do serializacji obiekty:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Poniższy przykład pokazuje, jak ustawić i pobrać obiekt możliwy do serializacji przy użyciu metod rozszerzających:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core udostępnia Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) lub kontroler stron <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Ta właściwość przechowuje dane, dopóki nie zostanie odczytany w innym żądaniu. Metody [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) i [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) mogą służyć do badania danych bez usuwania na końcu żądania. Wartość [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) oznacza wszystkie elementy w słowniku do przechowywania. `TempData` jest szczególnie przydatne w przypadku przekierowywania, gdy dane są wymagane dla więcej niż jednego żądania. `TempData` jest zaimplementowany przez `TempData` dostawców przy użyciu cookie stanu s lub sesji.

## <a name="tempdata-samples"></a>Przykłady TempData

Rozważmy następującą stronę, która tworzy klienta:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Zostanie wyświetlona następująca strona `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

W powyższym znaczniku na końcu żądania `TempData["Message"]` **nie** jest usuwany, ponieważ `Peek` jest używany. Odświeżanie wyświetlanej strony `TempData["Message"]` .

Poniższe znaczniki przypominają poprzedni kod, ale używają `Keep` do zachowywania danych na końcu żądania:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Nawigowanie po stronach *IndexPeek* i *IndexKeep* nie zostanie usunięte `TempData["Message"]` .

Poniższy kod wyświetla `TempData["Message"]` , ale na końcu żądania, `TempData["Message"]` jest usuwany:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Dostawcy TempData

cookieDostawca TempData oparty na protokole jest używany domyślnie do przechowywania TempData w cookie s.

cookieDane są szyfrowane przy użyciu <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> kodowania z <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> , a następnie fragmenty. Ze względu na cookie to, że jest podzielony, cookie Limit pojedynczego rozmiaru znaleziony w ASP.NET Core 1. x nie ma zastosowania. cookieDane nie są kompresowane, ponieważ kompresowanie zaszyfrowanych danych może prowadzić do problemów z zabezpieczeniami, takich jak ataki w ramach [przestępczości](https://wikipedia.org/wiki/CRIME_(security_exploit)) i [naruszenia](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Aby uzyskać więcej informacji na temat cookie dostawcy TempData opartego na systemie, zobacz <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Wybierz dostawcę TempData

Wybór dostawcy TempData obejmuje kilka zagadnień, takich jak:

1. Czy aplikacja już używa stanu sesji? Jeśli tak, użycie dostawcy TempData stanu sesji nie ma dodatkowych kosztów dla aplikacji (poza rozmiarem danych).
2. Czy aplikacja będzie używać TempData tylko dla stosunkowo małych ilości danych (do 500 bajtów)? W takim przypadku cookie dostawca TempData dodaje niewielki koszt do każdego żądania, które przenosi TempData. W przeciwnym razie dostawca TempData stanu sesji może być korzystne, aby uniknąć jednoczesnego wyzwolenia dużej ilości danych w każdym żądaniu do momentu użycia TempData.
3. Czy aplikacja działa w farmie serwerów na wielu serwerach? W takim przypadku nie jest wymagana dodatkowa konfiguracja do korzystania z cookie dostawcy TempData poza ochroną danych (zobacz <xref:security/data-protection/introduction> i [dostawcy magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Większość klientów sieci Web (takich jak przeglądarki sieci Web) wymusza limity maksymalnego rozmiaru każdej z nich cookie , całkowitą liczbę cookie lub obie. W przypadku korzystania z cookie dostawcy TempData upewnij się, że aplikacja nie przekroczy tych limitów. Należy wziąć pod uwagę łączny rozmiar danych. Konto zwiększające cookie rozmiar ze względu na szyfrowanie i rozdzielenie.

### <a name="configure-the-tempdata-provider"></a>Konfigurowanie dostawcy TempData

cookieDostawca TempData oparty na programie jest domyślnie włączony.

Aby włączyć dostawcę TempData opartego na sesji, należy użyć <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> metody rozszerzenia:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Kolejność oprogramowania pośredniczącego jest ważna. W poprzednim przykładzie `InvalidOperationException` występuje wyjątek, gdy `UseSession` jest wywoływany po `UseMvc` . Aby uzyskać więcej informacji, zobacz [porządkowanie oprogramowania pośredniczącego](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Jeśli celem jest .NET Framework i użycie dostawcy TempData opartego na sesji, Dodaj pakiet [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) do projektu.

## <a name="query-strings"></a>Ciągi zapytań

Ograniczoną ilość danych można przekazywać z jednego żądania do innego przez dodanie go do ciągu zapytania nowego żądania. Jest to przydatne do przechwytywania stanu w sposób trwały, który umożliwia udostępnianie linków ze stanem osadzonym za pośrednictwem poczty e-mail lub sieci społecznościowych. Ponieważ ciągi zapytań URL są publiczne, nigdy nie używaj ciągów zapytań do poufnych danych.

Oprócz niezamierzonego udostępniania, w tym danych w ciągach zapytań, można tworzyć szanse na ataki [między lokacjami (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) , które mogą nakłonić użytkowników do odwiedzania złośliwych witryn podczas uwierzytelniania. Osoby atakujące mogą następnie ukraść dane użytkowników z aplikacji lub podejmować złośliwe działania w imieniu użytkownika. Wszelkie zachowane aplikacje lub Stany sesji muszą chronić przed atakami CSRF. Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Ukryte pola

Dane można zapisywać w ukrytych polach formularzy i ogłaszane przy użyciu następnego żądania. Jest to typowe w formularzach wielostronicowych. Ponieważ klient może potencjalnie naruszać dane, aplikacja musi zawsze ponownie sprawdzić poprawność danych przechowywanych w ukrytych polach.

## <a name="httpcontextitems"></a>HttpContext. Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>Kolekcja jest używana do przechowywania danych podczas przetwarzania pojedynczego żądania. Zawartość kolekcji jest odrzucana po przetworzeniu żądania. `Items`Kolekcja jest często używana do zezwalania składnikom lub oprogramowaniu pośredniczącemu na komunikowanie się, gdy pracują w różnych punktach w czasie w trakcie żądania i nie mają bezpośredniego sposobu przekazywania parametrów.

W poniższym przykładzie [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) dodaje `isVerified` do `Items` kolekcji.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

W dalszej części potoku inne oprogramowanie pośredniczące może uzyskać dostęp do wartości `isVerified` :

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

W przypadku oprogramowania pośredniczącego, które jest używane tylko przez pojedynczą aplikację, `string` klucze są akceptowalne. Oprogramowanie pośredniczące współużytkowane przez wystąpienia aplikacji powinno używać unikatowych kluczy obiektów, aby uniknąć kolizji kluczy. Poniższy przykład pokazuje, jak używać unikatowego klucza obiektu zdefiniowanego w klasie pośredniczącej:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Inny kod może uzyskać dostęp do wartości przechowywanej `HttpContext.Items` przy użyciu klucza uwidacznianego przez klasę pośredniczącą:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Takie podejście ma również zalety wyeliminowania używania ciągów kluczy w kodzie.

## <a name="cache"></a>Pamięć podręczna

Buforowanie jest wydajnym sposobem przechowywania i pobierania danych. Aplikacja może kontrolować okres istnienia elementów w pamięci podręcznej.

Dane w pamięci podręcznej nie są skojarzone z określonym żądaniem, użytkownikiem lub sesją. **Należy zachować ostrożność, aby nie buforować danych specyficznych dla użytkownika, które mogą być pobierane przez inne żądania użytkowników.**

Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

Użyj [iniekcji zależności](xref:fundamentals/dependency-injection) , aby udostępnić dane wszystkim użytkownikom:

1. Zdefiniuj usługę zawierającą dane. Na przykład Klasa o nazwie `MyAppData` jest zdefiniowana:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Dodaj klasę usługi do `Startup.ConfigureServices` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Korzystaj z klasy usługi danych:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Typowe błędy

* "Nie można rozpoznać usługi dla typu" Microsoft. Extensions. buforowania. Distributed. IDistributedCache "podczas próby aktywowania elementu" Microsoft. AspNetCore. Session. DistributedSessionStore "."

  Jest to zazwyczaj spowodowane niepowodzeniem konfigurowania co najmniej jednej `IDistributedCache` implementacji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed> i <xref:performance/caching/memory>.

* W przypadku niepowodzenia utrwalania sesji przez oprogramowanie pośredniczące sesji (na przykład jeśli magazyn zapasowy nie jest dostępny), oprogramowanie pośredniczące rejestruje wyjątek, a żądanie nadal odbywa się normalnie. Prowadzi to do nieprzewidywalnego zachowania.

  Na przykład użytkownik przechowuje koszyk w sesji. Użytkownik dodaje element do koszyka, ale zatwierdzanie kończy się niepowodzeniem. Aplikacja nie wie o niepowodzeniu, dlatego zgłasza użytkownikowi informacje o tym, że element został dodany do swojego koszyka, co nie jest prawdziwe.

  Zalecanym podejściem do sprawdzenia pod kątem błędów jest wywoływanie `await feature.Session.CommitAsync();` kodu aplikacji, gdy aplikacja zakończy zapisywanie w sesji. `CommitAsync` zgłasza wyjątek, jeśli magazyn zapasowy jest niedostępny. Jeśli `CommitAsync` to się nie powiedzie, aplikacja może przetworzyć wyjątek. `LoadAsync` zgłasza w tych samych warunkach, w których magazyn danych jest niedostępny.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR i stan sesji

SignalR aplikacje nie powinny używać stanu sesji do przechowywania informacji. SignalR aplikacje mogą przechowywać stan dla połączenia w `Context.Items` centrum. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Dodatkowe zasoby

<xref:host-and-deploy/web-farm>
::: moniker-end
