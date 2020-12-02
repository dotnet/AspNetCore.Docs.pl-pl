---
title: ASP.NET Core oprogramowanie pośredniczące
author: rick-anderson
description: Dowiedz się więcej na temat ASP.NET Core oprogramowania pośredniczącego i potoku żądania.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: aa51e53284bc25629b3975ff0e6de967b9a2b866
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513125"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Core oprogramowanie pośredniczące

::: moniker range=">= aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)

Oprogramowanie pośredniczące jest oprogramowaniem, które jest połączone z potokiem aplikacji w celu obsługi żądań i odpowiedzi. Każdy składnik:

* Wybiera, czy żądanie ma zostać przekazane do następnego składnika w potoku.
* Może wykonać prace przed i po następnym składniku potoku.

Delegaty żądań są używane do kompilowania potoku żądania. Delegaty żądań obsługują każde żądanie HTTP.

Delegaty żądań są konfigurowane przy użyciu <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> metod rozszerzających, i. Osobnego delegata żądania można określić jako metodę anonimową (nazywaną w wierszu) lub można ją zdefiniować w klasie wielokrotnego użytku. Te klasy wielokrotnego użytku i metody anonimowe w wierszu to *oprogramowanie pośredniczące*, nazywane również *składnikami oprogramowania pośredniczącego*. Każdy składnik pośredniczący w potoku żądania jest odpowiedzialny za wywoływanie następnego składnika w potoku lub krótkiego obwodu potoku. W przypadku krótkiego obwodu oprogramowanie pośredniczące nosi nazwę *oprogramowania pośredniczącego terminalu* , ponieważ zapobiega przetwarzaniu żądania przez dalsze oprogramowanie pośredniczące.

<xref:migration/http-modules> Wyjaśnia różnicę między potokami żądań w ASP.NET Core i ASP.NET 4. x i udostępnia dodatkowe przykłady oprogramowania pośredniczącego.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder

Potok żądania ASP.NET Core składa się z sekwencji delegatów żądań o nazwie jeden po drugim. Na poniższym diagramie przedstawiono koncepcję. Wątek wykonywania jest zgodny z czarnym strzałką.

![Wzorzec przetwarzania żądań pokazujący żądanie docierające do, przetwarzania przez trzy middlewares i odpowiedzi opuszczających aplikację. Każde oprogramowanie pośredniczące uruchamia swoją logikę i przekazuje żądanie do następnego oprogramowania pośredniczącego przy następnej instrukcji (). Po przetworzeniu żądania przez trzecie oprogramowanie pośredniczące żądanie przechodzi z powrotem przez poprzednie dwie middlewares w odwrotnej kolejności, aby można było je przetwarzać po kolejne instrukcje () przed opuszczeniem aplikacji jako odpowiedzi na klienta.](index/_static/request-delegate-pipeline.png)

Każdy delegat może wykonać operacje przed i po następnym delegacie. Delegaty obsługi wyjątków powinny być wywoływane wczesnie w potoku, dlatego mogą przechwytywać wyjątki, które występują w późniejszych etapach potoku.

Najprostszym możliwym ASP.NET Core aplikacji jest skonfigurowanie pojedynczego delegata żądania, który obsługuje wszystkie żądania. Ten przypadek nie obejmuje rzeczywistego potoku żądania. Zamiast tego funkcja pojedynczej anonimowej jest wywoływana w odpowiedzi na każde żądanie HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Łączenie wielu delegatów żądań z <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> . `next`Parametr reprezentuje następny delegat w potoku. Potok można skrócić, *nie* wywołując *następnego* parametru. Zazwyczaj można wykonywać akcje zarówno przed, jak i po następnym delegatze, jak pokazano w poniższym przykładzie:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Gdy delegat nie przekazuje żądania do następnego delegata, jest on nazywany *krótkim obwodem potoku żądania*. Krótki obwód jest często pożądany, ponieważ pozwala uniknąć niepotrzebnej pracy. Na przykład [statyczne oprogramowanie pośredniczące](xref:fundamentals/static-files) może działać jako *oprogramowanie pośredniczące terminalu* przez przetwarzanie żądania dla pliku statycznego i krótkiego obwodu pozostałej części potoku. Oprogramowanie pośredniczące dodane do potoku przed oprogramowanie pośredniczące, które zakończy dalsze przetwarzanie, ciągle przetwarza kod po ich `next.Invoke` zestawie. Należy jednak zapoznać się z poniższym ostrzeżeniem dotyczącym próby zapisu do odpowiedzi, która została już wysłana.

