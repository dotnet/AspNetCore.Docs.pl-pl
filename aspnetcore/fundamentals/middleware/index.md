---
title: ASP.NET Podstawowe oprogramowanie pośredniczące
author: rick-anderson
description: Dowiedz się więcej o ASP.NET oprogramowaniu pośredniczącym Core i potoku żądań.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 6bf8ed823386ca4e1cf78982f7fba41fba429db8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751093"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Podstawowe oprogramowanie pośredniczące

::: moniker range=">= aspnetcore-3.0"

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Steve [Smith](https://ardalis.com/)

Oprogramowanie pośredniczące to oprogramowanie, które jest montowane w potoku aplikacji do obsługi żądań i odpowiedzi. Każdy składnik:

* Wybiera, czy przekazać żądanie do następnego składnika w potoku.
* Można wykonywać pracę przed i po następnym składniku w potoku.

Delegatów żądania są używane do tworzenia potoku żądania. Delegaci żądania obsługują każde żądanie HTTP.

Pełnomocników żądania są <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>konfigurowane przy użyciu , <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>i <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> metody rozszerzenia. Delegata pojedynczego żądania można określić w wierszu jako metodę anonimową (nazywaną oprogramowaniem pośredniczącym w wierszu) lub można ją zdefiniować w klasie wielokrotnegoużynia. Te klasy wielokrotnegoużynowania i metody anonimowe w wierszu są *oprogramowaniem pośredniczącym,* zwanym również *składnikami oprogramowania pośredniczącego.* Każdy składnik oprogramowania pośredniczącego w potoku żądań jest odpowiedzialny za wywoływanie następnego składnika w potoku lub zwarcie potoku. Gdy oprogramowanie pośredniczące zwarcia, to się nazywa *oprogramowanie pośredniczące terminalu,* ponieważ uniemożliwia dalsze oprogramowanie pośredniczące przetwarzania żądania.

<xref:migration/http-modules>wyjaśnia różnicę między potokami żądań w ASP.NET Core i ASP.NET 4.x i udostępnia dodatkowe przykłady oprogramowania pośredniczącego.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Tworzenie potoku oprogramowania pośredniczącego za pomocą aplikacji IApplicationBuilder

Potok żądania ASP.NET Core składa się z sekwencji delegatów żądania, wywoływanych jeden po drugim. Na poniższym diagramie przedstawiono koncepcję. Wątek wykonania następuje czarne strzałki.

![Zażądaj wzorca przetwarzania przedstawiającego przychodzące żądanie, przetwarzanie za pomocą trzech oprogramowania pośredniczącego i odpowiedź opuszczającą aplikację. Każde oprogramowanie pośredniczące uruchamia swoją logikę i przekazuje żądanie do następnego oprogramowania pośredniczącego w instrukcji next(). Po trzecim oprogramowaniu pośredniczącym przetwarza żądanie, żądanie przechodzi z powrotem przez poprzednie dwa oprogramowanie pośredniczące w odwrotnej kolejności do dodatkowego przetwarzania po ich next() instrukcji przed opuszczeniem aplikacji jako odpowiedź na klienta.](index/_static/request-delegate-pipeline.png)

Każdy pełnomocnik może wykonywać operacje przed i po następnym pełnomocniku. Delegatów obsługi wyjątków powinny być wywoływane na początku potoku, dzięki czemu mogą przechwytywać wyjątki, które występują w późniejszych etapach potoku.

Najprostsze możliwe ASP.NET Core aplikacja konfiguruje delegata pojedynczego żądania, który obsługuje wszystkie żądania. Ten przypadek nie obejmuje potoku żądania rzeczywistego. Zamiast tego pojedyncza funkcja anonimowa jest wywoływana w odpowiedzi na każde żądanie HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Łańcuch wielu delegatów <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>żądania wraz z . Parametr `next` reprezentuje następnego delegata w potoku. Potoku można zwarcie, *nie* wywołując *następnego* parametru. Zazwyczaj można wykonywać akcje zarówno przed, jak i po następnym delegatu, jak pokazano w poniższym przykładzie:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Gdy pełnomocnik nie przekazuje żądania do następnego pełnomocnika, nazywa *się zwarcie potoku żądania.* Zwarcie jest często pożądane, ponieważ pozwala uniknąć niepotrzebnej pracy. Na przykład [oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) może działać jako *oprogramowanie pośredniczące terminalu,* przetwarzając żądanie pliku statycznego i zwarcie pozostałej części potoku. Oprogramowanie pośredniczące dodane do potoku przed oprogramowaniem pośredniczącym, które kończy dalsze przetwarzanie, nadal przetwarza kod po ich `next.Invoke` instrukcjach. Jednak zobacz następujące ostrzeżenie dotyczące próby zapisu do odpowiedzi, która została już wysłana.

> [!WARNING]
> Nie dzwoń po `next.Invoke` wysłaniu odpowiedzi do klienta. Zmiany <xref:Microsoft.AspNetCore.Http.HttpResponse> po rozpoczęciu odpowiedzi zgłosić wyjątek. Na przykład zmiany, takie jak ustawianie nagłówków i kod stanu zgłosić wyjątek. Pisanie do organu odpowiedzi `next`po wywołaniu:
>
> * Może spowodować naruszenie protokołu. Na przykład pisanie więcej `Content-Length`niż podano .
> * Może uszkodzić format ciała. Na przykład zapisywanie stopki HTML do pliku CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>jest użyteczną wskazówką wskazującą, czy nagłówki zostały wysłane lub treść została napisana.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>delegaci nie `next` otrzymują parametru. Pierwszy `Run` pełnomocnik jest zawsze terminalu i kończy potoku. `Run`jest konwencją. Niektóre składniki oprogramowania `Run[Middleware]` pośredniczącego mogą udostępniać metody, które są uruchamiane na końcu potoku:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

W poprzednim przykładzie `Run` delegat zapisuje `"Hello from 2nd delegate."` do odpowiedzi, a następnie kończy potoku. Jeśli `Use` inny `Run` lub delegat `Run` zostanie dodany po pełnomocnika, nie jest wywoływana.

<a name="order"></a>

## <a name="middleware-order"></a>Kolejność oprogramowania pośredniczącego

Na poniższym diagramie przedstawiono potok przetwarzania całego żądania dla aplikacji ASP.NET Core MVC i Razor Pages. Możesz zobaczyć, jak w typowej aplikacji są uporządkowane istniejące oprogramowanie pośredniczące i gdzie dodawane są niestandardowe oprogramowanie pośredniczące. Masz pełną kontrolę nad tym, jak zmienić kolejność istniejących oprogramowania pośredniczącego lub wstrzyknąć nowe niestandardowe oprogramowanie pośredniczące, jeśli jest to konieczne dla scenariuszy.

![ASP.NET potoku oprogramowania pośredniczącego core](index/_static/middleware-pipeline.svg)

Oprogramowanie **pośredniczące punktu końcowego** na poprzednim diagramie wykonuje&mdash;potok filtrów dla odpowiedniego typu aplikacji MVC lub Razor Pages.

![ASP.NET potok filtrów Core](index/_static/mvc-endpoint.svg)

Kolejność, w jakiej składniki `Startup.Configure` oprogramowania pośredniczącego są dodawane w metodzie, definiuje kolejność wywoływania składników oprogramowania pośredniczącego na żądaniach i odwrotną kolejność odpowiedzi. Kolejność ma **kluczowe znaczenie** dla zabezpieczeń, wydajności i funkcjonalności.

Następująca `Startup.Configure` metoda dodaje składniki oprogramowania pośredniczącego związane z zabezpieczeniami w zalecanej kolejności:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Oprogramowanie pośredniczące, które nie jest dodawane podczas tworzenia nowej aplikacji internetowej z [kontami poszczególnych użytkowników,](xref:security/authentication/identity) jest komentowane.
* Nie każde oprogramowanie pośredniczące musi iść w tej dokładnej kolejności, ale wiele z nich. Na przykład `UseCors` `UseAuthentication`, `UseAuthorization` i musi iść w kolejności pokazanej.

Następująca `Startup.Configure` metoda dodaje składniki oprogramowania pośredniczącego dla typowych scenariuszy aplikacji:

1. Obsługa wyjątków/błędów
   * Gdy aplikacja działa w środowisku deweloperskim:
     * Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>strony wyjątków dewelopera ( ) zgłasza błędy środowiska uruchomieniowego aplikacji.
     * Oprogramowanie pośredniczące strony błędu bazy danych zgłasza błędy środowiska uruchomieniowego bazy danych.
   * Gdy aplikacja działa w środowisku produkcyjnym:
     * Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>obsługi wyjątków ( ) przechwytuje wyjątki generowane w następujących oprogramowaniach pośredniczących.
     * Protokół HTTP Strict Transport Security Protocol<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>(HSTS) Middleware ( ) dodaje `Strict-Transport-Security` nagłówek.
1. Oprogramowanie pośredniczące przekierowania HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) przekierowuje żądania HTTP do https.
1. Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>plików statycznych ( ) zwraca pliki statyczne i zwarcia dalsze przetwarzanie żądania.
1. Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>w polityce cookie ( ) jest zgodne z przepisami ogólnego rozporządzenia o ochronie danych (RODO) UE.
1. Oprogramowanie pośredniczące`UseRouting`routingu ( ) do kierowania żądań.
1. Oprogramowanie pośredniczące uwierzytelniania (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) próbuje uwierzytelnić użytkownika, zanim uzyska dostęp do bezpiecznych zasobów.
1. Oprogramowanie pośredniczące autoryzacji (`UseAuthorization`) upoważnia użytkownika do dostępu do bezpiecznych zasobów.
1. Oprogramowanie pośredniczące sesji (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) ustanawia i utrzymuje stan sesji. Jeśli aplikacja używa stanu sesji, wywołaj oprogramowanie pośredniczące sesji po oprogramowaniu pośredniczącym zasad plików cookie i przed oprogramowaniem pośredniczącym MVC.
1. Oprogramowanie pośredniczące routingu punktu końcowego (z`UseEndpoints` `MapRazorPages`) w celu dodania punktów końcowych razor Pages do potoku żądań.

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

