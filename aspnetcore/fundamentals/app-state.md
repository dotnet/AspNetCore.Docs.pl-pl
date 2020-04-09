---
title: Sesja w ASP.NET Core
author: rick-anderson
description: Odnajduj podejścia do zachowania sesji między żądaniami.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 85d2a418c3aaae40bbcdc040095c2c98d4b7242c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80640042"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Zarządzanie sesją i stanem w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Przez [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5)i Diana [LaRose](https://github.com/DianaLaRose)

HTTP jest protokołem bezstanowym. Domyślnie żądania HTTP są niezależnymi wiadomościami, które nie zachowują wartości użytkownika. W tym artykule opisano kilka podejść do zachowania danych użytkownika między żądaniami.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Zarządzanie stanem

stan może być przechowywany przy użyciu kilku metod. Każde podejście jest opisane w dalszej części tego tematu.

| Podejście do przechowywania danych | Mechanizm przechowywania |
| ---------------- | ----------------- |
| [Plik cookie](#cookies) | Pliki cookie HTTP. Może zawierać dane przechowywane przy użyciu kodu aplikacji po stronie serwera. |
| [Stan sesji](#session-state) | Pliki cookie HTTP i kod aplikacji po stronie serwera |
| [TempData ( TempData )](#tempdata) | Pliki cookie HTTP lub stan sesji |
| [Ciągi zapytań](#query-strings) | Ciągi zapytań HTTP |
| [Pola ukryte](#hidden-fields) | Pola formularza HTTP |
| [HttpContext.Items](#httpcontextitems) | Kod aplikacji po stronie serwera |
| [Pamięć podręczna](#cache) | Kod aplikacji po stronie serwera |

## <a name="cookies"></a>Pliki cookie

Pliki cookie przechowują dane w różnych żądaniach. Ponieważ pliki cookie są wysyłane przy każdym żądaniu, ich rozmiar powinien być minimalny. W idealnym przypadku tylko identyfikator powinien być przechowywany w pliku cookie z danymi przechowywanymi przez aplikację. Większość przeglądarek ogranicza rozmiar pliku cookie do 4096 bajtów. Dla każdej domeny dostępna jest tylko ograniczona liczba plików cookie.

Ponieważ pliki cookie podlegają manipulacjom, muszą zostać zweryfikowane przez aplikację. Pliki cookie mogą być usuwane przez użytkowników i wygasają na klientach. Jednak pliki cookie są zazwyczaj najbardziej trwałą formą trwałości danych na kliencie.

Pliki cookie są często używane do personalizacji, gdzie zawartość jest dostosowana do znanego użytkownika. Użytkownik jest identyfikowany i nie jest uwierzytelniany tylko w większości przypadków. Plik cookie może przechowywać nazwę użytkownika, nazwę konta lub unikatowy identyfikator użytkownika, taki jak identyfikator GUID. Plik cookie może służyć do uzyskiwania dostępu do spersonalizowanych ustawień użytkownika, takich jak preferowany kolor tła witryny.

Podczas wydawania plików cookie i rozwiązywania problemów związanych z prywatnością należy zapoznać się z [ogólnym rozporządzeniem Unii Europejskiej o ochronie danych (RODO).](https://ec.europa.eu/info/law/law-topic/data-protection) Aby uzyskać więcej informacji, zobacz [Ogólne rozporządzenie o ochronie danych (RODO) w ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stan sesji

Stan sesji jest ASP.NET Podstawowy scenariusz przechowywania danych użytkownika podczas przeglądania aplikacji sieci web przez użytkownika. Stan sesji używa magazynu obsługiwanego przez aplikację do utrwalania danych między żądaniami od klienta. Dane sesji są wspierane przez pamięć podręczną i uważane za dane efemeryczne. Witryna powinna nadal działać bez danych sesji. Krytyczne dane aplikacji powinny być przechowywane w bazie danych użytkownika i buforowane w sesji tylko jako optymalizacja wydajności.

Sesja nie jest obsługiwana w aplikacjach [SignalR,](xref:signalr/index) ponieważ [Centrum SignalR](xref:signalr/hubs) może być wykonywane niezależnie od kontekstu HTTP. Na przykład może to nastąpić, gdy długie żądanie sondowania jest otwarte przez koncentrator poza okres istnienia kontekstu HTTP żądania.

ASP.NET Core utrzymuje stan sesji, udostępniając klientowi plik cookie zawierający identyfikator sesji. Identyfikator sesji plików cookie:

* Jest wysyłany do aplikacji z każdym żądaniem.
* Jest używany przez aplikację do pobierania danych sesji.

Stan sesji wykazuje następujące zachowania:

* Plik cookie sesji jest specyficzny dla przeglądarki. Sesje nie są udostępniane w przeglądarkach.
* Sesyjne pliki cookie są usuwane po zakończeniu sesji przeglądarki.
* Jeśli plik cookie zostanie odebrany dla wygasłej sesji, tworzona jest nowa sesja, która używa tego samego pliku cookie sesji.
* Puste sesje nie są zachowywane. Sesja musi mieć co najmniej jedną wartość ustawioną, aby utrwalić sesję między żądaniami. Gdy sesja nie jest zachowywana, dla każdego nowego żądania jest generowany nowy identyfikator sesji.
* Aplikacja zachowuje sesję przez ograniczony czas po ostatnim żądaniu. Aplikacja ustawia limit czasu sesji lub używa domyślnej wartości 20 minut. Stan sesji jest idealny do przechowywania danych użytkownika:
  * To jest specyficzne dla konkretnej sesji.
  * Gdzie dane nie wymagają stałego przechowywania w sesjach.
* Dane sesji są usuwane po wywołaniu implementacji [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) lub po wygaśnięciu sesji.
* Nie ma domyślnego mechanizmu informowania kodu aplikacji, że przeglądarka klienta została zamknięta lub gdy plik cookie sesji zostanie usunięty lub wygasł na kliencie.
* Pliki cookie stanu sesji nie są domyślnie oznaczone jako niezbędne. Stan sesji nie jest funkcjonalny, chyba że śledzenie jest dozwolone przez odwiedzającego witrynę. Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Nie przechowuj poufnych danych w stanie sesji. Użytkownik może nie zamknąć przeglądarki i wyczyścić plik cookie sesji. Niektóre przeglądarki przechowują prawidłowe pliki cookie sesji w oknach przeglądarki. Sesja może nie być ograniczona do pojedynczego użytkownika. Następny użytkownik może kontynuować przeglądanie aplikacji za pomocą tego samego pliku cookie sesji.

Dostawca pamięci podręcznej w pamięci pamięci przechowuje dane sesji w pamięci serwera, na którym znajduje się aplikacja. W scenariuszu farmy serwerów:

* Użyj *sesji przyklejonych,* aby powiązać każdą sesję z określonym wystąpieniem aplikacji na poszczególnym serwerze. [Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) domyślnie używa [routingu żądań aplikacji (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) do wymuszania sesji przyklejonych. Jednak sesje lepkie mogą wpływać na skalowalność i komplikować aktualizacje aplikacji sieci web. Lepszym rozwiązaniem jest użycie rozproszonej pamięci podręcznej Redis lub SQL Server, która nie wymaga sesji przyklejonych. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Plik cookie sesji jest szyfrowany za pośrednictwem [protokołu IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). Ochrona danych musi być odpowiednio skonfigurowana do odczytu plików cookie sesji na każdym komputerze. Aby uzyskać więcej <xref:security/data-protection/introduction> informacji, zobacz i [Dostawcy magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurowanie stanu sesji

Pakiet [Microsoft.AspNetCore.Session:](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/)

* Jest dorozumiany przez ramy.
* Udostępnia oprogramowanie pośredniczące do zarządzania stanem sesji.

Aby włączyć oprogramowanie pośredniczące sesji, `Startup` musi zawierać:

* Dowolna pamięć [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) jest buforowana. Implementacja `IDistributedCache` jest używana jako zapas zapasowy dla sesji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Wywołanie [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) `ConfigureServices`w .
* Wywołanie [useSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) `Configure`w .

Poniższy kod pokazuje, jak skonfigurować dostawcę sesji w pamięci `IDistributedCache`z domyślną implementacją w pamięci:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,39)]

Poprzedni kod ustawia krótki limit czasu, aby uprościć testowanie.

Kolejność oprogramowania pośredniczącego jest ważna.  Zadzwoń `UseSession` `UseRouting` po `UseEndpoints`i przed . Zobacz [Zamawianie oprogramowania pośredniczącego](xref:fundamentals/middleware/index#order).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) jest dostępna po skonfigurowaniu stanu sesji.

`HttpContext.Session`nie można uzyskać dostępu `UseSession` przed został wywołany.

Nie można utworzyć nowej sesji z nowym plikiem cookie sesji po rozpoczęciu zapisywania aplikacji do strumienia odpowiedzi. Wyjątek jest rejestrowany w dzienniku serwera sieci web i nie jest wyświetlany w przeglądarce.

### <a name="load-session-state-asynchronously"></a>Ładowanie stanu sesji asynchronicznie

Domyślny dostawca sesji w ASP.NET Core ładuje rekordy sesji z podstawowej sieci zapasów [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) tylko wtedy, gdy metoda [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) jest jawnie wywoływana przed [metodyą TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set)lub [Remove.](/dotnet/api/microsoft.aspnetcore.http.isession.remove) Jeśli `LoadAsync` nie jest wywoływana jako pierwsza, podstawowy rekord sesji jest ładowany synchronicznie, co może spowodować obciążenie wydajnością na dużą skalę.

Aby aplikacje wymusiły ten wzorzec, zawiń [implementacje DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) i `LoadAsync` [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) wersjami, które zgłaszają wyjątek, jeśli metoda nie jest wywoływana przed `TryGetValue`, `Set`lub `Remove`. Zarejestruj opakowane wersje w kontenerze usług.

### <a name="session-options"></a>Opcje sesji

Aby zastąpić domyślne ustawienia sesji, użyj [funkcji SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Opcja | Opis |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Określa ustawienia używane do tworzenia pliku cookie. [Nazwa](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) domyślna na [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). [Domyślnie](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) ścieżka ma [wartość SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) domyślnie to [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) domyślnie `true`. [Domyślne ustawienia domyślne isessential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) to `false`. |
| [Idletimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Wskazuje, `IdleTimeout` jak długo sesja może być bezczynna, zanim jej zawartość zostanie porzucona. Każdy dostęp do sesji resetuje limit czasu. To ustawienie dotyczy tylko zawartości sesji, a nie pliku cookie. Wartość domyślna to 20 minut. |
| [Czas IO](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Maksymalny czas dowolny załadować sesję ze sklepu lub zatwierdzić ją z powrotem do magazynu. To ustawienie może dotyczyć tylko operacji asynchronicznych. Ten limit czasu można wyłączyć za pomocą [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Wartość domyślna to 1 minuta. |

Sesja używa pliku cookie do śledzenia i identyfikowania żądań z jednej przeglądarki. Domyślnie ten plik `.AspNetCore.Session`cookie ma nazwę , `/`i używa ścieżki . Ponieważ domyślny plik cookie nie określa domeny, nie jest on udostępniany skryptowi po stronie klienta `true`na stronie (ponieważ [domyślnie httpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) jest domyślny).

Aby zastąpić domyślne ustawienia sesji <xref:Microsoft.AspNetCore.Builder.SessionOptions>plików cookie, należy użyć:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Aplikacja używa [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) właściwość, aby określić, jak długo sesja może być bezczynny, zanim jego zawartość w pamięci podręcznej serwera są porzucone. Ta właściwość jest niezależna od wygaśnięcia pliku cookie. Każde żądanie, które przechodzi przez [oprogramowanie pośredniczące sesji](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) resetuje limit czasu.

Stan sesji *nie blokuje*się . Jeśli dwa żądania jednocześnie próbują zmodyfikować zawartość sesji, ostatnie żądanie zastępuje pierwsze. `Session`jest realizowany jako *spójna sesja,* co oznacza, że cała zawartość jest przechowywana razem. Gdy dwa żądania mają na celu zmodyfikowanie różnych wartości sesji, ostatnie żądanie może zastąpić zmiany sesji wprowadzone przez pierwszy.

### <a name="set-and-get-session-values"></a>Ustawianie i ustalanie wartości sesji

Stan sesji jest dostępny z klasy Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) lub [MVC Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) z [httpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Ta właściwość jest implementacją [ISession.](/dotnet/api/microsoft.aspnetcore.http.isession)

Implementacja `ISession` zawiera kilka metod rozszerzenia, aby ustawić i pobrać wartości całkowite i ciąg. Metody rozszerzenia znajdują się w obszarze nazw [Microsoft.AspNetCore.Http.](/dotnet/api/microsoft.aspnetcore.http)

`ISession`metody rozszerzenia:

* [Pobierz(ISession, Ciąg)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, Ciąg)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, Ciąg)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Poniższy przykład pobiera wartość sesji `IndexModel.SessionKeyName` dla`_Name` klucza (w przykładowej aplikacji) na stronie Strony Razor:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

W poniższym przykładzie pokazano, jak ustawić i uzyskać liczbę całkowitą i ciąg:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Wszystkie dane sesji muszą być serializowane, aby włączyć scenariusz rozproszonej pamięci podręcznej, nawet podczas korzystania z pamięci podręcznej w pamięci podręcznej. Serializatory ciągów i liczby całkowitej są dostarczane przez metody rozszerzenia [ISession](/dotnet/api/microsoft.aspnetcore.http.isession). Typy złożone muszą być serializowane przez użytkownika przy użyciu innego mechanizmu, takiego jak JSON.

Użyj następującego przykładowego kodu do serializacji obiektów:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

W poniższym przykładzie pokazano, jak ustawić i `SessionExtensions` uzyskać serializable obiektu z klasy:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData ( TempData )

ASP.NET Core udostępnia razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) lub <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>kontrolera . Ta właściwość przechowuje dane, dopóki nie jest odczytywany w innym żądaniu. [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) i [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) metody mogą służyć do badania danych bez usuwania na końcu żądania. [Zachowaj](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) znaczniki wszystkich elementów w słowniku do przechowywania. `TempData`Jest:

* Przydatne do przekierowywania, gdy dane są wymagane dla więcej niż jednego żądania.
* Zaimplementowane przez `TempData` dostawców przy użyciu plików cookie lub stanu sesji.

## <a name="tempdata-samples"></a>Próbki TempData

Rozważmy następującą stronę, która tworzy klienta:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Na następującej `TempData["Message"]`stronie wyświetlane są:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

W poprzednim znaczników, na końcu żądania, `TempData["Message"]` **nie** jest `Peek` usuwany, ponieważ jest używany. Odświeżenie strony powoduje wyświetlenie zawartości pliku `TempData["Message"]`.

Następujące znaczniki `Keep` są podobne do poprzedniego kodu, ale służy do zachowania danych na końcu żądania:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Nawigowanie między stronami *IndexPeek* i *IndexKeep* nie spowoduje usunięcia `TempData["Message"]`.

Następujący kod `TempData["Message"]`jest wyświetlany , ale na `TempData["Message"]` końcu żądania, jest usuwany:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Dostawcy TempData

Dostawca tempdata oparty na plikach cookie jest domyślnie używany do przechowywania tempdata w plikach cookie.

Dane plików cookie są szyfrowane przy użyciu [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), zakodowane [base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), a następnie fragmentaryczne. Maksymalny rozmiar pliku cookie jest mniejszy niż [4096 bajtów](http://www.faqs.org/rfcs/rfc2965.html) z powodu szyfrowania i fragmentowania. Dane plików cookie nie są kompresowane, ponieważ kompresja zaszyfrowanych danych może prowadzić do problemów z [bezpieczeństwem,](https://wikipedia.org/wiki/CRIME_(security_exploit)) takich jak ataki CRIME i [BREACH.](https://wikipedia.org/wiki/BREACH_(security_exploit)) Aby uzyskać więcej informacji na temat dostawcy tempdata opartego na plikach cookie, zobacz [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Wybierz dostawcę tempdata

Wybór dostawcy TempData obejmuje kilka zagadnień, takich jak:

* Czy aplikacja już używa stanu sesji? Jeśli tak, przy użyciu dostawcy TempData stanu sesji nie ma żadnych dodatkowych kosztów dla aplikacji poza rozmiar danych.
* Czy aplikacja używa TempData tylko oszczędnie dla stosunkowo niewielkich ilości danych, do 500 bajtów? Jeśli tak, dostawca pliku cookie TempData dodaje niewielki koszt do każdego żądania, które prowadzi TempData. Jeśli nie, dostawca tempdata stanu sesji może być korzystne, aby uniknąć zaokrąglania dużej ilości danych w każdym żądaniu, dopóki tempdata jest zużywana.
* Czy aplikacja działa w farmie serwerów na wielu serwerach? Jeśli tak, nie ma dodatkowej konfiguracji wymaganej do korzystania z dostawcy <xref:security/data-protection/introduction> plików cookie TempData poza ochroną danych (zobacz i [dostawcy magazynu kluczy).](xref:security/data-protection/implementation/key-storage-providers)

Większość klientów internetowych, takich jak przeglądarki internetowe, wymusza ograniczenia dotyczące maksymalnego rozmiaru każdego pliku cookie i całkowitej liczby plików cookie. Korzystając z dostawcy pliku cookie TempData, sprawdź, czy aplikacja nie przekroczy [tych limitów.](http://www.faqs.org/rfcs/rfc2965.html) Należy wziąć pod uwagę całkowity rozmiar danych. Uwzględnij wzrost rozmiaru pliku cookie z powodu szyfrowania i fragmentowania.

### <a name="configure-the-tempdata-provider"></a>Konfigurowanie dostawcy tempdata

Dostawca tempdata oparty na plikach cookie jest domyślnie włączony.

Aby włączyć dostawcę tempdata opartego na sesji, użyj metody rozszerzenia [AddSessionStateTempDataProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) Wymagane `AddSessionStateTempDataProvider` jest tylko jedno wywołanie:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,30)]

## <a name="query-strings"></a>Ciągi zapytań

Ograniczona ilość danych mogą być przekazywane z jednego żądania do innego, dodając go do ciągu kwerendy nowego żądania. Jest to przydatne do przechwytywania stanu w sposób trwały, który umożliwia łącza ze stanem osadzonym do udostępniania za pośrednictwem poczty e-mail lub sieci społecznościowych. Ponieważ ciągi zapytań adres url są publiczne, nigdy nie używaj ciągów zapytań dla poufnych danych.

Oprócz niezamierzonego udostępniania, w tym danych w ciągach zapytań może uwidocznić aplikację do ataków [forgery żądania między witrynami (CSRF).](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) Każdy zachowany stan sesji musi chronić przed atakami CSRF. Aby uzyskać więcej informacji, zobacz [Zapobieganie atakom xsrf/CSRF (Cross-Site Request Fałszerstwa" ( XSRF/CSRF) (Zapobieganie atakom wielomiejscowym).](xref:security/anti-request-forgery)

## <a name="hidden-fields"></a>Pola ukryte

Dane można zapisywać w ukrytych polach formularzy i księgować z powrotem w następnym żądaniu. Jest to powszechne w formularzach wielostronicowych. Ponieważ klient może potencjalnie manipulować danymi, aplikacja musi zawsze ponownie zaatrować dane przechowywane w ukrytych polach.

## <a name="httpcontextitems"></a>HttpContext.Items

Kolekcja [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) służy do przechowywania danych podczas przetwarzania pojedynczego żądania. Zawartość kolekcji są odrzucane po przetworzeniu żądania. Kolekcja `Items` jest często używana do zezwalania na składniki lub oprogramowanie pośredniczące do komunikowania się, gdy działają w różnych punktach w czasie podczas żądania i nie mają bezpośredniego sposobu przekazywania parametrów.

W poniższym przykładzie `isVerified` oprogramowanie `Items` [pośredniczące](xref:fundamentals/middleware/index) dodaje do kolekcji:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

W przypadku oprogramowania pośredniczącego używanego `string` tylko w jednej aplikacji dopuszczalne są stałe klucze. Oprogramowanie pośredniczące współużytkowane między aplikacjami powinno używać unikatowych kluczy obiektów, aby uniknąć kolizji kluczy. W poniższym przykładzie pokazano, jak używać unikatowego klucza obiektu zdefiniowanego w klasie oprogramowania pośredniczącego:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Inny kod może uzyskać `HttpContext.Items` dostęp do wartości przechowywanej przy użyciu klucza udostępnianego przez klasę oprogramowania pośredniczącego:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Takie podejście ma również tę zaletę, eliminując użycie ciągów kluczy w kodzie.

## <a name="cache"></a>Pamięć podręczna

Buforowanie to skuteczny sposób przechowywania i pobierania danych. Aplikacja może kontrolować okres istnienia elementów w pamięci podręcznej. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response>.

Buforowane dane nie są skojarzone z określonym żądaniem, użytkownikiem ani sesją. **Nie buforuj danych specyficznych dla użytkownika, które mogą być pobierane przez inne żądania użytkownika.**

Aby buforować dane <xref:performance/caching/memory>dla szerokich aplikacji, zobacz .

## <a name="common-errors"></a>Typowe błędy

* "Nie można rozpoznać usługi dla typu 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' podczas próby aktywowania 'Microsoft.AspNetCore.Session.DistributedSessionStore'".

  Jest to zazwyczaj spowodowane niepowodzeniem konfiguracji `IDistributedCache` co najmniej jednej implementacji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed> i <xref:performance/caching/memory>.

Jeśli oprogramowanie pośredniczące sesji nie może utrwalić sesji:

* Oprogramowanie pośredniczące rejestruje wyjątek i żądanie jest kontynuowane normalnie.
* Prowadzi to do nieprzewidywalnego zachowania.

Oprogramowanie pośredniczące sesji może nie utrwalić sesji, jeśli zapas zapasu nie jest dostępny. Na przykład użytkownik przechowuje koszyk w sesji. Użytkownik dodaje element do koszyka, ale zatwierdzenie kończy się niepowodzeniem. Aplikacja nie wie o awarii, więc zgłasza użytkownikowi, że element został dodany do koszyka, co nie jest prawdą.

Zalecane podejście do sprawdzania błędów `await feature.Session.CommitAsync` jest wywołanie, gdy aplikacja jest wykonywana pisanie do sesji. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>zgłasza wyjątek, jeśli magazyn zapasowy jest niedostępny. Jeśli `CommitAsync` nie powiedzie się, aplikacja może przetworzyć wyjątek. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>w tych samych warunkach, gdy magazyn danych jest niedostępny.
  
## <a name="signalr-and-session-state"></a>SignalR i stan sesji

Aplikacje SignalR nie powinny używać stanu sesji do przechowywania informacji. Aplikacje SignalR mogą przechowywać stan połączenia w `Context.Items` centrum. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Zasoby dodatkowe

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Rick [Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), Diana [LaRose](https://github.com/DianaLaRose)i [Luke Latham](https://github.com/guardrex)

HTTP jest protokołem bezstanowym. Bez podejmowania dodatkowych kroków żądania HTTP są niezależnymi wiadomościami, które nie zachowują wartości użytkownika ani stanu aplikacji. W tym artykule opisano kilka podejść do zachowania danych użytkownika i stanu aplikacji między żądaniami.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Zarządzanie stanem

stan może być przechowywany przy użyciu kilku metod. Każde podejście jest opisane w dalszej części tego tematu.

| Podejście do przechowywania danych | Mechanizm przechowywania |
| ---------------- | ----------------- |
| [Plik cookie](#cookies) | Pliki cookie HTTP (mogą zawierać dane przechowywane przy użyciu kodu aplikacji po stronie serwera) |
| [Stan sesji](#session-state) | Pliki cookie HTTP i kod aplikacji po stronie serwera |
| [TempData ( TempData )](#tempdata) | Pliki cookie HTTP lub stan sesji |
| [Ciągi zapytań](#query-strings) | Ciągi zapytań HTTP |
| [Pola ukryte](#hidden-fields) | Pola formularza HTTP |
| [HttpContext.Items](#httpcontextitems) | Kod aplikacji po stronie serwera |
| [Pamięć podręczna](#cache) | Kod aplikacji po stronie serwera |
| [Wstrzyknięcie zależności](#dependency-injection) | Kod aplikacji po stronie serwera |

## <a name="cookies"></a>Pliki cookie

Pliki cookie przechowują dane w różnych żądaniach. Ponieważ pliki cookie są wysyłane przy każdym żądaniu, ich rozmiar powinien być minimalny. W idealnym przypadku tylko identyfikator powinien być przechowywany w pliku cookie z danymi przechowywanymi przez aplikację. Większość przeglądarek ogranicza rozmiar pliku cookie do 4096 bajtów. Dla każdej domeny dostępna jest tylko ograniczona liczba plików cookie.

Ponieważ pliki cookie podlegają manipulacjom, muszą zostać zweryfikowane przez aplikację. Pliki cookie mogą być usuwane przez użytkowników i wygasają na klientach. Jednak pliki cookie są zazwyczaj najbardziej trwałą formą trwałości danych na kliencie.

Pliki cookie są często używane do personalizacji, gdzie zawartość jest dostosowana do znanego użytkownika. Użytkownik jest identyfikowany i nie jest uwierzytelniany tylko w większości przypadków. Plik cookie może przechowywać nazwę użytkownika, nazwę konta lub unikatowy identyfikator użytkownika (np. identyfikator GUID). Następnie możesz użyć pliku cookie, aby uzyskać dostęp do spersonalizowanych ustawień użytkownika, takich jak preferowany kolor tła witryny.

Należy pamiętać o [ogólnych przepisach o ochronie danych (RODO) unii europejskiej](https://ec.europa.eu/info/law/law-topic/data-protection) przy wydawaniu plików cookie i rozwiązywaniu problemów związanych z prywatnością. Aby uzyskać więcej informacji, zobacz [Ogólne rozporządzenie o ochronie danych (RODO) w ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stan sesji

Stan sesji jest ASP.NET Podstawowy scenariusz przechowywania danych użytkownika podczas przeglądania aplikacji sieci web przez użytkownika. Stan sesji używa magazynu obsługiwanego przez aplikację do utrwalania danych między żądaniami od klienta. Dane sesji są wspierane przez pamięć podręczną&mdash;i uważane za dane efemeryczne, witryna powinna nadal działać bez danych sesji. Krytyczne dane aplikacji powinny być przechowywane w bazie danych użytkownika i buforowane w sesji tylko jako optymalizacja wydajności.

> [!NOTE]
> Sesja nie jest obsługiwana w aplikacjach [SignalR,](xref:signalr/index) ponieważ [Centrum SignalR](xref:signalr/hubs) może być wykonywane niezależnie od kontekstu HTTP. Na przykład może to nastąpić, gdy długie żądanie sondowania jest otwarte przez koncentrator poza okres istnienia kontekstu HTTP żądania.

ASP.NET Core utrzymuje stan sesji, udostępniając klientowi plik cookie zawierający identyfikator sesji, który jest wysyłany do aplikacji przy każdym żądaniu. Aplikacja używa identyfikatora sesji do pobierania danych sesji.

Stan sesji wykazuje następujące zachowania:

* Ponieważ plik cookie sesji jest specyficzny dla przeglądarki, sesje nie są udostępniane w przeglądarkach.
* Sesyjne pliki cookie są usuwane po zakończeniu sesji przeglądarki.
* Jeśli plik cookie zostanie odebrany dla wygasłej sesji, tworzona jest nowa sesja, która używa tego samego pliku cookie sesji.
* Puste sesje nie są&mdash;zachowywane sesja musi mieć co najmniej jedną wartość ustawioną do utrwalenia sesji między żądaniami. Gdy sesja nie jest zachowywana, dla każdego nowego żądania jest generowany nowy identyfikator sesji.
* Aplikacja zachowuje sesję przez ograniczony czas po ostatnim żądaniu. Aplikacja ustawia limit czasu sesji lub używa domyślnej wartości 20 minut. Stan sesji jest idealny do przechowywania danych użytkownika, które są specyficzne dla określonej sesji, ale gdzie dane nie wymagają stałego magazynu w sesjach.
* Dane sesji są usuwane po wywołaniu implementacji [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) lub po wygaśnięciu sesji.
* Nie ma domyślnego mechanizmu informowania kodu aplikacji, że przeglądarka klienta została zamknięta lub gdy plik cookie sesji zostanie usunięty lub wygasł na kliencie.
* Szablony stron ASP.NET Core MVC i Razor obejmują obsługę ogólnego rozporządzenia o ochronie danych (RODO). Pliki cookie stanu sesji nie są domyślnie oznaczone jako niezbędne, więc stan sesji nie działa, chyba że śledzenie jest dozwolone przez odwiedzającego witrynę. Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Nie przechowuj poufnych danych w stanie sesji. Użytkownik może nie zamknąć przeglądarki i wyczyścić plik cookie sesji. Niektóre przeglądarki przechowują prawidłowe pliki cookie sesji w oknach przeglądarki. Sesja może nie być ograniczona&mdash;do pojedynczego użytkownika, a następny użytkownik może kontynuować przeglądanie aplikacji za pomocą tego samego pliku cookie sesji.

Dostawca pamięci podręcznej w pamięci pamięci przechowuje dane sesji w pamięci serwera, na którym znajduje się aplikacja. W scenariuszu farmy serwerów:

* Użyj *sesji przyklejonych,* aby powiązać każdą sesję z określonym wystąpieniem aplikacji na poszczególnym serwerze. [Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) domyślnie używa [routingu żądań aplikacji (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) do wymuszania sesji przyklejonych. Jednak sesje lepkie mogą wpływać na skalowalność i komplikować aktualizacje aplikacji sieci web. Lepszym rozwiązaniem jest użycie rozproszonej pamięci podręcznej Redis lub SQL Server, która nie wymaga sesji przyklejonych. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Plik cookie sesji jest szyfrowany za pośrednictwem [protokołu IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). Ochrona danych musi być odpowiednio skonfigurowana do odczytu plików cookie sesji na każdym komputerze. Aby uzyskać więcej <xref:security/data-protection/introduction> informacji, zobacz i [Dostawcy magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurowanie stanu sesji

Pakiet [Microsoft.AspNetCore.Session,](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) który znajduje się w [metapakiecie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)udostępnia oprogramowanie pośredniczące do zarządzania stanem sesji. Aby włączyć oprogramowanie pośredniczące sesji, `Startup` musi zawierać:

* Dowolna pamięć [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) jest buforowana. Implementacja `IDistributedCache` jest używana jako zapas zapasowy dla sesji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.
* Wywołanie [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) `ConfigureServices`w .
* Wywołanie [useSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) `Configure`w .

Poniższy kod pokazuje, jak skonfigurować dostawcę sesji w pamięci `IDistributedCache`z domyślną implementacją w pamięci:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Kolejność oprogramowania pośredniczącego jest ważna. W poprzednim przykładzie `InvalidOperationException` wyjątek `UseSession` występuje, gdy `UseMvc`jest wywoływany po . Aby uzyskać więcej informacji, zobacz [Oprogramowanie pośredniczące Zamawianie](xref:fundamentals/middleware/index#order).

[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) jest dostępna po skonfigurowaniu stanu sesji.

`HttpContext.Session`nie można uzyskać dostępu `UseSession` przed został wywołany.

Nie można utworzyć nowej sesji z nowym plikiem cookie sesji po rozpoczęciu zapisywania aplikacji do strumienia odpowiedzi. Wyjątek jest rejestrowany w dzienniku serwera sieci web i nie jest wyświetlany w przeglądarce.

### <a name="load-session-state-asynchronously"></a>Ładowanie stanu sesji asynchronicznie

Domyślny dostawca sesji w ASP.NET Core ładuje rekordy sesji z podstawowej sieci zapasów [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) tylko wtedy, gdy metoda [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) jest jawnie wywoływana przed [metodyą TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set)lub [Remove.](/dotnet/api/microsoft.aspnetcore.http.isession.remove) Jeśli `LoadAsync` nie jest wywoływana jako pierwsza, podstawowy rekord sesji jest ładowany synchronicznie, co może spowodować obciążenie wydajnością na dużą skalę.

Aby aplikacje wymusiły ten wzorzec, zawiń [implementacje DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) i `LoadAsync` [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) wersjami, które zgłaszają wyjątek, jeśli metoda nie jest wywoływana przed `TryGetValue`, `Set`lub `Remove`. Zarejestruj opakowane wersje w kontenerze usług.

### <a name="session-options"></a>Opcje sesji

Aby zastąpić domyślne ustawienia sesji, użyj [funkcji SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Opcja | Opis |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Określa ustawienia używane do tworzenia pliku cookie. [Nazwa](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) domyślna na [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). [Domyślnie](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) ścieżka ma [wartość SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) domyślnie to [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) domyślnie `true`. [Domyślne ustawienia domyślne isessential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) to `false`. |
| [Idletimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Wskazuje, `IdleTimeout` jak długo sesja może być bezczynna, zanim jej zawartość zostanie porzucona. Każdy dostęp do sesji resetuje limit czasu. To ustawienie dotyczy tylko zawartości sesji, a nie pliku cookie. Wartość domyślna to 20 minut. |
| [Czas IO](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Maksymalny czas dowolny załadować sesję ze sklepu lub zatwierdzić ją z powrotem do magazynu. To ustawienie może dotyczyć tylko operacji asynchronicznych. Ten limit czasu można wyłączyć za pomocą [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Wartość domyślna to 1 minuta. |

Sesja używa pliku cookie do śledzenia i identyfikowania żądań z jednej przeglądarki. Domyślnie ten plik `.AspNetCore.Session`cookie ma nazwę , `/`i używa ścieżki . Ponieważ domyślny plik cookie nie określa domeny, nie jest on udostępniany skryptowi po stronie klienta `true`na stronie (ponieważ [domyślnie httpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) jest domyślny).

Aby zastąpić domyślne ustawienia sesji `SessionOptions`plików cookie, należy użyć:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Aplikacja używa [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) właściwość, aby określić, jak długo sesja może być bezczynny, zanim jego zawartość w pamięci podręcznej serwera są porzucone. Ta właściwość jest niezależna od wygaśnięcia pliku cookie. Każde żądanie, które przechodzi przez [oprogramowanie pośredniczące sesji](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) resetuje limit czasu.

Stan sesji *nie blokuje*się . Jeśli dwa żądania jednocześnie próbują zmodyfikować zawartość sesji, ostatnie żądanie zastępuje pierwsze. `Session`jest realizowany jako *spójna sesja,* co oznacza, że cała zawartość jest przechowywana razem. Gdy dwa żądania mają na celu zmodyfikowanie różnych wartości sesji, ostatnie żądanie może zastąpić zmiany sesji wprowadzone przez pierwszy.

### <a name="set-and-get-session-values"></a>Ustawianie i ustalanie wartości sesji

Stan sesji jest dostępny z klasy Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) lub [MVC Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) z [httpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Ta właściwość jest implementacją [ISession.](/dotnet/api/microsoft.aspnetcore.http.isession)

Implementacja `ISession` zawiera kilka metod rozszerzenia, aby ustawić i pobrać wartości całkowite i ciąg. Metody rozszerzenia znajdują się w obszarze nazw [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (dodaj instrukcję, `using Microsoft.AspNetCore.Http;` aby uzyskać dostęp do metod rozszerzenia), gdy pakiet [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) odwołuje się do projektu. Oba pakiety są zawarte w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

`ISession`metody rozszerzenia:

* [Pobierz(ISession, Ciąg)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, Ciąg)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, Ciąg)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Poniższy przykład pobiera wartość sesji `IndexModel.SessionKeyName` dla`_Name` klucza (w przykładowej aplikacji) na stronie Strony Razor:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

W poniższym przykładzie pokazano, jak ustawić i uzyskać liczbę całkowitą i ciąg:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Wszystkie dane sesji muszą być serializowane, aby włączyć scenariusz rozproszonej pamięci podręcznej, nawet podczas korzystania z pamięci podręcznej w pamięci podręcznej. Ciągi i liczba całkowita serializatory są dostarczane przez metody rozszerzenia [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). Typy złożone muszą być serializowane przez użytkownika przy użyciu innego mechanizmu, takiego jak JSON.

Dodaj następujące metody rozszerzenia, aby ustawić i uzyskać obiekty serializable:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

W poniższym przykładzie pokazano, jak ustawić i uzyskać serializable obiektu z metod rozszerzenia:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData ( TempData )

ASP.NET Core udostępnia razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) lub <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>kontrolera . Ta właściwość przechowuje dane, dopóki nie jest odczytywany w innym żądaniu. [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) i [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) metody mogą służyć do badania danych bez usuwania na końcu żądania. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) oznacza wszystkie elementy w słowniku do przechowywania. `TempData`jest szczególnie przydatne do przekierowania, gdy dane są wymagane dla więcej niż jednego żądania. `TempData`jest implementowany `TempData` przez dostawców przy użyciu plików cookie lub stanu sesji.

## <a name="tempdata-samples"></a>Próbki TempData

Rozważmy następującą stronę, która tworzy klienta:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Na następującej `TempData["Message"]`stronie wyświetlane są:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

W poprzednim znaczników, na końcu żądania, `TempData["Message"]` **nie** jest `Peek` usuwany, ponieważ jest używany. Wyświetlane jest odświeżenie strony `TempData["Message"]`.

Następujące znaczniki `Keep` są podobne do poprzedniego kodu, ale służy do zachowania danych na końcu żądania:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Nawigowanie między stronami *IndexPeek* i *IndexKeep* nie spowoduje usunięcia `TempData["Message"]`.

Następujący kod `TempData["Message"]`jest wyświetlany , ale na `TempData["Message"]` końcu żądania, jest usuwany:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Dostawcy TempData

Dostawca tempdata oparty na plikach cookie jest domyślnie używany do przechowywania tempdata w plikach cookie.

Dane plików cookie są szyfrowane przy użyciu [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), zakodowane [base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), a następnie fragmentaryczne. Ponieważ plik cookie jest fragmentowany, limit rozmiaru pojedynczego pliku cookie znaleziony w ASP.NET Core 1.x nie ma zastosowania. Dane plików cookie nie są kompresowane, ponieważ kompresja zaszyfrowanych danych może prowadzić do problemów z [bezpieczeństwem,](https://wikipedia.org/wiki/CRIME_(security_exploit)) takich jak ataki CRIME i [BREACH.](https://wikipedia.org/wiki/BREACH_(security_exploit)) Aby uzyskać więcej informacji na temat dostawcy tempdata opartego na plikach cookie, zobacz [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Wybierz dostawcę tempdata

Wybór dostawcy TempData obejmuje kilka zagadnień, takich jak:

1. Czy aplikacja już używa stanu sesji? Jeśli tak, przy użyciu dostawcy TempData stanu sesji nie ma żadnych dodatkowych kosztów dla aplikacji (oprócz rozmiaru danych).
2. Czy aplikacja używa TempData tylko oszczędnie dla stosunkowo niewielkich ilości danych (do 500 bajtów)? Jeśli tak, dostawca pliku cookie TempData dodaje niewielki koszt do każdego żądania, które prowadzi TempData. Jeśli nie, dostawca tempdata stanu sesji może być korzystne, aby uniknąć zaokrąglania dużej ilości danych w każdym żądaniu, dopóki tempdata jest zużywana.
3. Czy aplikacja działa w farmie serwerów na wielu serwerach? Jeśli tak, nie ma dodatkowej konfiguracji wymaganej do korzystania z dostawcy <xref:security/data-protection/introduction> plików cookie TempData poza ochroną danych (zobacz i [dostawcy magazynu kluczy).](xref:security/data-protection/implementation/key-storage-providers)

> [!NOTE]
> Większość klientów internetowych (takich jak przeglądarki internetowe) wymusza limity maksymalnego rozmiaru każdego pliku cookie, całkowitej liczby plików cookie lub obu tych plików. Korzystając z dostawcy pliku cookie TempData, sprawdź, czy aplikacja nie przekroczy tych limitów. Należy wziąć pod uwagę całkowity rozmiar danych. Uwzględnij wzrost rozmiaru pliku cookie z powodu szyfrowania i fragmentowania.

### <a name="configure-the-tempdata-provider"></a>Konfigurowanie dostawcy tempdata

Dostawca tempdata oparty na plikach cookie jest domyślnie włączony.

Aby włączyć dostawcę tempdata opartego na sesji, użyj metody rozszerzenia [AddSessionStateTempDataProvider:](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider)

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Kolejność oprogramowania pośredniczącego jest ważna. W poprzednim przykładzie `InvalidOperationException` wyjątek `UseSession` występuje, gdy `UseMvc`jest wywoływany po . Aby uzyskać więcej informacji, zobacz [Oprogramowanie pośredniczące Zamawianie](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Jeśli kierowanie .NET Framework i przy użyciu dostawcy TempData oparte na sesji, dodać [microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) pakiet do projektu.

## <a name="query-strings"></a>Ciągi zapytań

Ograniczona ilość danych mogą być przekazywane z jednego żądania do innego, dodając go do ciągu kwerendy nowego żądania. Jest to przydatne do przechwytywania stanu w sposób trwały, który umożliwia łącza ze stanem osadzonym do udostępniania za pośrednictwem poczty e-mail lub sieci społecznościowych. Ponieważ ciągi zapytań adres url są publiczne, nigdy nie używaj ciągów zapytań dla poufnych danych.

Oprócz niezamierzonego udostępniania, w tym danych w ciągach zapytań, można tworzyć możliwości ataków [CSRF (Cross-Site Request Forgery),](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) co może skłonić użytkowników do odwiedzania złośliwych witryn podczas uwierzytelniania. Osoby atakujące mogą następnie wykraść dane użytkownika z aplikacji lub podjąć szkodliwe działania w imieniu użytkownika. Każdy zachowany stan aplikacji lub sesji musi chronić przed atakami CSRF. Aby uzyskać więcej informacji, zobacz [Zapobieganie atakom xsrf/CSRF (Cross-Site Request Fałszerstwa" ( XSRF/CSRF) (Zapobieganie atakom wielomiejscowym).](xref:security/anti-request-forgery)

## <a name="hidden-fields"></a>Pola ukryte

Dane można zapisywać w ukrytych polach formularzy i księgować z powrotem w następnym żądaniu. Jest to powszechne w formularzach wielostronicowych. Ponieważ klient może potencjalnie manipulować danymi, aplikacja musi zawsze ponownie zaatrować dane przechowywane w ukrytych polach.

## <a name="httpcontextitems"></a>HttpContext.Items

Kolekcja [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) służy do przechowywania danych podczas przetwarzania pojedynczego żądania. Zawartość kolekcji są odrzucane po przetworzeniu żądania. Kolekcja `Items` jest często używana do zezwalania na składniki lub oprogramowanie pośredniczące do komunikowania się, gdy działają w różnych punktach w czasie podczas żądania i nie mają bezpośredniego sposobu przekazywania parametrów.

W poniższym przykładzie `isVerified` oprogramowanie `Items` [pośredniczące](xref:fundamentals/middleware/index) dodaje do kolekcji.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

W dalszej części potoku inny program `isVerified`pośredniczący może uzyskać dostęp do wartości:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

W przypadku oprogramowania pośredniczącego używanego `string` tylko przez jedną aplikację klucze są dopuszczalne. Oprogramowanie pośredniczące współużytkowane między wystąpieniami aplikacji należy użyć unikatowych kluczy obiektów, aby uniknąć kolizji kluczy. W poniższym przykładzie pokazano, jak używać unikatowego klucza obiektu zdefiniowanego w klasie oprogramowania pośredniczącego:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Inny kod może uzyskać `HttpContext.Items` dostęp do wartości przechowywanej przy użyciu klucza udostępnianego przez klasę oprogramowania pośredniczącego:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Takie podejście ma również tę zaletę, eliminując użycie ciągów kluczy w kodzie.

## <a name="cache"></a>Pamięć podręczna

Buforowanie to skuteczny sposób przechowywania i pobierania danych. Aplikacja może kontrolować okres istnienia elementów w pamięci podręcznej.

Buforowane dane nie są skojarzone z określonym żądaniem, użytkownikiem ani sesją. **Należy uważać, aby nie buforować danych specyficznych dla użytkownika, które mogą być pobierane przez żądania innych użytkowników.**

Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

Użyj [iniekcji zależności,](xref:fundamentals/dependency-injection) aby udostępnić dane wszystkim użytkownikom:

1. Zdefiniuj usługę zawierającą dane. Na przykład klasa `MyAppData` o nazwie jest zdefiniowana:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Dodaj klasę usługi `Startup.ConfigureServices`do:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Korzystanie z klasy usługi danych:

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

* "Nie można rozpoznać usługi dla typu 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' podczas próby aktywowania 'Microsoft.AspNetCore.Session.DistributedSessionStore'".

  Jest to zwykle spowodowane niepowodzeniem konfiguracji `IDistributedCache` co najmniej jednej implementacji. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed> i <xref:performance/caching/memory>.

* W przypadku, gdy oprogramowanie pośredniczące sesji nie może utrwalić sesji (na przykład, jeśli zapas zapasu nie jest dostępny), oprogramowanie pośredniczące rejestruje wyjątek i żądanie jest kontynuowane normalnie. Prowadzi to do nieprzewidywalnego zachowania.

  Na przykład użytkownik przechowuje koszyk w sesji. Użytkownik dodaje element do koszyka, ale zatwierdzenie kończy się niepowodzeniem. Aplikacja nie wie o awarii, więc zgłasza użytkownikowi, że element został dodany do koszyka, co nie jest prawdą.

  Zalecane podejście do sprawdzania błędów `await feature.Session.CommitAsync();` jest wywołanie z kodu aplikacji, gdy aplikacja jest wykonywana pisanie do sesji. `CommitAsync`zgłasza wyjątek, jeśli magazyn zapasowy jest niedostępny. Jeśli `CommitAsync` nie powiedzie się, aplikacja może przetworzyć wyjątek. `LoadAsync`w tych samych warunkach, w których magazyn danych jest niedostępny.
  
## <a name="opno-locsignalr-and-session-state"></a>SignalRi stan sesji

SignalRaplikacje nie powinny używać stanu sesji do przechowywania informacji. SignalRaplikacje mogą przechowywać `Context.Items` stan połączenia w centrum. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Zasoby dodatkowe

<xref:host-and-deploy/web-farm>
::: moniker-end