> [!WARNING]
> Nie wywołuj `next.Invoke` po wysłaniu odpowiedzi do klienta. <xref:Microsoft.AspNetCore.Http.HttpResponse>Po rozpoczęciu zgłaszania wyjątku zmiany wprowadzone po odpowiedzi. Na przykład [ustawienie nagłówki i kod stanu zgłasza wyjątek](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started). Zapisywanie w treści odpowiedzi po wywołaniu `next` :
>
> * Może spowodować naruszenie protokołu. Na przykład zapisywanie więcej niż podane `Content-Length` .
> * Może uszkodzić format treści. Na przykład, pisząc stopkę HTML do pliku CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> jest przydatną wskazówką wskazującą, czy nagłówki zostały wysłane lub w których zapisano treść.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> Delegaty nie odbierają `next` parametru. Pierwszy `Run` delegat jest zawsze terminalem i kończy potok. `Run` jest konwencją. Niektóre składniki pośredniczące mogą uwidaczniać `Run[Middleware]` metody, które są uruchamiane na końcu potoku:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

W poprzednim przykładzie `Run` Delegat zapisuje dane `"Hello from 2nd delegate."` do odpowiedzi, a następnie kończy potok. Jeśli `Use` `Run` po delegatze zostanie dodany inny obiekt lub delegat `Run` , nie jest on wywoływany.

<a name="order"></a>

## <a name="middleware-order"></a>Zamówienie oprogramowania pośredniczącego

Na poniższym diagramie przedstawiono potok przetwarzania kompletnego żądania dla ASP.NET Core aplikacji MVC i Razor Pages. Możesz zobaczyć, jak w typowej aplikacji są uporządkowane istniejące middlewares i gdzie są dodawane niestandardowe middlewares. Masz pełną kontrolę nad tym, jak zmienić kolejność istniejących middlewares lub wstrzyknąć nowe niestandardowe middlewares w miarę potrzeb w scenariuszach.

![ASP.NET Core potok oprogramowania pośredniczącego](index/_static/middleware-pipeline.svg)

Oprogramowanie pośredniczące **punktu końcowego** na powyższym diagramie wykonuje potok filtru dla odpowiedniego typu aplikacji &mdash; MVC lub Razor strony.

![Potok filtru ASP.NET Core](index/_static/mvc-endpoint.svg)

Kolejność, w jakiej składniki pośredniczące są dodawane w `Startup.Configure` metodzie, definiuje kolejność, w jakiej składniki oprogramowania pośredniczącego są wywoływane w żądaniach i odwrotnej kolejności odpowiedzi. Kolejność ma **kluczowe znaczenie** dla bezpieczeństwa, wydajności i funkcjonalności.

Poniższa `Startup.Configure` Metoda dodaje składniki pośredniczące związane z zabezpieczeniami w typowym zalecanym porządku:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Oprogramowanie pośredniczące, które nie jest dodawane podczas tworzenia nowej aplikacji sieci Web z [kontami poszczególnych użytkowników](xref:security/authentication/identity) , jest oznaczone jako komentarz.
* Nie każde oprogramowanie pośredniczące musi przejść do tej dokładnej kolejności, ale wiele do. Na przykład:
  * `UseCors`, `UseAuthentication` i `UseAuthorization` muszą przejść w podanej kolejności.
  * `UseCors` obecnie musi przejść `UseResponseCaching` do [tego błędu](https://github.com/dotnet/aspnetcore/issues/23218).

W niektórych scenariuszach oprogramowanie pośredniczące będzie miało inną kolejność. Na przykład kolejność buforowania i kompresji jest specyficzna dla scenariusza i istnieje wiele prawidłowych postanowień. Na przykład:

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

Za pomocą powyższego kodu procesora można zapisać przez buforowanie skompresowanej odpowiedzi, ale może być możliwe przebuforowanie wielu reprezentacji zasobu przy użyciu różnych algorytmów kompresji, takich jak gzip lub brotli.

Poniższe porządkowanie łączy pliki statyczne, aby umożliwić buforowanie skompresowanych plików statycznych:

```csharp
app.UseResponseCaching
app.UseResponseCompression
app.UseStaticFiles
```

Poniższa `Startup.Configure` Metoda dodaje składniki pośredniczące dla typowych scenariuszy aplikacji:

1. Obsługa wyjątków/błędów
   * Gdy aplikacja jest uruchamiana w środowisku deweloperskim:
     * Oprogramowanie pośredniczące strony wyjątków dla deweloperów ( <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> ) zgłasza błędy środowiska uruchomieniowego aplikacji.
     * Strona błędu bazy danych raporty pośredniczące — błędy środowiska uruchomieniowego bazy danych.
   * Gdy aplikacja jest uruchamiana w środowisku produkcyjnym:
     * Program obsługi wyjątków ( <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ) przechwytuje wyjątki zgłoszone w następujących middlewares.
     * Protokół pośredniczący protokołu HTTP Strict Transport Security (HSTS) ( <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A> ) dodaje `Strict-Transport-Security` nagłówek.
1. Oprogramowanie pośredniczące przekierowywania HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ) przekierowuje żądania HTTP do protokołu HTTPS.
1. Oprogramowanie pośredniczące plików statycznych ( <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> ) zwraca pliki statyczne i dalsze przetwarzanie żądań na krótkie obwody.
1. Cookie Oprogramowanie pośredniczące zasad ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) powoduje, że aplikacja jest zgodna z przepisami ue ogólne rozporządzenie o ochronie danych (Rodo).
1. Kierowanie oprogramowania pośredniczącego ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ) do przesyłania żądań.
1. Oprogramowanie pośredniczące uwierzytelniania ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ) próbuje uwierzytelnić użytkownika przed zezwoleniem im na dostęp do zabezpieczonych zasobów.
1. Oprogramowanie pośredniczące autoryzacji ( <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ) autoryzuje użytkownika do uzyskiwania dostępu do zabezpieczonych zasobów.
1. Oprogramowanie pośredniczące sesji ( <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> ) ustanawia i utrzymuje stan sesji. Jeśli aplikacja używa stanu sesji, wywołaj oprogramowanie pośredniczące sesji po oprogramowaniu Cookie pośredniczącym zasad i przed oprogramowania MVC.
1. Program pośredniczący do routingu punktu końcowego ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> z <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A> ) umożliwia dodawanie Razor punktów końcowych stron do potoku żądania.

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