W poprzednim przykładzie kodu każda metoda rozszerzenia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> oprogramowania <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> pośredniczącego jest narażona za pośrednictwem obszaru nazw.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>jest pierwszym składnikiem oprogramowania pośredniczącego dodanego do potoku. W związku z tym oprogramowanie pośredniczące obsługi wyjątków połowy wszelkie wyjątki, które występują w późniejszych wywołań.

Oprogramowanie pośredniczące plików statycznych jest wywoływane na wczesnym etapie potoku, dzięki czemu może obsługiwać żądania i zwarcie bez przechodzenia przez pozostałe składniki. Oprogramowanie pośredniczące plików statycznych **nie** zapewnia żadnych kontroli autoryzacji. Wszystkie pliki obsługiwane przez oprogramowanie pośredniczące plików statycznych, w tym te pod *wwwroot*, są publicznie dostępne. Aby uzyskać podejście do zabezpieczania plików statycznych, zobacz <xref:fundamentals/static-files>.

Jeśli żądanie nie jest obsługiwane przez oprogramowanie pośredniczące plików statycznych, jest przekazywane<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>do oprogramowania pośredniczącego uwierzytelniania ( ), który wykonuje uwierzytelnianie. Uwierzytelnianie nie zwiera nieuwierzyte żądania. Chociaż oprogramowanie pośredniczące uwierzytelniania uwierzytelnia żądania, autoryzacja (i odrzucenie) występuje tylko wtedy, gdy MVC wybierze określoną stronę Razor lub kontroler MVC i akcję.

