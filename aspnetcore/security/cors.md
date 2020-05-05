---
title: Włącz żądania między źródłami (CORS) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, w jaki sposób mechanizm CORS jest standardem umożliwiającym lub odrzucanie żądań między źródłami w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773814"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Włącz żądania między źródłami (CORS) w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)

W tym artykule pokazano, jak włączyć funkcję CORS w aplikacji ASP.NET Core.

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego źródła*. Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji. Czasami możesz chcieć zezwolić innym lokacjom na wykonywanie żądań między źródłami do aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł CORS firmy Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Współużytkowanie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):

* Jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.
* **Nie** jest funkcją zabezpieczeń, funkcja CORS osłabi zabezpieczenia. Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS. Aby uzyskać więcej informacji, zobacz [jak działa mechanizm CORS](#how-cors).
* Zezwala serwerowi jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.
* Jest bezpieczniejsze i bardziej elastyczne niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>To samo źródło

Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Te dwa adresy URL mają te same źródła:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Te adresy URL mają różne źródła niż poprzednie dwa adresy URL:

* `https://example.net`&ndash; Inna domena
* `https://www.example.com/foo.html`&ndash; Inna poddomena
* `http://example.com/foo.html`&ndash; Inny schemat
* `https://example.com:9000/foo.html`&ndash; Inny port

## <a name="enable-cors"></a>Włączanie mechanizmu CORS

Istnieją trzy sposoby włączania mechanizmu CORS:

* W oprogramowaniu pośredniczącym przy użyciu [nazwanych zasad](#np) lub [zasad domyślnych](#dp).
* Korzystanie z [routingu punktów końcowych](#ecors).
* Z atrybutem [[EnableCors]](#attr) .

Użycie atrybutu [[EnableCors]](#attr) z nazwanymi zasadami zapewnia kontrolkę Finest w ograniczaniu punktów końcowych, które obsługują mechanizm CORS.

Każde podejście jest szczegółowo opisane w poniższych sekcjach.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS z nazwanymi zasadami i programem pośredniczącym

Oprogramowanie pośredniczące CORS obsługuje żądania między źródłami. Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji z określonymi źródłami:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Powyższy kod ma następujące działanie:

* Ustawia nazwę zasad na `_myAllowSpecificOrigins`. Nazwa zasad jest dowolną.
* Wywołuje metodę <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozszerzającą i określa zasady `_myAllowSpecificOrigins` CORS. `UseCors`dodaje oprogramowanie pośredniczące CORS. Wywołanie `UseCors` musi być umieszczone po `UseRouting`, ale wcześniej. `UseAuthorization` Aby uzyskać więcej informacji, zobacz [kolejność oprogramowania pośredniczącego](xref:fundamentals/middleware/index#middleware-order).
* Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Wyrażenie lambda przyjmuje <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt. [Opcje konfiguracji](#cors-policy-options), takie jak `WithOrigins`, zostały opisane w dalszej części tego artykułu.
* Włącza zasady `_myAllowSpecificOrigins` CORS dla wszystkich punktów końcowych kontrolera. Zobacz [Routing punktów końcowych](#ecors) , aby zastosować zasady CORS do określonych punktów końcowych.

W przypadku routingu punktu końcowego oprogramowanie do obsługi mechanizmu CORS ***musi*** być skonfigurowane do wykonywania między `UseRouting` wywołaniami `UseEndpoints`i.

Zobacz temat [CORS testów](#testc) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody dodaje usługi CORS do kontenera usługi aplikacji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Aby uzyskać więcej informacji, zobacz [Opcje zasad CORS](#cpo) w tym dokumencie.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metody mogą być łańcucha, jak pokazano w poniższym kodzie:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Uwaga: określony adres URL **nie** może zawierać końcowego ukośnika (`/`). Jeśli adres URL kończy się `/`na, porównywanie zwraca `false` i nie jest zwracany nagłówek.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>Mechanizm CORS z domyślnymi zasadami i oprogramowania pośredniczącego

Następujący wyróżniony kod włącza domyślne zasady CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Poprzedni kod stosuje domyślne zasady CORS do wszystkich punktów końcowych kontrolera.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Włączanie mechanizmu CORS przy użyciu routingu punktu końcowego

Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy `RequireCors` użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf). Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/20709) w [usłudze GitHub i testowanie mechanizmu CORS przy użyciu routingu punktów końcowych i [HttpOptions]](#tcer).

Dzięki kierowaniu punktów końcowych można włączyć funkcję CORS dla poszczególnych punktów końcowych przy <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> użyciu zestawu metod rozszerzających:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Powyższy kod ma następujące działanie:

* `app.UseCors`włącza oprogramowanie pośredniczące CORS. Ponieważ zasady domyślne nie zostały skonfigurowane, `app.UseCors()` sama nie włącza mechanizmu CORS.
* Punkty `/echo` końcowe i umożliwiają Zezwalanie na żądania między źródłami przy użyciu określonych zasad.
* Punkty `/echo2` końcowe Razor stron i ***nie*** zezwalają na żądania między źródłami, ponieważ nie określono żadnych zasad domyślnych.

Atrybut [[DisableCors]](#dc) ***nie wyłącza mechanizmu*** CORS, który został włączony przez Routing punktów końcowych `RequireCors`za pomocą.

Aby uzyskać instrukcje dotyczące testowania kodu podobnego do powyższego, zobacz artykuł [CORS testów z routingiem punktów końcowych i [HttpOptions]](#tcer) .

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Włączanie mechanizmu CORS z atrybutami

Włączanie mechanizmu CORS z atrybutem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) i stosowanie nazwanych zasad tylko do tych punktów końcowych, które wymagają mechanizmu CORS, udostępnia formant Finest.

Atrybut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) zapewnia alternatywę do stosowania mechanizmu CORS globalnie. Ten `[EnableCors]` atrybut włącza funkcję CORS dla wybranych punktów końcowych, a nie wszystkie punkty końcowe:

* `[EnableCors]`Określa zasady domyślne.
* `[EnableCors("{Policy String}")]`Określa nazwane zasady.

Ten `[EnableCors]` atrybut może być stosowany do:

* RazorStronic`PageModel`
* Kontroler
* Metoda akcji kontrolera

Różne zasady można stosować do kontrolerów, modeli stron lub metod akcji z `[EnableCors]` atrybutem. Gdy `[EnableCors]` atrybut jest stosowany do kontrolera, modelu strony lub metody akcji, a funkcja CORS jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane. ***Zalecamy łączenie zasad. Użyj*** atrybutu ***lub oprogramowania pośredniczącego, a nie obu w tej samej aplikacji.*** `[EnableCors]`

Poniższy kod stosuje różne zasady do każdej metody:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Poniższy kod tworzy dwie zasady CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

W przypadku kontroli Finest ograniczającej żądania CORS:

* Używany `[EnableCors("MyPolicy")]` z nazwanymi zasadami.
* Nie Definiuj zasad domyślnych.
* Nie używaj [routingu punktów końcowych](#ecors).

Kod w następnej sekcji jest zgodny z poprzednią listą.

Zobacz temat [CORS testów](#testc) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.

<a name="dc"></a>

### <a name="disable-cors"></a>Wyłącz funkcję CORS

Atrybut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***nie wyłącza mechanizmu*** CORS, który został włączony przez [Routing punktu końcowego](#ecors).

Poniższy kod definiuje zasady `"MyPolicy"`CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Poniższy kod wyłącza funkcję CORS dla `GetValues2` akcji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Powyższy kod ma następujące działanie:

* Nie włącza mechanizmu CORS z [routingiem punktów końcowych](#ecors).
* Nie definiuje [domyślnych zasad CORS](#dp).
* Używa elementu [[EnableCors ("Moja Policy")]](#attr) w celu `"MyPolicy"` włączenia zasad CORS dla kontrolera.
* Wyłącza funkcję CORS dla `GetValues2` metody.

Aby uzyskać instrukcje dotyczące testowania poprzedniego kodu, zobacz temat [CORS testów](#testc) .

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opcje zasad CORS

W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:

* [Ustaw dozwolone źródła](#set-the-allowed-origins)
* [Ustaw dozwolone metody HTTP](#set-the-allowed-http-methods)
* [Ustaw nagłówki dozwolonych żądań](#set-the-allowed-request-headers)
* [Ustawianie nagłówków uwidocznionych odpowiedzi](#set-the-exposed-response-headers)
* [Poświadczenia w żądaniach między źródłami](#credentials-in-cross-origin-requests)
* [Ustaw czas wygaśnięcia inspekcji wstępnej](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`. W przypadku niektórych opcji warto przeczytać najpierw sekcję [jak działa mechanizm CORS](#how-cors) .

## <a name="set-the-allowed-origins"></a>Ustaw dozwolone źródła

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Zezwala na żądania CORS ze wszystkich źródeł z dowolnym schematem`http` ( `https`lub). `AllowAnyOrigin`jest niezabezpieczony, ponieważ *Każda witryna sieci Web* może wprowadzać żądania między źródłami do aplikacji.

> [!NOTE]
> Określenie `AllowAnyOrigin` i `AllowCredentials` jest niebezpieczną konfiguracją i może skutkować fałszerstwem żądania między lokacjami. Usługa CORS zwraca nieprawidłową odpowiedź CORS, gdy aplikacja jest skonfigurowana przy użyciu obu metod.

`AllowAnyOrigin`ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Origin` nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Właściwość zasad jako funkcję, która pozwala na pochodzenie zgodne ze skonfigurowaną domeną wieloznaczną podczas oceniania, czy pochodzenie jest dozwolone.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Ustaw dozwolone metody HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Zezwala na dowolną metodę HTTP:
* Ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Methods` nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Ustaw nagłówki dozwolonych żądań

Aby zezwolić na wysyłanie określonych nagłówków w żądaniu CORS, nazywanymi [nagłówkami żądań autora](https://xhr.spec.whatwg.org/#request), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> i określ dozwolone nagłówki:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Aby zezwolić na wszystkie [nagłówki żądań autora](https://www.w3.org/TR/cors/#author-request-headers), <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>Wywołaj:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`ma wpływ na żądania inspekcji wstępnej oraz nagłówek [Access-Control-Request-Heads](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) . Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .

Zasady oprogramowania CORS są zgodne z określonymi nagłówkami określonymi przez `WithHeaders` jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie pasują do nagłówków określonych `WithHeaders`w.

Na przykład rozważ zastosowanie skonfigurowanej aplikacji w następujący sposób:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

Oprogramowanie pośredniczące CORS odrzuca żądanie wstępne z następującym nagłówkiem żądania, ponieważ `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nie ma na `WithHeaders`liście:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Aplikacja zwraca odpowiedź *200 OK* , ale nie wysyła nagłówków CORS z powrotem. W związku z tym przeglądarka nie próbuje żądania między źródłami.

### <a name="set-the-exposed-response-headers"></a>Ustawianie nagłówków uwidocznionych odpowiedzi

Domyślnie przeglądarka nie ujawnia wszystkich nagłówków odpowiedzi dla aplikacji. Aby uzyskać więcej informacji, zobacz temat [udostępnianie zasobów między źródłami W3C (terminologia): prosty nagłówek odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).

Domyślnie dostępne są nagłówki odpowiedzi:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specyfikacja CORS wywołuje te nagłówki *proste odpowiedzi*. Aby udostępnić inne nagłówki aplikacji, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Poświadczenia w żądaniach między źródłami

Poświadczenia wymagają specjalnej obsługi w żądaniu CORS. Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem między źródłami. Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP. Aby wysłać poświadczenia z żądaniem między źródłami, klient musi mieć ustawioną `XMLHttpRequest.withCredentials` wartość `true`.

Bezpośrednie `XMLHttpRequest` używanie:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Za pomocą jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Za pomocą [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Serwer musi zezwalać na poświadczenia. Aby zezwolić na poświadczenia między źródłami, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>Wywołaj:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.

Jeśli przeglądarka wyśle poświadczenia, ale odpowiedź nie zawiera prawidłowego `Access-Control-Allow-Credentials` nagłówka, przeglądarka nie ujawnia odpowiedzi aplikacji, a żądanie między źródłami nie powiedzie się.

Zezwalanie na poświadczenia między źródłami stanowi zagrożenie bezpieczeństwa. Witryna sieci Web w innej domenie może wysyłać poświadczenia zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specyfikacja mechanizmu CORS określa również, że ustawienia pochodzenia `"*"` (wszystkie źródła) są nieprawidłowe, jeśli `Access-Control-Allow-Credentials` nagłówek jest obecny.

<a name="pref"></a>

## <a name="preflight-requests"></a>Żądania wstępnego lotu

W przypadku niektórych żądań CORS przeglądarka wysyła żądanie dodatkowych [opcji](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) przed wykonaniem rzeczywistego żądania. To żądanie jest nazywane [żądaniem wstępnym](https://developer.mozilla.org/docs/Glossary/Preflight_request). Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są ***wszystkie*** następujące warunki:

* Metoda żądania ma wartość GET, główna lub OPUBLIKOWANa.
* Aplikacja nie ustawia nagłówków żądań `Accept`innych niż, `Accept-Language`, `Content-Language`, `Content-Type`, lub. `Last-Event-ID`
* `Content-Type` Nagłówek, jeśli jest ustawiony, ma jedną z następujących wartości:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Reguła dotycząca nagłówków żądań ustawiona dla żądania klienta dotyczy nagłówków, które są ustawiane przez aplikację przez wywołanie `setRequestHeader` na `XMLHttpRequest` obiekcie. Specyfikacja CORS wywołuje [nagłówki żądania autora](https://www.w3.org/TR/cors/#author-request-headers)tych nagłówków. Reguła nie ma zastosowania do nagłówków, które można ustawić w przeglądarce, `User-Agent`na `Host`przykład, `Content-Length`lub.

Poniżej znajduje się Przykładowa odpowiedź podobna do żądania inspekcji wstępnej wykonanego z przycisku **[Put Test]** w sekcji [test CORS](#testc) tego dokumentu.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

W ramach żądania wstępnego jest stosowana Metoda [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Może zawierać następujące nagłówki:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda http, która będzie używana dla rzeczywistego żądania.
* [Access-Control-Request-Heads](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): lista nagłówków żądań, które aplikacja ustawia na rzeczywiste żądanie. Jak wspomniano wcześniej, nie obejmuje to nagłówków, które są ustawiane przez `User-Agent`przeglądarkę, takich jak.
* [Access-Control-Allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Jeśli żądanie wstępne nie zostanie odrzucone, aplikacja zwróci `200 OK` odpowiedź, ale nie ustawi nagłówków CORS. W związku z tym przeglądarka nie próbuje żądania między źródłami. Przykład odmowy żądania inspekcji wstępnej znajduje się w sekcji [test CORS](#testc) tego dokumentu.

Korzystając z narzędzi F12, aplikacja konsoli pokazuje błąd podobny do jednego z następujących, w zależności od przeglądarki:

* Firefox: Zablokowano żądanie między źródłami: te same zasady pochodzenia nie zezwalają na odczytywanie zasobu `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`zdalnego pod adresem. (Przyczyna: żądanie CORS nie powiodło się). [Dowiedz się więcej](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Oparte na chromie: dostęp do pobieraniahttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5w "" zhttps://cors3.azurewebsites.netpochodzenia "" został zablokowany przez zasady CORS: odpowiedź na żądanie wstępnej inspekcji nie przeszedł sprawdzenia kontroli dostępu: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie. Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.

Aby zezwolić na określone nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>Wywołaj:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Aby zezwolić na wszystkie [nagłówki żądań autora](https://www.w3.org/TR/cors/#author-request-headers), <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>Wywołaj:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Przeglądarki nie są spójne w sposób ich `Access-Control-Request-Headers`ustawiania. Jeśli:

* Nagłówki są ustawione na coś innego niż`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jest wywoływana: Uwzględnij co `Accept`najmniej `Content-Type`,, `Origin`i oraz wszystkie niestandardowe nagłówki, które mają być obsługiwane.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Kod automatycznego żądania wstępnego

Po zastosowaniu zasad CORS:

* Globalnie przez wywołanie `app.UseCors` w `Startup.Configure`.
* Przy użyciu `[EnableCors]` atrybutu.

ASP.NET Core odpowiada na żądanie opcji inspekcji wstępnej.

Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy `RequireCors` użyciu obecnie ***nie*** obsługuje automatycznych żądań inspekcji wstępnej.

Sekcja [CORS testów](#testc) tego dokumentu przedstawia takie zachowanie.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] atrybut dla żądań inspekcji wstępnej

Gdy mechanizm CORS jest włączony przy użyciu odpowiednich zasad, ASP.NET Core zazwyczaj automatycznie reaguje na żądania funkcji CORS przed inspekcją. W niektórych scenariuszach taka sytuacja może nie być taka sama. Na przykład przy użyciu mechanizmu [CORS z routingiem punktu końcowego](#ecors).

Poniższy kod używa atrybutu [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) do tworzenia punktów końcowych dla żądań opcji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Aby uzyskać instrukcje dotyczące testowania poprzedniego kodu, zobacz temat [CORS testów z routingiem punktów końcowych i [HttpOptions]](#tcer) .

### <a name="set-the-preflight-expiration-time"></a>Ustaw czas wygaśnięcia inspekcji wstępnej

Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana. Aby ustawić ten nagłówek, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak działa mechanizm CORS

W tej sekcji opisano, co się dzieje w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie komunikatów http.

* Mechanizm CORS **nie** jest funkcją zabezpieczeń. CORS jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.
  * Na przykład złośliwy aktor może użyć [skryptów między lokacjami (XSS)](xref:security/cross-site-scripting) względem witryny i wykonać żądanie między lokacjami w celu wykraść informacji.
* Interfejs API nie jest bezpieczniejszy dzięki umożliwieniu mechanizmu CORS.
  * Aby wymusić mechanizm CORS, należy do klienta (przeglądarki). Serwer wykonuje żądanie i zwraca odpowiedź, jest to klient, który zwraca błąd i blokuje odpowiedź. Na przykład w dowolnym z poniższych narzędzi zostanie wyświetlona odpowiedź serwera:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Przeglądarka sieci Web, wprowadzając adres URL na pasku adresu.
* Jest to sposób, aby serwer zezwalał przeglądarce na wykonywanie żądania [interfejsu API](https://developer.mozilla.org/docs/Web/API/Fetch_API) [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub pobierania między źródłami, które w przeciwnym razie byłoby zabronione.
  * Przeglądarki bez funkcji CORS nie mogą wykonywać żądań między źródłami. Przed zastosowaniem mechanizmu CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) został użyty do obejścia tego ograniczenia. JSONP nie używa XHR, używa `<script>` znacznika do odbierania odpowiedzi. Skrypty mogą być ładowane między różnymi źródłami.

[Specyfikacja CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania między źródłami. Jeśli przeglądarka obsługuje mechanizm CORS, ustawia te nagłówki automatycznie dla żądań cross-Origin. Niestandardowy kod JavaScript nie jest wymagany do włączenia mechanizmu CORS.

[Przycisk Put test](https://cors3.azurewebsites.net/test) na wdrożonym [przykładzie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Poniżej znajduje się przykład żądania między źródłami z przycisku Testuj [wartości](https://cors3.azurewebsites.net/) do `https://cors1.azurewebsites.net/api/values`. `Origin` Nagłówek:

* Zapewnia domenę lokacji, która żąda żądania.
* Jest wymagany i musi się różnić od hosta.

**Nagłówki ogólne**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Nagłówki odpowiedzi**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Nagłówki żądań**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

W `OPTIONS` obszarze żądania serwer ustawia **Response headers** `Access-Control-Allow-Origin: {allowed origin}` nagłówek odpowiedzi w odpowiedzi. Przykładowo wdrożone [przykładowe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) `OPTIONS` żądanie [usunięcia [EnableCors]](https://cors1.azurewebsites.net/test?number=2) zawiera następujące nagłówki:

**Nagłówki ogólne**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Nagłówki odpowiedzi**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Nagłówki żądań**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

W poprzednich **nagłówkach odpowiedzi**serwer ustawia nagłówek [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) w odpowiedzi. `https://cors1.azurewebsites.net` Wartość tego nagłówka pasuje do `Origin` nagłówka z żądania.

Jeśli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> jest wywoływana, `Access-Control-Allow-Origin: *`jest zwracana wartość symbol wieloznaczny. `AllowAnyOrigin`zezwala na dowolne źródło.

Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami nie powiedzie się. W programie przeglądarka nie zezwala na żądanie. Nawet jeśli serwer zwróci pomyślną odpowiedź, przeglądarka nie udostępni odpowiedzi dla aplikacji klienckiej.

<a name="options"></a>

### <a name="display-options-requests"></a>Wyświetl żądania opcji

Domyślnie przeglądarki Chrome i Edge nie wyświetlają żądań OPTIONS na karcie Sieć narzędzi F12. Aby wyświetlić żądania opcji w następujących przeglądarkach:

* `chrome://flags/#out-of-blink-cors` lub `edge://flags/#out-of-blink-cors`
* Wyłącz flagę.
* uruchomieniu.

W przeglądarce Firefox domyślnie są wyświetlane żądania dotyczące opcji.

## <a name="cors-in-iis"></a>Mechanizm CORS w usługach IIS

W przypadku wdrażania w programie IIS należy uruchomić funkcję CORS przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy. Aby zapewnić obsługę tego scenariusza, należy zainstalować i skonfigurować [moduł CORS usług IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) dla aplikacji.

<a name="testc"></a>

## <a name="test-cors"></a>Testowanie CORS

[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ma kod do testowania mechanizmu CORS. Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample). Przykładem jest projekt interfejsu API ze Razor stronami dodanymi:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`powinien być używany tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Poniżej `ValuesController` podano punkty końcowe do testowania:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) jest dostarczany przez pakiet NuGet [Rick. docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) i wyświetla informacje o trasie.

Przetestuj poprzedni przykładowy kod przy użyciu jednej z następujących metod:

* Użyj wdrożonej przykładowej aplikacji [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)pod adresem. Nie ma potrzeby pobierania przykładu.
* Uruchom przykład przy `dotnet run` użyciu domyślnego adresu URL `https://localhost:5001`.
* Uruchom przykład z programu Visual Studio z portem ustawionym na 44398 dla adresu URL `https://localhost:44398`.

Używanie przeglądarki z narzędziami F12:

* Wybierz przycisk **wartości** i Przejrzyj nagłówki na karcie **Sieć** .
* Wybierz przycisk **Put test** . Zobacz temat [Opcje wyświetlania żądania](#options) , aby uzyskać instrukcje dotyczące wyświetlania żądania opcji. **Test Put** tworzy dwa żądania, żądanie wstępnej kontroli i żądanie Put.
* Wybierz przycisk **`GetValues2 [DisableCors]`** , aby wyzwolić nieudane żądanie CORS. Jak wspomniano w dokumencie, odpowiedź zwraca 200 sukcesu, ale żądanie CORS nie zostało wykonane. Wybierz kartę **konsola** , aby wyświetlić błąd CORS. W zależności od przeglądarki wyświetlane są błędy podobne do następujących:

     Dostęp do pobierania z `'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` lokalizacji źródłowej został zablokowany przez zasady CORS: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie. Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.
     
Punkty końcowe z obsługą mechanizmu CORS można testować za pomocą narzędzia, takiego jak [zwinięcie](https://curl.haxx.se/), [programu Fiddler](https://www.telerik.com/fiddler)lub [Poster](https://www.getpostman.com/). W przypadku korzystania z narzędzia, Źródło żądania określone przez `Origin` nagłówek musi różnić się od hosta przyjmującego żądanie. Jeśli żądanie nie jest *źródłem krzyżowe* na podstawie wartości `Origin` nagłówka:

* Nie ma potrzeby przetwarzania żądania przez oprogramowanie pośredniczące CORS.
* Nagłówki CORS nie są zwracane w odpowiedzi.

Następujące polecenie używa `curl` polecenia, aby wydać żądanie Options z informacjami:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testowanie mechanizmu CORS przy użyciu routingu punktów końcowych i [HttpOptions]

Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy `RequireCors` użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf). Rozważmy następujący kod, który używa [routingu punktu końcowego w celu włączenia mechanizmu CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Poniżej `TodoItems1Controller` przedstawiono punkty końcowe do testowania:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=1) wdrożonej [próbki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).

Przyciski **delete [EnableCors]** i **Get [EnableCors]** zostały wykonane pomyślnie, ponieważ punkty końcowe `[EnableCors]` mają i odpowiadają na żądania inspekcji wstępnej. Inne punkty końcowe zakończą się niepowodzeniem. Przycisk **Get** kończy się niepowodzeniem, ponieważ [kod JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) wysyła:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Poniżej `TodoItems2Controller` znajdują się podobne punkty końcowe, ale zawiera jawny kod odpowiadający na żądania opcji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=2) wdrożonej próbki. Z listy rozwijanej **kontroler** wybierz kolejno pozycje **Inspekcja wstępna** , a następnie **Ustaw kontroler**. Wszystkie wywołania mechanizmu CORS do `TodoItems2Controller` punktów końcowych powiodły się.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Wprowadzenie do modułu CORS usług IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym artykule pokazano, jak włączyć funkcję CORS w aplikacji ASP.NET Core.

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego źródła*. Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji. Czasami możesz chcieć zezwolić innym lokacjom na wykonywanie żądań między źródłami do aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł CORS firmy Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Współużytkowanie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):

* Jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.
* **Nie** jest funkcją zabezpieczeń, funkcja CORS osłabi zabezpieczenia. Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS. Aby uzyskać więcej informacji, zobacz [jak działa mechanizm CORS](#how-cors).
* Zezwala serwerowi jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.
* Jest bezpieczniejsze i bardziej elastyczne niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>To samo źródło

Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Te dwa adresy URL mają te same źródła:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Te adresy URL mają różne źródła niż poprzednie dwa adresy URL:

* `https://example.net`&ndash; Inna domena
* `https://www.example.com/foo.html`&ndash; Inna poddomena
* `http://example.com/foo.html`&ndash; Inny schemat
* `https://example.com:9000/foo.html`&ndash; Inny port

Program Internet Explorer nie traktuje portu podczas porównywania źródeł.

## <a name="cors-with-named-policy-and-middleware"></a>CORS z nazwanymi zasadami i programem pośredniczącym

Oprogramowanie pośredniczące CORS obsługuje żądania między źródłami. Poniższy kod włącza mechanizm CORS dla całej aplikacji z określonym źródłem:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Powyższy kod ma następujące działanie:

* Ustawia nazwę zasad na "\_myAllowSpecificOrigins". Nazwa zasad jest dowolną.
* Wywołuje metodę <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozszerzenia, która umożliwia mechanizm CORS.
* Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Wyrażenie lambda przyjmuje <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt. [Opcje konfiguracji](#cors-policy-options), takie jak `WithOrigins`, zostały opisane w dalszej części tego artykułu.

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody dodaje usługi CORS do kontenera usługi aplikacji:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Aby uzyskać więcej informacji, zobacz [Opcje zasad CORS](#cpo) w tym dokumencie.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda może łączyć metody łańcucha, jak pokazano w poniższym kodzie:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Uwaga: adres URL **nie** może zawierać końcowego ukośnika (`/`). Jeśli adres URL kończy się `/`na, porównywanie zwraca `false` i nie jest zwracany nagłówek.

Poniższy kod dotyczy zasad CORS dla wszystkich punktów końcowych aplikacji za pośrednictwem oprogramowania do obsługi mechanizmu CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Uwaga: `UseCors` należy wywołać przed `UseMvc`.

Zobacz [Włączanie mechanizmu CORS Razor na stronach, kontrolerach i metodach akcji](#ecors) , aby zastosować zasady CORS na poziomie strony/kontrolera/akcji.

Zobacz temat [CORS testów](#test) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.

## <a name="enable-cors-with-attributes"></a>Włączanie mechanizmu CORS z atrybutami

Atrybut [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) stanowi alternatywę dla zastosowania mechanizmu CORS globalnie. Ten `[EnableCors]` atrybut włącza funkcję CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych.

Użyj `[EnableCors]` , aby określić zasady domyślne i `[EnableCors("{Policy String}")]` określić zasady.

Ten `[EnableCors]` atrybut może być stosowany do:

* RazorStronic`PageModel`
* Kontroler
* Metoda akcji kontrolera

Możesz zastosować różne zasady do kontrolera/strony-model/akcja z `[EnableCors]` atrybutem. Gdy `[EnableCors]` atrybut jest stosowany do metody Controller/model/akcja, a funkcja CORS jest włączona w oprogramowaniu pośredniczącym, stosowane są ***obie*** zasady. ***Nie*** zaleca się łączenia zasad. Użyj `[EnableCors]` atrybutu lub oprogramowania pośredniczącego, ***nie obu jednocześnie**. W przypadku `[EnableCors]`korzystania z **programu nie należy definiować zasad** domyślnych.

Poniższy kod stosuje różne zasady do każdej metody:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Poniższy kod tworzy domyślne zasady CORS i zasady o nazwie `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Wyłącz funkcję CORS

Atrybut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) wyłącza funkcję CORS dla kontrolera/strony-modelu/akcji.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opcje zasad CORS

W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:

* [Ustaw dozwolone źródła](#set-the-allowed-origins)
* [Ustaw dozwolone metody HTTP](#set-the-allowed-http-methods)
* [Ustaw nagłówki dozwolonych żądań](#set-the-allowed-request-headers)
* [Ustawianie nagłówków uwidocznionych odpowiedzi](#set-the-exposed-response-headers)
* [Poświadczenia w żądaniach między źródłami](#credentials-in-cross-origin-requests)
* [Ustaw czas wygaśnięcia inspekcji wstępnej](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`. W przypadku niektórych opcji warto przeczytać najpierw sekcję [jak działa mechanizm CORS](#how-cors) .

## <a name="set-the-allowed-origins"></a>Ustaw dozwolone źródła

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Zezwala na żądania CORS ze wszystkich źródeł z dowolnym schematem`http` ( `https`lub). `AllowAnyOrigin`jest niezabezpieczony, ponieważ *Każda witryna sieci Web* może wprowadzać żądania między źródłami do aplikacji.

> [!NOTE]
> Określenie `AllowAnyOrigin` i `AllowCredentials` jest niebezpieczną konfiguracją i może skutkować fałszerstwem żądania między lokacjami. W przypadku bezpiecznej aplikacji należy określić dokładną listę źródeł, jeśli klient musi autoryzować sam do uzyskiwania dostępu do zasobów serwera.

`AllowAnyOrigin`ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Origin` nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Właściwość zasad jako funkcję, która pozwala na pochodzenie zgodne ze skonfigurowaną domeną wieloznaczną podczas oceniania, czy pochodzenie jest dozwolone.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Ustaw dozwolone metody HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Zezwala na dowolną metodę HTTP:
* Ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Methods` nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Ustaw nagłówki dozwolonych żądań

Aby zezwolić na wysyłanie określonych nagłówków w żądaniu CORS, nazywanymi *nagłówkami żądań autora*, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> i określ dozwolone nagłówki:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Aby zezwolić na wszystkie nagłówki żądań autora, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>Wywołaj:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

To ustawienie ma wpływ na żądania inspekcji wstępnej i `Access-Control-Request-Headers` nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .

Oprogramowanie pośredniczące CORS zawsze umożliwia wysyłanie czterech nagłówków `Access-Control-Request-Headers` w celu, niezależnie od wartości skonfigurowanych w CorsPolicy. Heads. Ta lista nagłówków obejmuje:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Na przykład rozważ zastosowanie skonfigurowanej aplikacji w następujący sposób:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Oprogramowanie pośredniczące CORS pomyślnie reaguje na żądanie inspekcji wstępnej z następującym nagłówkiem żądania `Content-Language` , ponieważ zawsze jest listy dozwolonych:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Ustawianie nagłówków uwidocznionych odpowiedzi

Domyślnie przeglądarka nie ujawnia wszystkich nagłówków odpowiedzi dla aplikacji. Aby uzyskać więcej informacji, zobacz temat [udostępnianie zasobów między źródłami W3C (terminologia): prosty nagłówek odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).

Domyślnie dostępne są nagłówki odpowiedzi:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specyfikacja CORS wywołuje te nagłówki *proste odpowiedzi*. Aby udostępnić inne nagłówki aplikacji, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Poświadczenia w żądaniach między źródłami

Poświadczenia wymagają specjalnej obsługi w żądaniu CORS. Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem między źródłami. Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP. Aby wysłać poświadczenia z żądaniem między źródłami, klient musi mieć ustawioną `XMLHttpRequest.withCredentials` wartość `true`.

Bezpośrednie `XMLHttpRequest` używanie:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Za pomocą jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Za pomocą [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Serwer musi zezwalać na poświadczenia. Aby zezwolić na poświadczenia między źródłami, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>Wywołaj:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.

Jeśli przeglądarka wyśle poświadczenia, ale odpowiedź nie zawiera prawidłowego `Access-Control-Allow-Credentials` nagłówka, przeglądarka nie ujawnia odpowiedzi aplikacji, a żądanie między źródłami nie powiedzie się.

Zezwalanie na poświadczenia między źródłami stanowi zagrożenie bezpieczeństwa. Witryna sieci Web w innej domenie może wysyłać poświadczenia zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specyfikacja mechanizmu CORS określa również, że ustawienia pochodzenia `"*"` (wszystkie źródła) są nieprawidłowe, jeśli `Access-Control-Allow-Credentials` nagłówek jest obecny.

### <a name="preflight-requests"></a>Żądania wstępnego lotu

W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie przed wykonaniem rzeczywistego żądania. To żądanie jest nazywane *żądaniem wstępnym*. Jeśli spełnione są następujące warunki, przeglądarka może pominąć żądanie wstępne:

* Metoda żądania ma wartość GET, główna lub OPUBLIKOWANa.
* Aplikacja nie ustawia nagłówków żądań `Accept`innych niż, `Accept-Language`, `Content-Language`, `Content-Type`, lub. `Last-Event-ID`
* `Content-Type` Nagłówek, jeśli jest ustawiony, ma jedną z następujących wartości:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Reguła dotycząca nagłówków żądań ustawiona dla żądania klienta dotyczy nagłówków, które są ustawiane przez aplikację przez wywołanie `setRequestHeader` na `XMLHttpRequest` obiekcie. Specyfikacja CORS wywołuje *nagłówki żądania autora*tych nagłówków. Reguła nie ma zastosowania do nagłówków, które można ustawić w przeglądarce, `User-Agent`na `Host`przykład, `Content-Length`lub.

Poniżej znajduje się przykład żądania wstępnego:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Żądanie przed inspekcją używa metody HTTP OPTIONS. Zawiera dwa specjalne nagłówki:

* `Access-Control-Request-Method`: Metoda HTTP, która będzie używana dla rzeczywistego żądania.
* `Access-Control-Request-Headers`: Lista nagłówków żądań, które aplikacja ustawia na rzeczywiste żądanie. Jak wspomniano wcześniej, nie obejmuje to nagłówków, które są ustawiane przez `User-Agent`przeglądarkę, takich jak.

<!-- I think this needs to be removed, was put here accidently -->

Gdy mechanizm CORS jest włączony przy użyciu odpowiednich zasad, ASP.NET Core zwykle automatycznie reaguje na żądania inspekcji wstępnej. Zobacz [[HttpOptions] atrybutu dla żądań inspekcji wstępnej](#pro).

Żądanie inspekcji wstępnej CORS może zawierać `Access-Control-Request-Headers` nagłówek, który wskazuje serwerowi nagłówki wysyłane z rzeczywistym żądaniem.

Aby zezwolić na określone nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>Wywołaj:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Aby zezwolić na wszystkie nagłówki żądań autora, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>Wywołaj:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Przeglądarki nie są w pełni spójne w sposób `Access-Control-Request-Headers`ich ustawiania. Jeśli ustawisz nagłówki jako elementy inne niż `"*"` (lub użycie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), należy uwzględnić co najmniej `Accept`, `Content-Type`i i `Origin`wszystkie niestandardowe nagłówki, które mają być obsługiwane.

Poniżej znajduje się Przykładowa odpowiedź na żądanie inspekcji wstępnej (przy założeniu, że serwer zezwala na żądanie):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Odpowiedź zawiera `Access-Control-Allow-Methods` nagłówek, który zawiera listę dozwolonych metod i opcjonalnie `Access-Control-Allow-Headers` nagłówek, który zawiera listę dozwolonych nagłówków. W przypadku pomyślnego przeprowadzenia żądania wstępnego przeglądarka wyśle rzeczywiste żądanie.

Jeśli żądanie wstępne nie zostanie odrzucone, aplikacja zwróci odpowiedź *200 OK* , ale nie wyśle nagłówków CORS z powrotem. W związku z tym przeglądarka nie próbuje żądania między źródłami.

### <a name="set-the-preflight-expiration-time"></a>Ustaw czas wygaśnięcia inspekcji wstępnej

Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana. Aby ustawić ten nagłówek, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak działa mechanizm CORS

W tej sekcji opisano, co się dzieje w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie komunikatów http.

* Mechanizm CORS **nie** jest funkcją zabezpieczeń. CORS jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.
  * Na przykład złośliwy aktor może użyć [zapobiegania skryptom między lokacjami (XSS)](xref:security/cross-site-scripting) względem witryny i wykonać żądanie między lokacjami w celu wykraść informacji.
* Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.
  * Aby wymusić mechanizm CORS, należy do klienta (przeglądarki). Serwer wykonuje żądanie i zwraca odpowiedź, jest to klient, który zwraca błąd i blokuje odpowiedź. Na przykład w dowolnym z poniższych narzędzi zostanie wyświetlona odpowiedź serwera:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Przeglądarka sieci Web, wprowadzając adres URL na pasku adresu.
* Jest to sposób, aby serwer zezwalał przeglądarce na wykonywanie żądania [interfejsu API](https://developer.mozilla.org/docs/Web/API/Fetch_API) [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub pobierania między źródłami, które w przeciwnym razie byłoby zabronione.
  * Przeglądarki (bez CORS) nie mogą wykonywać żądań między źródłami. Przed zastosowaniem mechanizmu CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) został użyty do obejścia tego ograniczenia. JSONP nie używa XHR, używa `<script>` znacznika do odbierania odpowiedzi. Skrypty mogą być ładowane między różnymi źródłami.

[Specyfikacja CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania między źródłami. Jeśli przeglądarka obsługuje mechanizm CORS, ustawia te nagłówki automatycznie dla żądań cross-Origin. Niestandardowy kod JavaScript nie jest wymagany do włączenia mechanizmu CORS.

Poniżej przedstawiono przykład żądania między źródłami danych. `Origin` Nagłówek zawiera domenę lokacji, która żąda żądania. `Origin` Nagłówek jest wymagany i musi się różnić od hosta.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Jeśli serwer zezwala na żądanie, ustawia `Access-Control-Allow-Origin` nagłówek w odpowiedzi. Wartość tego nagłówka jest zgodna z `Origin` nagłówkiem z żądania lub jest wartością `"*"`symbolu wieloznacznego, co oznacza, że wszystkie źródła są dozwolone:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami nie powiedzie się. W programie przeglądarka nie zezwala na żądanie. Nawet jeśli serwer zwróci pomyślną odpowiedź, przeglądarka nie udostępni odpowiedzi dla aplikacji klienckiej.

<a name="test"></a>

## <a name="test-cors"></a>Testowanie CORS

Aby przetestować CORS:

1. [Utwórz projekt interfejsu API](xref:tutorials/first-web-api). Możesz również [pobrać przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Włącz funkcję CORS przy użyciu jednego z metod w tym dokumencie. Przykład:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`powinien być używany tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Utwórz projekt aplikacji sieci Web (Razor strony lub MVC). Przykład używa Razor stron. Aplikację sieci Web można utworzyć w tym samym rozwiązaniu co projekt interfejsu API.
1. Dodaj następujący wyróżniony kod do pliku *index. cshtml* :

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. W poprzednim kodzie Zastąp `url: 'https://<web app>.azurewebsites.net/api/values/1',` ciąg adresem URL wdrożonej aplikacji.
1. Wdróż projekt interfejsu API. Na przykład [Wdróż na platformie Azure](xref:host-and-deploy/azure-apps/index).
1. Uruchom Razor strony lub aplikację MVC na pulpicie, a następnie kliknij przycisk **Testuj** . Użyj narzędzi F12, aby przejrzeć komunikaty o błędach.
1. Usuń pochodzenie hosta lokalnego `WithOrigins` z i Wdróż aplikację. Alternatywnie Uruchom aplikację kliencką z innym portem. Na przykład uruchom polecenie z programu Visual Studio.
1. Przetestuj za pomocą aplikacji klienckiej. Błędy funkcji CORS zwracają błąd, ale komunikat o błędzie nie jest dostępny dla języka JavaScript. Aby wyświetlić błąd, Użyj karty konsola w narzędziach F12. W zależności od przeglądarki pojawia się błąd (w konsoli narzędzia F12) podobny do poniższego:

   * Korzystanie z przeglądarki Microsoft Edge:

     **SEC7120: [CORS] nie znaleziono `https://localhost:44375` `https://localhost:44375` źródła w nagłówku odpowiedzi "Access-Control-Allow-Origin" dla zasobu Cross-Origin w`https://webapi.azurewebsites.net/api/values/1`**

   * Korzystanie z programu Chrome:

     **Dostęp do elementu XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` w lokalizacji `https://localhost:44375` z punktu początkowego został zablokowany przez zasady CORS: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.**
     
Punkty końcowe z obsługą mechanizmu CORS można testować za pomocą narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/). W przypadku korzystania z narzędzia, Źródło żądania określone przez `Origin` nagłówek musi różnić się od hosta przyjmującego żądanie. Jeśli żądanie nie jest *źródłem krzyżowe* na podstawie wartości `Origin` nagłówka:

* Nie ma potrzeby przetwarzania żądania przez oprogramowanie pośredniczące CORS.
* Nagłówki CORS nie są zwracane w odpowiedzi.

## <a name="cors-in-iis"></a>Mechanizm CORS w usługach IIS

W przypadku wdrażania w programie IIS należy uruchomić funkcję CORS przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy. Aby zapewnić obsługę tego scenariusza, należy zainstalować i skonfigurować [moduł CORS usług IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) dla aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Wprowadzenie do modułu CORS usług IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