W poprzednim przykładowym kodzie każda Metoda rozszerzenia oprogramowania pośredniczącego jest udostępniana w <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> przestrzeni nazw.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> jest pierwszym składnikiem pośredniczącym dodanym do potoku. W związku z tym, oprogramowanie pośredniczące programu obsługi wyjątków przechwytuje wszystkie wyjątki występujące w późniejszych wywołaniach.

Oprogramowanie pośredniczące plików statycznych jest wczesnie wywoływane w potoku, dzięki czemu może obsługiwać żądania i krótkie obwód bez przechodzenia przez pozostałe składniki. Oprogramowanie pośredniczące plików statycznych **nie zapewnia żadnych** kontroli autoryzacji. Wszystkie pliki obsługiwane przez oprogramowanie pośredniczące plików statycznych, w tym w katalogu *wwwroot*, są publicznie dostępne. Aby zapoznać się z podejściem do zabezpieczania plików statycznych, zobacz <xref:fundamentals/static-files> .

Jeśli żądanie nie jest obsługiwane przez oprogramowanie pośredniczące pliku statycznego, jest ono przesyłane do oprogramowania pośredniczącego uwierzytelniania ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ), które wykonuje uwierzytelnianie. Uwierzytelnianie nie ma krótkoterminowych żądań nieuwierzytelnionych. Chociaż uwierzytelnianie pośredniczące uwierzytelnia żądania, autoryzacja (i odrzucanie) występuje tylko po zaznaczeniu przez MVC określonego Razor kontrolera i akcji.

Poniższy przykład ilustruje kolejność oprogramowania pośredniczącego, w którym żądania plików statycznych są obsługiwane przez oprogramowanie pośredniczące plików statycznych przed użyciem oprogramowania pośredniczącego kompresji. Pliki statyczne nie są kompresowane z tą kolejnością oprogramowania. RazorOdpowiedzi na strony można skompresować.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

W przypadku aplikacji jednostronicowych (aplikacji jednostronicowych) oprogramowanie pośredniczące SPA <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> zazwyczaj jest ostatnim węzłem potoku oprogramowania pośredniczącego. Oprogramowanie pośredniczące SPA jest ostatnie:

* Aby zezwolić wszystkim innym middlewares na odpowiadanie na pasujące żądania.
* Aby umożliwić uruchamianie aplikacji jednostronicowych z routingiem po stronie klienta dla wszystkich tras, które nie są rozpoznawane przez aplikację serwera.

Aby uzyskać więcej informacji na temat aplikacji jednostronicowych, zobacz przewodniki dotyczące szablonów projektów [reagowanie](xref:spa/react) i [kątowych](xref:spa/angular) .

### <a name="forwarded-headers-middleware-order"></a>Przekierowane nagłówki — zamówienie oprogramowania

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a>Rozgałęzianie potoku oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> rozszerzenia są używane jako konwencja rozgałęziania potoku. `Map` rozgałęzienia potoku żądania na podstawie dopasowań podanej ścieżki żądania. Jeśli ścieżka żądania rozpoczyna się od podaną ścieżką, rozgałęzienie jest wykonywane.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

W poniższej tabeli przedstawiono żądania i odpowiedzi z `http://localhost:1234` używania poprzedniego kodu.

| Żądanie             | Reakcja                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Witaj od delegata innego niż mapowanie. |
| localhost: 1234/Map1 | Test mapy 1                   |
| localhost: 1234/MAP2 — | Test mapy 2                   |
| localhost: 1234/map3 — | Witaj od delegata innego niż mapowanie. |

Gdy `Map` jest używany, dopasowane segmenty ścieżki są usuwane z `HttpRequest.Path` i dodawane do `HttpRequest.PathBase` każdego żądania.