W poniższym przykładzie pokazano kolejność oprogramowania pośredniczącego, w którym żądania plików statycznych są obsługiwane przez oprogramowanie pośredniczące plików statycznych przed oprogramowaniem pośredniczącym kompresji odpowiedzi. Pliki statyczne nie są kompresowane z tej kolejności oprogramowania pośredniczącego. Odpowiedzi strony razor mogą być skompresowane.

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

W przypadku aplikacji jednostronicowych (SPA) oprogramowanie pośredniczące <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> SPA zwykle zajmuje ostatnie miejsce w potoku oprogramowania pośredniczącego. Oprogramowanie pośredniczące SPA jest na końcu:

* Aby umożliwić wszystkie inne oprogramowanie pośredniczące, aby najpierw odpowiadać na pasujące żądania.
* Aby umożliwić spa z routingu po stronie klienta do uruchamiania dla wszystkich tras, które nie są rozpoznawane przez aplikację serwera.

Aby uzyskać więcej informacji na temat oso, zobacz przewodniki dla [react](xref:spa/react) i [angular](xref:spa/angular) szablonów projektu.

## <a name="branch-the-middleware-pipeline"></a>Rozgałęzić potok oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>rozszerzenia są używane jako konwencja do rozgałęzienia potoku. `Map`odgałęzia potoku żądań na podstawie dopasowań danej ścieżki żądania. Jeśli ścieżka żądania rozpoczyna się od danej ścieżki, gałąź jest wykonywana.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

W poniższej tabeli przedstawiono `http://localhost:1234` żądania i odpowiedzi z użycia poprzedniego kodu.

| Żądanie             | Odpowiedź                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Witam od delegata spoza mapy. |
| localhost:1234/map1 | Test mapy 1                   |
| localhost:1234/map2 | Test mapy 2                   |
| localhost:1234/map3 | Witam od delegata spoza mapy. |

Gdy `Map` jest używany, dopasowane segmenty ścieżki `HttpRequest.Path` są usuwane `HttpRequest.PathBase` i dołączane do każdego żądania.

`Map`obsługuje zagnieżdżanie, na przykład:

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

`Map`może również dopasować wiele segmentów jednocześnie:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>odgałęzia potok żądania na podstawie wyniku danego predykatu. Każdy predykat `Func<HttpContext, bool>` typu może służyć do mapowania żądań do nowej gałęzi potoku. W poniższym przykładzie predykat jest używany do wykrywania `branch`obecności zmiennej ciągu zapytania:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