`Map` obsługuje zagnieżdżanie, na przykład:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` można również dopasować wiele segmentów jednocześnie:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> rozgałęzienia potoku żądania na podstawie wyniku danego predykatu. Dowolny predykat typu `Func<HttpContext, bool>` może służyć do mapowania żądań do nowej gałęzi potoku. W poniższym przykładzie predykat służy do wykrywania obecności zmiennej ciągu zapytania `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

W poniższej tabeli przedstawiono żądania i odpowiedzi z `http://localhost:1234` używania poprzedniego kodu:

| Żądanie                       | Reakcja                     |
| ----------------------------- | ---------------------------- |
| localhost: 1234                | Witaj od delegata innego niż mapowanie. |
| localhost: 1234/? Branch = Master | Używane gałęzie = Master         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> oddziałuje również potok żądania na podstawie wyniku danego predykatu. W przeciwieństwie `MapWhen` do programu, gałąź ta jest ponownie przyłączona do głównego potoku, jeśli nie jest ona krótka lub nie zawiera terminalu pośredniczącego:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

W poprzednim przykładzie odpowiedź "Hello z głównego potoku". jest zapisywana dla wszystkich żądań. Jeśli żądanie zawiera zmienną ciągu zapytania `branch` , jej wartość jest rejestrowana przed ponownym przyłączeniem głównego potoku.

## <a name="built-in-middleware"></a>Wbudowane oprogramowanie pośredniczące