W poniższej tabeli przedstawiono `http://localhost:1234` żądania i odpowiedzi z użycia poprzedniego kodu:

| Żądanie                       | Odpowiedź                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Witam od delegata spoza mapy. |
| localhost:1234/?branch=master | Używana gałąź = wzorzec         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*>również oddziały potoku żądania na podstawie wyniku danego predykatu. W `MapWhen`przeciwieństwie do tej gałęzi jest ponownie dołączany do głównego rurociągu, jeśli nie zwarcia lub zawiera oprogramowanie pośredniczące terminalu:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

W poprzednim przykładzie odpowiedź "Hello z głównego potoku." jest napisane dla wszystkich wniosków. Jeśli żądanie zawiera zmienną `branch`ciągu zapytania, jego wartość jest rejestrowana przed ponownym dołączeniem potoku głównego.

## <a name="built-in-middleware"></a>Wbudowane oprogramowanie pośredniczące

ASP.NET Core jest dostarczany z następującymi składnikami oprogramowania pośredniczącego. *Kolumna Zamówienie* zawiera uwagi dotyczące umieszczania oprogramowania pośredniczącego w potoku przetwarzania żądań i na jakich warunkach oprogramowanie pośredniczące może zakończyć przetwarzanie żądania. Gdy oprogramowanie pośredniczące zwarcia potoku przetwarzania żądania i uniemożliwia dalsze oprogramowanie pośredniczące niższego rzędu przetwarzania żądania, to się nazywa *oprogramowanie pośredniczące terminalu*. Aby uzyskać więcej informacji na temat zwarcia, zobacz [Tworzenie potoku oprogramowania pośredniczącego z iApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) sekcji.