ASP.NET Core dostarcza z następującymi składnikami oprogramowania pośredniczącego. Kolumna *Order* zawiera uwagi dotyczące umieszczania oprogramowania pośredniczącego w potoku przetwarzania żądań i w ramach jakich warunków oprogramowanie pośredniczące może przerwać przetwarzanie żądań. W przypadku krótkiego obwody przez oprogramowanie pośredniczące proces przetwarzania żądań i zapobiega przetwarzaniu żądania przez dalsze *podrzędne oprogramowanie pośredniczące.* Aby uzyskać więcej informacji na temat skracania obwodów, zobacz sekcję [Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Oprogramowanie pośredniczące | Opis | Zamówienie |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Zapewnia obsługę uwierzytelniania. | Przed zainstalowaniem `HttpContext.User` . Terminal dla wywołań zwrotnych uwierzytelniania OAuth. |
| [Autoryzacja](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | Zapewnia obsługę autoryzacji. | Natychmiast po oprogramowaniu pośredniczącym uwierzytelniania. |
| [Cookie Zasad](xref:security/gdpr) | Śledzi zgodę użytkowników na przechowywanie informacji osobistych i wymusza minimalne standardy dla cookie pól, takich jak `secure` i `SameSite` . | Przed wystawianym przez oprogramowanie pośredniczące cookie . Przykłady: uwierzytelnianie, sesja, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje udostępnianie zasobów między źródłami. | Przed składnikami korzystającymi z mechanizmu CORS. `UseCors` obecnie musi przejść `UseResponseCaching` do [tego błędu](https://github.com/dotnet/aspnetcore/issues/23218).|
| [Diagnostyka](xref:fundamentals/error-handling) | Kilka oddzielnych middlewares, które udostępniają stronę wyjątku dewelopera, obsługę wyjątków, strony kodu stanu i domyślną stronę sieci Web dla nowych aplikacji. | Przed składnikami, które generują błędy. Terminal dla wyjątków lub obsługa domyślnej strony sieci Web dla nowych aplikacji. |
| [Nagłówki przesłane dalej](xref:host-and-deploy/proxy-load-balancer) | Przekazuje nagłówki proxy do bieżącego żądania. | Przed składnikami, które zużywają zaktualizowane pola. Przykłady: schemat, host, adres IP klienta, metoda. |
| [Sprawdzenie kondycji](xref:host-and-deploy/health-checks) | Sprawdza kondycję aplikacji ASP.NET Core i jej zależności, na przykład sprawdzanie dostępności bazy danych. | Terminal, jeśli żądanie pasuje do punktu końcowego sprawdzania kondycji. |
| [Propagowanie nagłówka](xref:fundamentals/http-requests#header-propagation-middleware) | Propaguje nagłówki HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP. |
| [Zastąpienie metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Zezwala na przychodzące żądanie POST przesłaniające metodę. | Przed składnikami, które zużywają zaktualizowaną metodę. |
| [Przekierowanie HTTPS](xref:security/enforcing-ssl#require-https) | Przekierowuj wszystkie żądania HTTP do protokołu HTTPS. | Przed składnikami, które używają adresu URL. |
| [Zabezpieczenia protokołu HTTP Strict Transport (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Ulepszanie zabezpieczeń oprogramowanie pośredniczące, które dodaje specjalny nagłówek odpowiedzi. | Przed wysłaniem odpowiedzi i po składnikach, które modyfikują żądania. Przykłady: nagłówki przesłane dalej, ponowne zapisywanie adresów URL. |
| [MVC](xref:mvc/overview) | Przetwarza żądania ze MVC/ Razor Pages. | Terminal, jeśli żądanie pasuje do trasy. |
| [OWIN](xref:fundamentals/owin) | Współdziałanie z aplikacjami opartymi na OWIN, serwerami i oprogramowanie pośredniczące. | Terminal, jeśli oprogramowanie OWIN w pełni przetwarza żądanie. |
| [Buforowanie odpowiedzi](xref:performance/caching/middleware) | Zapewnia obsługę buforowania odpowiedzi. | Przed składnikami, które wymagają buforowania. `UseCORS` musi występować przed `UseResponseCaching` .|
| [Kompresja odpowiedzi](xref:performance/response-compression) | Zapewnia obsługę kompresowania odpowiedzi. | Przed składnikami wymagającymi kompresji. |
| [Lokalizacja żądania](xref:fundamentals/localization) | Zapewnia obsługę lokalizacji. | Przed uwzględnieniem poufnych składników lokalizacji. |
| [Routing punktów końcowych](xref:fundamentals/routing) | Definiuje trasy żądań i ogranicza je. | Terminal dla pasujących tras. |
| [HASŁA](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | Obsługuje wszystkie żądania z tego punktu w łańcuchu oprogramowania pośredniczącego, zwracając domyślną stronę aplikacji jednostronicowej (SPA) | Na koniec łańcucha, dzięki czemu inne oprogramowanie pośredniczące do obsługi plików statycznych, działania MVC itd., ma pierwszeństwo.|
| [Sesja](xref:fundamentals/app-state) | Zapewnia obsługę zarządzania sesjami użytkowników. | Przed składnikami, które wymagają sesji. | 
| [Pliki statyczne](xref:fundamentals/static-files) | Zapewnia obsługę plików statycznych i przeglądania katalogów. | Terminal, jeśli żądanie pasuje do pliku. |
| [Ponowne zapisywanie adresów URL](xref:fundamentals/url-rewriting) | Zapewnia obsługę ponownego zapisywania adresów URL i Przekierowywanie żądań. | Przed składnikami, które używają adresu URL. |
| [Protokoły WebSocket](xref:fundamentals/websockets) | Włącza protokół WebSockets. | Przed składnikami, które są wymagane do akceptowania żądań WebSocket. |

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Opcje okresu istnienia i rejestracji](xref:fundamentals/dependency-injection#lifetime-and-registration-options) zawierają pełny przykład oprogramowania pośredniczącego z usługami *objętymi zakresem*, *przejściowymi* i *pojedynczymi* okresami istnienia.
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)

Oprogramowanie pośredniczące jest oprogramowaniem, które jest połączone z potokiem aplikacji w celu obsługi żądań i odpowiedzi. Każdy składnik:

* Wybiera, czy żądanie ma zostać przekazane do następnego składnika w potoku.
* Może wykonać prace przed i po następnym składniku potoku.

Delegaty żądań są używane do kompilowania potoku żądania. Delegaty żądań obsługują każde żądanie HTTP.

Delegaty żądań są konfigurowane przy użyciu <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> metod rozszerzających, i. Osobnego delegata żądania można określić jako metodę anonimową (nazywaną w wierszu) lub można ją zdefiniować w klasie wielokrotnego użytku. Te klasy wielokrotnego użytku i metody anonimowe w wierszu to *oprogramowanie pośredniczące*, nazywane również *składnikami oprogramowania pośredniczącego*. Każdy składnik pośredniczący w potoku żądania jest odpowiedzialny za wywoływanie następnego składnika w potoku lub krótkiego obwodu potoku. W przypadku krótkiego obwodu oprogramowanie pośredniczące nosi nazwę *oprogramowania pośredniczącego terminalu* , ponieważ zapobiega przetwarzaniu żądania przez dalsze oprogramowanie pośredniczące.

<xref:migration/http-modules> Wyjaśnia różnicę między potokami żądań w ASP.NET Core i ASP.NET 4. x i udostępnia dodatkowe przykłady oprogramowania pośredniczącego.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder

Potok żądania ASP.NET Core składa się z sekwencji delegatów żądań o nazwie jeden po drugim. Na poniższym diagramie przedstawiono koncepcję. Wątek wykonywania jest zgodny z czarnym strzałką.

![Wzorzec przetwarzania żądań pokazujący żądanie docierające do, przetwarzania przez trzy middlewares i odpowiedzi opuszczających aplikację. Każde oprogramowanie pośredniczące uruchamia swoją logikę i przekazuje żądanie do następnego oprogramowania pośredniczącego przy następnej instrukcji (). Po przetworzeniu żądania przez trzecie oprogramowanie pośredniczące żądanie przechodzi z powrotem przez poprzednie dwie middlewares w odwrotnej kolejności, aby można było je przetwarzać po kolejne instrukcje () przed opuszczeniem aplikacji jako odpowiedzi na klienta.](index/_static/request-delegate-pipeline.png)

Każdy delegat może wykonać operacje przed i po następnym delegacie. Delegaty obsługi wyjątków powinny być wywoływane wczesnie w potoku, dlatego mogą przechwytywać wyjątki, które występują w późniejszych etapach potoku.

Najprostszym możliwym ASP.NET Core aplikacji jest skonfigurowanie pojedynczego delegata żądania, który obsługuje wszystkie żądania. Ten przypadek nie obejmuje rzeczywistego potoku żądania. Zamiast tego funkcja pojedynczej anonimowej jest wywoływana w odpowiedzi na każde żądanie HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Pierwszy <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> Delegat kończy potok.

Łączenie wielu delegatów żądań z <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> . `next`Parametr reprezentuje następny delegat w potoku. Potok można skrócić, *nie* wywołując *następnego* parametru. Zazwyczaj można wykonywać akcje zarówno przed, jak i po następnym delegatze, jak pokazano w poniższym przykładzie:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Gdy delegat nie przekazuje żądania do następnego delegata, jest on nazywany *krótkim obwodem potoku żądania*. Krótki obwód jest często pożądany, ponieważ pozwala uniknąć niepotrzebnej pracy. Na przykład [statyczne oprogramowanie pośredniczące](xref:fundamentals/static-files) może działać jako *oprogramowanie pośredniczące terminalu* przez przetwarzanie żądania dla pliku statycznego i krótkiego obwodu pozostałej części potoku. Oprogramowanie pośredniczące dodane do potoku przed oprogramowanie pośredniczące, które zakończy dalsze przetwarzanie, ciągle przetwarza kod po ich `next.Invoke` zestawie. Należy jednak zapoznać się z poniższym ostrzeżeniem dotyczącym próby zapisu do odpowiedzi, która została już wysłana.

> [!WARNING]
> Nie wywołuj `next.Invoke` po wysłaniu odpowiedzi do klienta. <xref:Microsoft.AspNetCore.Http.HttpResponse>Po rozpoczęciu zgłaszania wyjątku zmiany wprowadzone po odpowiedzi. Na przykład zmiany, takie jak nagłówki ustawień i kod stanu zgłaszają wyjątek. Zapisywanie w treści odpowiedzi po wywołaniu `next` :
>
> * Może spowodować naruszenie protokołu. Na przykład zapisywanie więcej niż podane `Content-Length` .
> * Może uszkodzić format treści. Na przykład, pisząc stopkę HTML do pliku CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> jest przydatną wskazówką wskazującą, czy nagłówki zostały wysłane lub w których zapisano treść.

<a name="order"></a>

## <a name="middleware-order"></a>Zamówienie oprogramowania pośredniczącego

Kolejność, w jakiej składniki pośredniczące są dodawane w `Startup.Configure` metodzie, definiuje kolejność, w jakiej składniki oprogramowania pośredniczącego są wywoływane w żądaniach i odwrotnej kolejności odpowiedzi. Kolejność ma **kluczowe znaczenie** dla bezpieczeństwa, wydajności i funkcjonalności.

W poniższej `Startup.Configure` metodzie w zalecanej kolejności dodano składniki pośredniczące powiązane z zabezpieczeniami:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Oprogramowanie pośredniczące, które nie jest dodawane podczas tworzenia nowej aplikacji sieci Web z [kontami poszczególnych użytkowników](xref:security/authentication/identity) , jest oznaczone jako komentarz.
* Nie każde oprogramowanie pośredniczące musi przejść do tej dokładnej kolejności, ale wiele do. Na przykład, `UseCors` i `UseAuthentication` musi przejść w podanej kolejności.

Poniższa `Startup.Configure` Metoda dodaje składniki pośredniczące dla typowych scenariuszy aplikacji:

1. Obsługa wyjątków/błędów
   * Gdy aplikacja jest uruchamiana w środowisku deweloperskim:
     * Oprogramowanie pośredniczące strony wyjątków dla deweloperów ( <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> ) zgłasza błędy środowiska uruchomieniowego aplikacji.
     * Strona błędu bazy danych oprogramowanie pośredniczące ( `Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage` ) raportuje błędy środowiska uruchomieniowego bazy danych.
   * Gdy aplikacja jest uruchamiana w środowisku produkcyjnym:
     * Program obsługi wyjątków ( <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ) przechwytuje wyjątki zgłoszone w następujących middlewares.
     * Protokół pośredniczący protokołu HTTP Strict Transport Security (HSTS) ( <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A> ) dodaje `Strict-Transport-Security` nagłówek.
1. Oprogramowanie pośredniczące przekierowywania HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ) przekierowuje żądania HTTP do protokołu HTTPS.
1. Oprogramowanie pośredniczące plików statycznych ( <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> ) zwraca pliki statyczne i dalsze przetwarzanie żądań na krótkie obwody.
1. Cookie Oprogramowanie pośredniczące zasad ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) powoduje, że aplikacja jest zgodna z przepisami ue ogólne rozporządzenie o ochronie danych (Rodo).
1. Oprogramowanie pośredniczące uwierzytelniania ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ) próbuje uwierzytelnić użytkownika przed zezwoleniem im na dostęp do zabezpieczonych zasobów.
1. Oprogramowanie pośredniczące sesji ( <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> ) ustanawia i utrzymuje stan sesji. Jeśli aplikacja używa stanu sesji, wywołaj oprogramowanie pośredniczące sesji po oprogramowaniu Cookie pośredniczącym zasad i przed oprogramowania MVC.
1. MVC ( <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ), aby dodać MVC do potoku żądania.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

W poprzednim przykładowym kodzie każda Metoda rozszerzenia oprogramowania pośredniczącego jest udostępniana w <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> przestrzeni nazw.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> jest pierwszym składnikiem pośredniczącym dodanym do potoku. W związku z tym, oprogramowanie pośredniczące programu obsługi wyjątków przechwytuje wszystkie wyjątki występujące w późniejszych wywołaniach.

Oprogramowanie pośredniczące plików statycznych jest wczesnie wywoływane w potoku, dzięki czemu może obsługiwać żądania i krótkie obwód bez przechodzenia przez pozostałe składniki. Oprogramowanie pośredniczące plików statycznych **nie zapewnia żadnych** kontroli autoryzacji. Wszystkie pliki obsługiwane przez oprogramowanie pośredniczące plików statycznych, w tym w katalogu *wwwroot*, są publicznie dostępne. Aby zapoznać się z podejściem do zabezpieczania plików statycznych, zobacz <xref:fundamentals/static-files> .

Jeśli żądanie nie jest obsługiwane przez oprogramowanie pośredniczące pliku statycznego, jest ono przesyłane do oprogramowania pośredniczącego uwierzytelniania ( <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> ), które wykonuje uwierzytelnianie. Uwierzytelnianie nie ma krótkoterminowych żądań nieuwierzytelnionych. Chociaż uwierzytelnianie pośredniczące uwierzytelnia żądania, autoryzacja (i odrzucanie) występuje tylko po zaznaczeniu przez MVC określonego Razor kontrolera i akcji.

Poniższy przykład ilustruje kolejność oprogramowania pośredniczącego, w którym żądania plików statycznych są obsługiwane przez oprogramowanie pośredniczące plików statycznych przed użyciem oprogramowania pośredniczącego kompresji. Pliki statyczne nie są kompresowane z tą kolejnością oprogramowania. Odpowiedzi MVC <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> można skompresować.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Używanie, uruchamianie i mapowanie

Skonfiguruj potok HTTP przy użyciu <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> , <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> i <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> . `Use`Metoda może skrócić obwód rurociągu (oznacza to, że jeśli nie wywoła `next` delegata żądania). `Run` jest konwencją, a niektóre składniki pośredniczące mogą uwidaczniać `Run[Middleware]` metody, które są uruchamiane na końcu potoku.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> rozszerzenia są używane jako konwencja rozgałęziania potoku. `Map` rozgałęzienia potoku żądania na podstawie dopasowań podanej ścieżki żądania. Jeśli ścieżka żądania rozpoczyna się od podaną ścieżką, rozgałęzienie jest wykonywane.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

W poniższej tabeli przedstawiono żądania i odpowiedzi z `http://localhost:1234` używania poprzedniego kodu.

| Żądanie             | Reakcja                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Witaj od delegata innego niż mapowanie. |
| localhost: 1234/Map1 | Test mapy 1                   |
| localhost: 1234/MAP2 — | Test mapy 2                   |
| localhost: 1234/map3 — | Witaj od delegata innego niż mapowanie. |

Gdy `Map` jest używany, dopasowane segmenty ścieżki są usuwane z `HttpRequest.Path` i dodawane do `HttpRequest.PathBase` każdego żądania.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> rozgałęzienia potoku żądania na podstawie wyniku danego predykatu. Dowolny predykat typu `Func<HttpContext, bool>` może służyć do mapowania żądań do nowej gałęzi potoku. W poniższym przykładzie predykat służy do wykrywania obecności zmiennej ciągu zapytania `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

W poniższej tabeli przedstawiono żądania i odpowiedzi z `http://localhost:1234` używania poprzedniego kodu.

| Żądanie                       | Reakcja                     |
| ----------------------------- | ---------------------------- |
| localhost: 1234                | Witaj od delegata innego niż mapowanie. |
| localhost: 1234/? Branch = Master | Używane gałęzie = Master         |

`Map` obsługuje zagnieżdżanie, na przykład:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` można również dopasować wiele segmentów jednocześnie:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Wbudowane oprogramowanie pośredniczące

ASP.NET Core dostarcza z następującymi składnikami oprogramowania pośredniczącego. Kolumna *Order* zawiera uwagi dotyczące umieszczania oprogramowania pośredniczącego w potoku przetwarzania żądań i w ramach jakich warunków oprogramowanie pośredniczące może przerwać przetwarzanie żądań. W przypadku krótkiego obwody przez oprogramowanie pośredniczące proces przetwarzania żądań i zapobiega przetwarzaniu żądania przez dalsze *podrzędne oprogramowanie pośredniczące.* Aby uzyskać więcej informacji na temat skracania obwodów, zobacz sekcję [Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .

| Oprogramowanie pośredniczące | Opis | Zamówienie |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Zapewnia obsługę uwierzytelniania. | Przed zainstalowaniem `HttpContext.User` . Terminal dla wywołań zwrotnych uwierzytelniania OAuth. |
| [Cookie Zasad](xref:security/gdpr) | Śledzi zgodę użytkowników na przechowywanie informacji osobistych i wymusza minimalne standardy dla cookie pól, takich jak `secure` i `SameSite` . | Przed wystawianym przez oprogramowanie pośredniczące cookie . Przykłady: uwierzytelnianie, sesja, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje udostępnianie zasobów między źródłami. | Przed składnikami korzystającymi z mechanizmu CORS. |
| [Diagnostyka](xref:fundamentals/error-handling) | Kilka oddzielnych middlewares, które udostępniają stronę wyjątku dewelopera, obsługę wyjątków, strony kodu stanu i domyślną stronę sieci Web dla nowych aplikacji. | Przed składnikami, które generują błędy. Terminal dla wyjątków lub obsługa domyślnej strony sieci Web dla nowych aplikacji. |
| [Nagłówki przesłane dalej](xref:host-and-deploy/proxy-load-balancer) | Przekazuje nagłówki proxy do bieżącego żądania. | Przed składnikami, które zużywają zaktualizowane pola. Przykłady: schemat, host, adres IP klienta, metoda. |
| [Sprawdzenie kondycji](xref:host-and-deploy/health-checks) | Sprawdza kondycję aplikacji ASP.NET Core i jej zależności, na przykład sprawdzanie dostępności bazy danych. | Terminal, jeśli żądanie pasuje do punktu końcowego sprawdzania kondycji. |
| [Zastąpienie metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Zezwala na przychodzące żądanie POST przesłaniające metodę. | Przed składnikami, które zużywają zaktualizowaną metodę. |
| [Przekierowanie HTTPS](xref:security/enforcing-ssl#require-https) | Przekierowuj wszystkie żądania HTTP do protokołu HTTPS. | Przed składnikami, które używają adresu URL. |
| [Zabezpieczenia protokołu HTTP Strict Transport (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Ulepszanie zabezpieczeń oprogramowanie pośredniczące, które dodaje specjalny nagłówek odpowiedzi. | Przed wysłaniem odpowiedzi i po składnikach, które modyfikują żądania. Przykłady: nagłówki przesłane dalej, ponowne zapisywanie adresów URL. |
| [MVC](xref:mvc/overview) | Przetwarza żądania ze MVC/ Razor Pages. | Terminal, jeśli żądanie pasuje do trasy. |
| [OWIN](xref:fundamentals/owin) | Współdziałanie z aplikacjami opartymi na OWIN, serwerami i oprogramowanie pośredniczące. | Terminal, jeśli oprogramowanie OWIN w pełni przetwarza żądanie. |
| [Buforowanie odpowiedzi](xref:performance/caching/middleware) | Zapewnia obsługę buforowania odpowiedzi. | Przed składnikami, które wymagają buforowania. |
| [Kompresja odpowiedzi](xref:performance/response-compression) | Zapewnia obsługę kompresowania odpowiedzi. | Przed składnikami wymagającymi kompresji. |
| [Lokalizacja żądania](xref:fundamentals/localization) | Zapewnia obsługę lokalizacji. | Przed uwzględnieniem poufnych składników lokalizacji. |
| [Routing punktów końcowych](xref:fundamentals/routing) | Definiuje trasy żądań i ogranicza je. | Terminal dla pasujących tras. |
| [Sesja](xref:fundamentals/app-state) | Zapewnia obsługę zarządzania sesjami użytkowników. | Przed składnikami, które wymagają sesji. |
| [Pliki statyczne](xref:fundamentals/static-files) | Zapewnia obsługę plików statycznych i przeglądania katalogów. | Terminal, jeśli żądanie pasuje do pliku. |
| [Ponowne zapisywanie adresów URL](xref:fundamentals/url-rewriting) | Zapewnia obsługę ponownego zapisywania adresów URL i Przekierowywanie żądań. | Przed składnikami, które używają adresu URL. |
| [Protokoły WebSocket](xref:fundamentals/websockets) | Włącza protokół WebSockets. | Przed składnikami, które są wymagane do akceptowania żądań WebSocket. |

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