| Oprogramowanie pośredniczące | Opis | Zamówienie |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Zapewnia obsługę uwierzytelniania. | Wcześniej `HttpContext.User` jest to potrzebne. Terminal dla wywołań zwrotnych OAuth. |
| [Autoryzacja](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | Zapewnia obsługę autoryzacji. | Natychmiast po uwierzytelniania oprogramowanie pośredniczące. |
| [Polityka dotycząca plików cookie](xref:security/gdpr) | Śledzi zgodę użytkowników na przechowywanie danych osobowych i wymusza `secure` `SameSite`minimalne standardy dla pól plików cookie, takich jak i . | Przed oprogramowaniem pośredniczącym, które wystawia pliki cookie. Przykłady: Uwierzytelnianie, Sesja, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje udostępnianie zasobów między źródłami. | Przed komponentami, które używają CORS. |
| [Diagnostyka](xref:fundamentals/error-handling) | Kilka oddzielnych programów pośredniczących, które zapewniają stronę wyjątków dla deweloperów, obsługę wyjątków, strony kodu stanu i domyślną stronę sieci Web dla nowych aplikacji. | Przed składnikami, które generują błędy. Terminal dla wyjątków lub obsługującej domyślną stronę internetową dla nowych aplikacji. |
| [Nagłówki przekazywane dalej](xref:host-and-deploy/proxy-load-balancer) | Przekazuje nagłówki proksowane do bieżącego żądania. | Przed składnikami, które zużywają zaktualizowane pola. Przykłady: schemat, host, adres IP klienta, metoda. |
| [Kontrola kondycji](xref:host-and-deploy/health-checks) | Sprawdza kondycję aplikacji ASP.NET Core i jej zależności, takie jak sprawdzanie dostępności bazy danych. | Terminal, jeśli żądanie pasuje do punktu końcowego sprawdzania kondycji. |
| [Propagacja nagłówka](xref:fundamentals/http-requests#header-propagation-middleware) | Propaguje nagłówki HTTP z żądania przychodzącego do wychodzących żądań klienta HTTP. |
| [Zastępowanie metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umożliwia przychodzące żądanie POST, aby zastąpić metodę. | Przed składnikami, które zużywają zaktualizowaną metodę. |
| [Przekierowanie HTTPS](xref:security/enforcing-ssl#require-https) | Przekieruj wszystkie żądania HTTP do protokołu HTTPS. | Przed składnikami, które zużywają adres URL. |
| [Ścisłe bezpieczeństwo transportu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Oprogramowanie pośredniczące zwiększające bezpieczeństwo, które dodaje specjalny nagłówek odpowiedzi. | Przed odpowiedzi są wysyłane i po składniki, które modyfikują żądania. Przykłady: Przesłane dalej nagłówki, przepisywanie adresów URL. |
| [MVC](xref:mvc/overview) | Przetwarza żądania za pomocą stron MVC/Razor. | Terminal, jeśli żądanie pasuje do trasy. |
| [OWIN](xref:fundamentals/owin) | Współpracuj z aplikacjami, serwerami i oprogramowaniem pośredniczącym opartym na OWIN. | Terminal, jeśli oprogramowanie pośredniczące OWIN w pełni przetwarza żądanie. |
| [Buforowanie odpowiedzi](xref:performance/caching/middleware) | Zapewnia obsługę buforowania odpowiedzi. | Przed komponentami, które wymagają buforowania. |
| [Kompresja odpowiedzi](xref:performance/response-compression) | Zapewnia obsługę kompresji odpowiedzi. | Przed komponentami, które wymagają kompresji. |
| [Poproś o lokalizację](xref:fundamentals/localization) | Zapewnia obsługę lokalizacji. | Przed lokalizacją wrażliwych składników. |
| [Routing punktu końcowego](xref:fundamentals/routing) | Definiuje i ogranicza trasy żądań. | Terminal do pasujących tras. |
| [Spa](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | Obsługuje wszystkie żądania z tego punktu w łańcuchu oprogramowania pośredniczącego, zwracając domyślną stronę dla aplikacji jednostronicowej (SPA) | Późno w łańcuchu, tak aby inne oprogramowanie pośredniczące do obsługi plików statycznych, akcje MVC, itp., ma pierwszeństwo.|
| [Sesja](xref:fundamentals/app-state) | Zapewnia obsługę zarządzania sesjami użytkowników. | Przed składnikami, które wymagają sesji. | 
| [Pliki statyczne](xref:fundamentals/static-files) | Zapewnia obsługę obsługi plików statycznych i przeglądania katalogów. | Terminal, jeśli żądanie pasuje do pliku. |
| [Ponowne zapisywanie adresów URL](xref:fundamentals/url-rewriting) | Zapewnia obsługę przepisywania adresów URL i przekierowywania żądań. | Przed składnikami, które zużywają adres URL. |
| [Protokoły WebSocket](xref:fundamentals/websockets) | Włącza protokół WebSockets. | Przed składników, które są wymagane do akceptowania żądań WebSocket. |

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Steve [Smith](https://ardalis.com/)

Oprogramowanie pośredniczące to oprogramowanie, które jest montowane w potoku aplikacji do obsługi żądań i odpowiedzi. Każdy składnik:

* Wybiera, czy przekazać żądanie do następnego składnika w potoku.
* Można wykonywać pracę przed i po następnym składniku w potoku.

Delegatów żądania są używane do tworzenia potoku żądania. Delegaci żądania obsługują każde żądanie HTTP.

Pełnomocników żądania są <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>konfigurowane przy użyciu , <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>i <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> metody rozszerzenia. Delegata pojedynczego żądania można określić w wierszu jako metodę anonimową (nazywaną oprogramowaniem pośredniczącym w wierszu) lub można ją zdefiniować w klasie wielokrotnegoużynia. Te klasy wielokrotnegoużynowania i metody anonimowe w wierszu są *oprogramowaniem pośredniczącym,* zwanym również *składnikami oprogramowania pośredniczącego.* Każdy składnik oprogramowania pośredniczącego w potoku żądań jest odpowiedzialny za wywoływanie następnego składnika w potoku lub zwarcie potoku. Gdy oprogramowanie pośredniczące zwarcia, to się nazywa *oprogramowanie pośredniczące terminalu,* ponieważ uniemożliwia dalsze oprogramowanie pośredniczące przetwarzania żądania.

<xref:migration/http-modules>wyjaśnia różnicę między potokami żądań w ASP.NET Core i ASP.NET 4.x i udostępnia dodatkowe przykłady oprogramowania pośredniczącego.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Tworzenie potoku oprogramowania pośredniczącego za pomocą aplikacji IApplicationBuilder

Potok żądania ASP.NET Core składa się z sekwencji delegatów żądania, wywoływanych jeden po drugim. Na poniższym diagramie przedstawiono koncepcję. Wątek wykonania następuje czarne strzałki.

![Zażądaj wzorca przetwarzania przedstawiającego przychodzące żądanie, przetwarzanie za pomocą trzech oprogramowania pośredniczącego i odpowiedź opuszczającą aplikację. Każde oprogramowanie pośredniczące uruchamia swoją logikę i przekazuje żądanie do następnego oprogramowania pośredniczącego w instrukcji next(). Po trzecim oprogramowaniu pośredniczącym przetwarza żądanie, żądanie przechodzi z powrotem przez poprzednie dwa oprogramowanie pośredniczące w odwrotnej kolejności do dodatkowego przetwarzania po ich next() instrukcji przed opuszczeniem aplikacji jako odpowiedź na klienta.](index/_static/request-delegate-pipeline.png)

Każdy pełnomocnik może wykonywać operacje przed i po następnym pełnomocniku. Delegatów obsługi wyjątków powinny być wywoływane na początku potoku, dzięki czemu mogą przechwytywać wyjątki, które występują w późniejszych etapach potoku.

Najprostsze możliwe ASP.NET Core aplikacja konfiguruje delegata pojedynczego żądania, który obsługuje wszystkie żądania. Ten przypadek nie obejmuje potoku żądania rzeczywistego. Zamiast tego pojedyncza funkcja anonimowa jest wywoływana w odpowiedzi na każde żądanie HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Pierwszy <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegat kończy potok.

Łańcuch wielu delegatów <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>żądania wraz z . Parametr `next` reprezentuje następnego delegata w potoku. Potoku można zwarcie, *nie* wywołując *następnego* parametru. Zazwyczaj można wykonywać akcje zarówno przed, jak i po następnym delegatu, jak pokazano w poniższym przykładzie:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Gdy pełnomocnik nie przekazuje żądania do następnego pełnomocnika, nazywa *się zwarcie potoku żądania.* Zwarcie jest często pożądane, ponieważ pozwala uniknąć niepotrzebnej pracy. Na przykład [oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) może działać jako *oprogramowanie pośredniczące terminalu,* przetwarzając żądanie pliku statycznego i zwarcie pozostałej części potoku. Oprogramowanie pośredniczące dodane do potoku przed oprogramowaniem pośredniczącym, które kończy dalsze przetwarzanie, nadal przetwarza kod po ich `next.Invoke` instrukcjach. Jednak zobacz następujące ostrzeżenie dotyczące próby zapisu do odpowiedzi, która została już wysłana.

> [!WARNING]
> Nie dzwoń po `next.Invoke` wysłaniu odpowiedzi do klienta. Zmiany <xref:Microsoft.AspNetCore.Http.HttpResponse> po rozpoczęciu odpowiedzi zgłosić wyjątek. Na przykład zmiany, takie jak ustawianie nagłówków i kod stanu zgłosić wyjątek. Pisanie do organu odpowiedzi `next`po wywołaniu:
>
> * Może spowodować naruszenie protokołu. Na przykład pisanie więcej `Content-Length`niż podano .
> * Może uszkodzić format ciała. Na przykład zapisywanie stopki HTML do pliku CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>jest użyteczną wskazówką wskazującą, czy nagłówki zostały wysłane lub treść została napisana.

<a name="order"></a>

## <a name="middleware-order"></a>Kolejność oprogramowania pośredniczącego

Kolejność, w jakiej składniki `Startup.Configure` oprogramowania pośredniczącego są dodawane w metodzie, definiuje kolejność wywoływania składników oprogramowania pośredniczącego na żądaniach i odwrotną kolejność odpowiedzi. Kolejność ma **kluczowe znaczenie** dla zabezpieczeń, wydajności i funkcjonalności.

Następująca `Startup.Configure` metoda dodaje składniki oprogramowania pośredniczącego związane z zabezpieczeniami w zalecanej kolejności:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Oprogramowanie pośredniczące, które nie jest dodawane podczas tworzenia nowej aplikacji internetowej z [kontami poszczególnych użytkowników,](xref:security/authentication/identity) jest komentowane.
* Nie każde oprogramowanie pośredniczące musi iść w tej dokładnej kolejności, ale wiele z nich. Na przykład `UseCors` `UseAuthentication` i musi iść w kolejności pokazanej.

Następująca `Startup.Configure` metoda dodaje składniki oprogramowania pośredniczącego dla typowych scenariuszy aplikacji:

1. Obsługa wyjątków/błędów
   * Gdy aplikacja działa w środowisku deweloperskim:
     * Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>strony wyjątków dewelopera ( ) zgłasza błędy środowiska uruchomieniowego aplikacji.
     * Oprogramowanie pośredniczące`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`strony błędu bazy danych ( ) zgłasza błędy środowiska uruchomieniowego bazy danych.
   * Gdy aplikacja działa w środowisku produkcyjnym:
     * Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>obsługi wyjątków ( ) przechwytuje wyjątki generowane w następujących oprogramowaniach pośredniczących.
     * Protokół HTTP Strict Transport Security Protocol<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>(HSTS) Middleware ( ) dodaje `Strict-Transport-Security` nagłówek.
1. Oprogramowanie pośredniczące przekierowania HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) przekierowuje żądania HTTP do https.
1. Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>plików statycznych ( ) zwraca pliki statyczne i zwarcia dalsze przetwarzanie żądania.
1. Oprogramowanie pośredniczące<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>w polityce cookie ( ) jest zgodne z przepisami ogólnego rozporządzenia o ochronie danych (RODO) UE.
1. Oprogramowanie pośredniczące uwierzytelniania (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) próbuje uwierzytelnić użytkownika, zanim uzyska dostęp do bezpiecznych zasobów.
1. Oprogramowanie pośredniczące sesji (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) ustanawia i utrzymuje stan sesji. Jeśli aplikacja używa stanu sesji, wywołaj oprogramowanie pośredniczące sesji po oprogramowaniu pośredniczącym zasad plików cookie i przed oprogramowaniem pośredniczącym MVC.
1. MVC<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>( ), aby dodać MVC do potoku żądania.

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

W poprzednim przykładzie kodu każda metoda rozszerzenia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> oprogramowania <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> pośredniczącego jest narażona za pośrednictwem obszaru nazw.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>jest pierwszym składnikiem oprogramowania pośredniczącego dodanego do potoku. W związku z tym oprogramowanie pośredniczące obsługi wyjątków połowy wszelkie wyjątki, które występują w późniejszych wywołań.

Oprogramowanie pośredniczące plików statycznych jest wywoływane na wczesnym etapie potoku, dzięki czemu może obsługiwać żądania i zwarcie bez przechodzenia przez pozostałe składniki. Oprogramowanie pośredniczące plików statycznych **nie** zapewnia żadnych kontroli autoryzacji. Wszystkie pliki obsługiwane przez oprogramowanie pośredniczące plików statycznych, w tym te pod *wwwroot*, są publicznie dostępne. Aby uzyskać podejście do zabezpieczania plików statycznych, zobacz <xref:fundamentals/static-files>.

Jeśli żądanie nie jest obsługiwane przez oprogramowanie pośredniczące plików statycznych, jest przekazywane<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>do oprogramowania pośredniczącego uwierzytelniania ( ), który wykonuje uwierzytelnianie. Uwierzytelnianie nie zwiera nieuwierzyte żądania. Chociaż oprogramowanie pośredniczące uwierzytelniania uwierzytelnia żądania, autoryzacja (i odrzucenie) występuje tylko wtedy, gdy MVC wybierze określoną stronę Razor lub kontroler MVC i akcję.

W poniższym przykładzie pokazano kolejność oprogramowania pośredniczącego, w którym żądania plików statycznych są obsługiwane przez oprogramowanie pośredniczące plików statycznych przed oprogramowaniem pośredniczącym kompresji odpowiedzi. Pliki statyczne nie są kompresowane z tej kolejności oprogramowania pośredniczącego. Odpowiedzi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> mogą być skompresowane.

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

Skonfiguruj <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>potok <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>HTTP <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>za pomocą , i . Metoda `Use` może zwarcie potoku (oznacza to, że `next` jeśli nie wywoła delegata żądania). `Run`jest konwencją, a niektóre składniki `Run[Middleware]` oprogramowania pośredniczącego mogą udostępniać metody, które są uruchamiane na końcu potoku.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>rozszerzenia są używane jako konwencja do rozgałęzienia potoku. `Map`odgałęzia potoku żądań na podstawie dopasowań danej ścieżki żądania. Jeśli ścieżka żądania rozpoczyna się od danej ścieżki, gałąź jest wykonywana.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

W poniższej tabeli przedstawiono `http://localhost:1234` żądania i odpowiedzi z użycia poprzedniego kodu.

| Żądanie             | Odpowiedź                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Witam od delegata spoza mapy. |
| localhost:1234/map1 | Test mapy 1                   |
| localhost:1234/map2 | Test mapy 2                   |
| localhost:1234/map3 | Witam od delegata spoza mapy. |

Gdy `Map` jest używany, dopasowane segmenty ścieżki `HttpRequest.Path` są usuwane `HttpRequest.PathBase` i dołączane do każdego żądania.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>odgałęzia potok żądania na podstawie wyniku danego predykatu. Każdy predykat `Func<HttpContext, bool>` typu może służyć do mapowania żądań do nowej gałęzi potoku. W poniższym przykładzie predykat jest używany do wykrywania `branch`obecności zmiennej ciągu zapytania:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

W poniższej tabeli przedstawiono `http://localhost:1234` żądania i odpowiedzi z użycia poprzedniego kodu.

| Żądanie                       | Odpowiedź                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Witam od delegata spoza mapy. |
| localhost:1234/?branch=master | Używana gałąź = wzorzec         |

`Map`obsługuje zagnieżdżanie, na przykład:

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

`Map`może również dopasować wiele segmentów jednocześnie:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Wbudowane oprogramowanie pośredniczące

ASP.NET Core jest dostarczany z następującymi składnikami oprogramowania pośredniczącego. *Kolumna Zamówienie* zawiera uwagi dotyczące umieszczania oprogramowania pośredniczącego w potoku przetwarzania żądań i na jakich warunkach oprogramowanie pośredniczące może zakończyć przetwarzanie żądania. Gdy oprogramowanie pośredniczące zwarcia potoku przetwarzania żądania i uniemożliwia dalsze oprogramowanie pośredniczące niższego rzędu przetwarzania żądania, to się nazywa *oprogramowanie pośredniczące terminalu*. Aby uzyskać więcej informacji na temat zwarcia, zobacz [Tworzenie potoku oprogramowania pośredniczącego z iApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) sekcji.

| Oprogramowanie pośredniczące | Opis | Zamówienie |
| ---------- | ----------- | ----- |
| [Authentication](xref:security/authentication/identity) | Zapewnia obsługę uwierzytelniania. | Wcześniej `HttpContext.User` jest to potrzebne. Terminal dla wywołań zwrotnych OAuth. |
| [Polityka dotycząca plików cookie](xref:security/gdpr) | Śledzi zgodę użytkowników na przechowywanie danych osobowych i wymusza `secure` `SameSite`minimalne standardy dla pól plików cookie, takich jak i . | Przed oprogramowaniem pośredniczącym, które wystawia pliki cookie. Przykłady: Uwierzytelnianie, Sesja, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguruje udostępnianie zasobów między źródłami. | Przed komponentami, które używają CORS. |
| [Diagnostyka](xref:fundamentals/error-handling) | Kilka oddzielnych programów pośredniczących, które zapewniają stronę wyjątków dla deweloperów, obsługę wyjątków, strony kodu stanu i domyślną stronę sieci Web dla nowych aplikacji. | Przed składnikami, które generują błędy. Terminal dla wyjątków lub obsługującej domyślną stronę internetową dla nowych aplikacji. |
| [Nagłówki przekazywane dalej](xref:host-and-deploy/proxy-load-balancer) | Przekazuje nagłówki proksowane do bieżącego żądania. | Przed składnikami, które zużywają zaktualizowane pola. Przykłady: schemat, host, adres IP klienta, metoda. |
| [Kontrola kondycji](xref:host-and-deploy/health-checks) | Sprawdza kondycję aplikacji ASP.NET Core i jej zależności, takie jak sprawdzanie dostępności bazy danych. | Terminal, jeśli żądanie pasuje do punktu końcowego sprawdzania kondycji. |
| [Zastępowanie metody HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Umożliwia przychodzące żądanie POST, aby zastąpić metodę. | Przed składnikami, które zużywają zaktualizowaną metodę. |
| [Przekierowanie HTTPS](xref:security/enforcing-ssl#require-https) | Przekieruj wszystkie żądania HTTP do protokołu HTTPS. | Przed składnikami, które zużywają adres URL. |
| [Ścisłe bezpieczeństwo transportu HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Oprogramowanie pośredniczące zwiększające bezpieczeństwo, które dodaje specjalny nagłówek odpowiedzi. | Przed odpowiedzi są wysyłane i po składniki, które modyfikują żądania. Przykłady: Przesłane dalej nagłówki, przepisywanie adresów URL. |
| [MVC](xref:mvc/overview) | Przetwarza żądania za pomocą stron MVC/Razor. | Terminal, jeśli żądanie pasuje do trasy. |
| [OWIN](xref:fundamentals/owin) | Współpracuj z aplikacjami, serwerami i oprogramowaniem pośredniczącym opartym na OWIN. | Terminal, jeśli oprogramowanie pośredniczące OWIN w pełni przetwarza żądanie. |
| [Buforowanie odpowiedzi](xref:performance/caching/middleware) | Zapewnia obsługę buforowania odpowiedzi. | Przed komponentami, które wymagają buforowania. |
| [Kompresja odpowiedzi](xref:performance/response-compression) | Zapewnia obsługę kompresji odpowiedzi. | Przed komponentami, które wymagają kompresji. |
| [Poproś o lokalizację](xref:fundamentals/localization) | Zapewnia obsługę lokalizacji. | Przed lokalizacją wrażliwych składników. |
| [Routing punktu końcowego](xref:fundamentals/routing) | Definiuje i ogranicza trasy żądań. | Terminal do pasujących tras. |
| [Sesja](xref:fundamentals/app-state) | Zapewnia obsługę zarządzania sesjami użytkowników. | Przed składnikami, które wymagają sesji. |
| [Pliki statyczne](xref:fundamentals/static-files) | Zapewnia obsługę obsługi plików statycznych i przeglądania katalogów. | Terminal, jeśli żądanie pasuje do pliku. |
| [Ponowne zapisywanie adresów URL](xref:fundamentals/url-rewriting) | Zapewnia obsługę przepisywania adresów URL i przekierowywania żądań. | Przed składnikami, które zużywają adres URL. |
| [Protokoły WebSocket](xref:fundamentals/websockets) | Włącza protokół WebSockets. | Przed składników, które są wymagane do akceptowania żądań WebSocket. |

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
