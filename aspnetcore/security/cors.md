---
title: Włączanie żądań między źródłami (CORS) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak cors jako standard zezwalania lub odrzucania żądań cross-origin w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642698"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Włączanie żądań między źródłami (CORS) w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Kirk [Larkin](https://twitter.com/serpent5)

W tym artykule pokazano, jak włączyć usługę CORS w aplikacji ASP.NET Core.

Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego pochodzenia*. Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny. Czasami możesz zezwolić innym witrynom na składanie żądań cross-origin do aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Udostępnianie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):

* Jest standardem W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.
* **Nie** jest to funkcja zabezpieczeń, CORS rozluźnia zabezpieczenia. Interfejs API nie jest bezpieczniejsze, umożliwiając CORS. Aby uzyskać więcej informacji, zobacz [Jak działa CORS](#how-cors).
* Umożliwia serwerowi jawne zezwalanie na niektóre żądania między źródłami, jednocześnie odrzucając inne.
* Jest bezpieczniejsza i bardziej elastyczna niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>To samo pochodzenie

Dwa adresy URL mają to samo pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454).](https://tools.ietf.org/html/rfc6454)

Te dwa adresy URL mają to samo źródło:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Te adresy URL mają inne źródła niż poprzednie dwa adresy URL:

* `https://example.net`&ndash; Inna domena
* `https://www.example.com/foo.html`&ndash; Inna poddomena
* `http://example.com/foo.html`&ndash; Inny schemat
* `https://example.com:9000/foo.html`&ndash; Inny port

## <a name="enable-cors"></a>Włączanie mechanizmu CORS

Istnieją trzy sposoby włączenia cors:

* W oprogramowaniu pośredniczącym przy użyciu [nazwanych zasad](#np) lub [zasad domyślnych](#dp).
* Korzystanie z [routingu punktów końcowych](#ecors).
* Z atrybutem [[EnableCors].](#attr)

Za pomocą [atrybutu [EnableCors]](#attr) z nazwanych zasad zapewnia najwyższą kontrolę w ograniczaniu punktów końcowych, które obsługują CORS.

Każde podejście jest szczegółowo opisane w poniższych sekcjach.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS z nazwanymi zasadami i oprogramowaniem pośredniczącym

Oprogramowanie pośredniczące CORS obsługuje żądania cross-origin. Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji o określonych źródłach:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Powyższy kod ma następujące działanie:

* Ustawia nazwę zasady `_myAllowSpecificOrigins`na . Nazwa zasad jest dowolna.
* Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia i `_myAllowSpecificOrigins` określa zasady CORS. `UseCors`dodaje oprogramowanie pośredniczące CORS. Wezwanie do `UseCors` musi być `UseRouting`umieszczone `UseAuthorization`po , ale przed . Aby uzyskać więcej informacji, zobacz [Kolejność oprogramowania pośredniczącego](xref:fundamentals/middleware/index#middleware-order).
* Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda bierze <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt. [Opcje konfiguracji](#cors-policy-options), `WithOrigins`takie jak , są opisane w dalszej części tego artykułu.
* Włącza `_myAllowSpecificOrigins` zasady CORS dla wszystkich punktów końcowych kontrolera. Zobacz [routing punktu końcowego,](#ecors) aby zastosować zasady CORS do określonych punktów końcowych.

W obszarze routingu punktu końcowego oprogramowanie pośredniczące CORS `UseRouting` `UseEndpoints` ***musi*** być skonfigurowane do wykonywania między wywołaniami do i .

Zobacz [Test CORS](#testc) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Aby uzyskać więcej informacji, zobacz [opcje zasad CORS](#cpo) w tym dokumencie.

Metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> mogą być połączone łańcuchem, jak pokazano w poniższym kodzie:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Uwaga: Określony adres URL **nie** może`/`zawierać końcowego ukośnika ( ). Jeśli adres URL `/`zostanie zakończony `false` na , porównanie zwraca się i nie zwracany jest żaden nagłówek.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS z domyślną zasadą i oprogramowaniem pośredniczącym

Poniższy wyróżniony kod umożliwia domyślną zasadę CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Powyższy kod stosuje domyślne zasady CORS do wszystkich punktów końcowych kontrolera.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Włącz cors z routingiem punktu końcowego

Włączenie mechanizmu CORS dla punktu `RequireCors` końcowego przy użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf). Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/aspnetcore/issues/20709) i [test cors z routingiem punktu końcowego i [HttpOptions]](#tcer).

Za pomocą routingu punktu końcowego cors można włączyć na <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> podstawie punktu końcowego przy użyciu zestawu metod rozszerzenia:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Powyższy kod ma następujące działanie:

* `app.UseCors`włącza oprogramowanie pośredniczące CORS. Ponieważ domyślna zasada nie została `app.UseCors()` skonfigurowana, sama nie włącza mechanizmu CORS.
* Punkty `/echo` końcowe i kontrolera zezwalają na żądania cross-origin przy użyciu określonych zasad.
* Punkty `/echo2` końcowe stron i Razor ***pages nie*** zezwalają na żądania między źródłami, ponieważ nie określono zasad domyślnych.

Atrybut [[DisableCors]](#dc) ***nie*** wyłącza mechanizmu CORS, który został `RequireCors`włączony przez routing punktu końcowego za pomocą pliku .

Zobacz [Test CORS z routingu punktu końcowego i [HttpOptions]](#tcer) instrukcje dotyczące testowania kodu podobnego do poprzedniego.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Włącz cors z atrybutami

Włączenie cors z [atrybutem [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) i stosowanie nazwanych zasad tylko do tych punktów końcowych, które wymagają CORS zapewnia najwyższą kontrolę.

Atrybut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) stanowi alternatywę dla globalnego stosowania usługi CORS. Atrybut `[EnableCors]` umożliwia CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych:

* `[EnableCors]`określa domyślną zasadę.
* `[EnableCors("{Policy String}")]`określa nazwaną zasadę.

Atrybut `[EnableCors]` można zastosować do:

* Strona Razor`PageModel`
* Kontrolera
* Metoda akcji kontrolera

Różne zasady mogą być stosowane do kontrolerów, modeli `[EnableCors]` stron lub metod akcji z atrybutem. Gdy `[EnableCors]` atrybut jest stosowany do kontrolera, modelu strony lub metody akcji, a cors jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane. ***Zalecamy, aby nie łączyć zasad. Użyj atrybutu*** `[EnableCors]` ***lub oprogramowania pośredniczącego, a nie w tej samej aplikacji.***

Poniższy kod stosuje inną zasadę do każdej metody:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Poniższy kod tworzy dwie zasady CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Aby uzyskać najlepszą kontrolę ograniczania żądań CORS:

* Użyj `[EnableCors("MyPolicy")]` z nazwanymi zasadami.
* Nie definiuj zasad domyślnych.
* Nie używaj [routingu punktów końcowych](#ecors).

Kod w następnej sekcji spełnia poprzednią listę.

Zobacz [Test CORS](#testc) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.

<a name="dc"></a>

### <a name="disable-cors"></a>Wyłącz cors

Atrybut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***nie*** wyłącza mechanizmu CORS, który został włączony przez [routing punktu końcowego](#ecors).

Poniższy kod definiuje zasady `"MyPolicy"`CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Następujący kod wyłącza cors `GetValues2` dla akcji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Powyższy kod ma następujące działanie:

* Nie włącza mechanizmu CORS z [routingiem punktów końcowych.](#ecors)
* Nie definiuje [domyślnej zasady CORS](#dp).
* Używa [funkcji [EnableCors("MyPolicy")],](#attr) `"MyPolicy"` aby włączyć zasady CORS dla kontrolera.
* Wyłącza CORS `GetValues2` dla metody.

Zobacz [Test CORS](#testc) instrukcje dotyczące testowania poprzedniego kodu.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Warianty polityki CORS

W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:

* [Ustawianie dozwolonych źródeł](#set-the-allowed-origins)
* [Ustawianie dozwolonych metod HTTP](#set-the-allowed-http-methods)
* [Ustawianie nagłówków dozwolonych żądań](#set-the-allowed-request-headers)
* [Ustawianie nagłówków narażonych odpowiedzi](#set-the-exposed-response-headers)
* [Poświadczenia w żądaniach między źródłami](#credentials-in-cross-origin-requests)
* [Ustawianie czasu wygaśnięcia inspekcji wstępnej](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`. W przypadku niektórych opcji warto najpierw przeczytać sekcję [Jak działa CORS.](#how-cors)

## <a name="set-the-allowed-origins"></a>Ustawianie dozwolonych źródeł

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umożliwia żądania CORS ze wszystkich źródeł`http` `https`z dowolnym schematem ( lub ). `AllowAnyOrigin`jest niezabezpieczona, ponieważ *każda witryna sieci Web* może składać żądania cross-origin do aplikacji.

> [!NOTE]
> Określanie `AllowAnyOrigin` `AllowCredentials` i jest niezabezpieczona konfiguracja i może spowodować fałszerstwa żądań między lokacjami. Usługa CORS zwraca nieprawidłową odpowiedź CORS, gdy aplikacja jest skonfigurowana przy obu metodach.

`AllowAnyOrigin`wpływa na żądania inspekcji `Access-Control-Allow-Origin` wstępnej i nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> właściwość zasad jako funkcję, która umożliwia początek, aby dopasować skonfigurowaną domenę symboli wieloznacznych podczas oceny, czy pochodzenie jest dozwolone.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Ustawianie dozwolonych metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Zezwala na dowolną metodę HTTP:
* Wpływa na żądania inspekcji `Access-Control-Allow-Methods` wstępnej i nagłówka. Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Ustawianie nagłówków dozwolonych żądań

Aby zezwolić na wysyłane określone nagłówki w żądaniu CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> nazywane [nagłówkami żądań autora](https://xhr.spec.whatwg.org/#request), zadzwoń i określ dozwolone nagłówki:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [nagłówki żądań autora,](https://www.w3.org/TR/cors/#author-request-headers)zadzwoń:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`wpływa na żądania inspekcji wstępnej i nagłówka [nagłówków żądania kontroli dostępu.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)

Zasady oprogramowania pośredniczącego CORS są `WithHeaders` zgodne z określonymi nagłówkami określonymi przez jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie odpowiadają nagłówkom podanym w pliku `WithHeaders`.

Rozważmy na przykład aplikację skonfigurowaną w następujący sposób:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

Oprogramowanie pośredniczące cors odrzuca żądanie inspekcji wstępnej `Content-Language` z następującym nagłówkiem żądania, ponieważ ( `WithHeaders`[HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nie jest wymieniony w :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Aplikacja zwraca odpowiedź *200 OK,* ale nie wysyła nagłówków CORS z powrotem. W związku z tym przeglądarka nie próbuje żądania cross-origin.

### <a name="set-the-exposed-response-headers"></a>Ustawianie nagłówków narażonych odpowiedzi

Domyślnie przeglądarka nie udostępnia wszystkie nagłówki odpowiedzi do aplikacji. Aby uzyskać więcej informacji, zobacz [Udostępnianie zasobów W3C Cross-Origin (Terminologia): Nagłówek prostej odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).

Nagłówki odpowiedzi, które są domyślnie dostępne, to:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specyfikacja CORS wywołuje te nagłówki *proste nagłówki odpowiedzi*. Aby udostępnić aplikacji inne nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>zadzwoń do:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Poświadczenia w żądaniach między źródłami

Poświadczenia wymagają specjalnej obsługi w żądaniu CORS. Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem cross-origin. Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP. Aby wysłać poświadczenia z żądaniem cross-origin, klient musi ustawić `XMLHttpRequest.withCredentials` na `true`.

Korzystanie `XMLHttpRequest` bezpośrednio:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Korzystanie z jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Korzystanie z [interfejsu API pobierania:](https://developer.mozilla.org/docs/Web/API/Fetch_API)

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Serwer musi zezwolić na poświadczenia. Aby zezwolić na poświadczenia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>między źródłami, zadzwoń:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.

Jeśli przeglądarka wysyła poświadczenia, ale odpowiedź nie `Access-Control-Allow-Credentials` zawiera prawidłowego nagłówka, przeglądarka nie udostępnia odpowiedzi na aplikację, a żądanie między źródłami nie powiedzie się.

Zezwalanie na poświadczenia między źródłami jest zagrożeniem bezpieczeństwa. Witryna sieci Web w innej domenie może wysyłać poświadczeń zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specyfikacja CORS stwierdza również, `"*"` że ustawienie początku (wszystkie `Access-Control-Allow-Credentials` źródła) jest nieprawidłowe, jeśli nagłówek jest obecny.

<a name="pref"></a>

## <a name="preflight-requests"></a>Żądania inspekcji wstępnej

W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie [opcji](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) przed wysłaniem rzeczywistego żądania. To żądanie jest nazywane [żądaniem inspekcji wstępnej](https://developer.mozilla.org/docs/Glossary/Preflight_request). Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są ***wszystkie*** następujące warunki:

* Metoda żądania to GET, HEAD lub POST.
* Aplikacja nie ustawia nagłówków żądań `Accept` `Accept-Language`innych `Content-Language` `Content-Type`niż `Last-Event-ID`, , , lub .
* Nagłówek, `Content-Type` jeśli jest ustawiony, ma jedną z następujących wartości:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Reguła nagłówków żądań ustawiona dla żądania klienta ma zastosowanie `setRequestHeader` do `XMLHttpRequest` nagłówków ustawianych przez aplikację, wywołując obiekt. Specyfikacja CORS wywołuje te nagłówki [nagłówków żądania autora nagłówków](https://www.w3.org/TR/cors/#author-request-headers). Reguła nie ma zastosowania do nagłówków, które `User-Agent` `Host`przeglądarka `Content-Length`może ustawić, takich jak , lub .

Poniżej przedstawiono przykładową odpowiedź podobną do żądania inspekcji wstępnej złożonego z przycisku **[Put test]** w sekcji [Test CORS](#testc) tego dokumentu.

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

Żądanie inspekcji wstępnej używa metody [OPCJE HTTP.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Może zawierać następujące nagłówki:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda HTTP, która będzie używana dla rzeczywistego żądania.
* [Nagłówki żądania kontroli dostępu:](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)lista nagłówków żądań ustawianych przez aplikację w rzeczywistym żądaniu. Jak wspomniano wcześniej, nie obejmuje to nagłówków, które `User-Agent`ustawia przeglądarka, takich jak .
* [Metody dostępu-kontrola-zezwalaj](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Jeśli żądanie inspekcji wstępnej zostanie odrzucone, `200 OK` aplikacja zwraca odpowiedź, ale nie ustawia nagłówków CORS. W związku z tym przeglądarka nie próbuje żądania cross-origin. Na przykład odmowy żądania inspekcji wstępnej, zobacz [sekcji Test CORS](#testc) tego dokumentu.

Za pomocą narzędzi F12 aplikacja konsoli wyświetla błąd podobny do jednego z następujących, w zależności od przeglądarki:

* Firefox: Żądanie cross-origin zablokowane: Ta sama polityka pochodzenia nie `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`zezwala na odczytanie zdalnego zasobu w . (Powód: żądanie CORS nie powiodło się). [Dowiedz się więcej](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Chromium na podstawie: Dostęphttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5do pobraniahttps://cors3.azurewebsites.netw ' od początku ' ' został zablokowany przez zasady CORS: Odpowiedź na żądanie inspekcji wstępnej nie przekazuje kontroli dostępu: Nie ma nagłówka "Access-Control-Allow-Origin" w żądanym zasobie. Jeśli nieprzezroczysta odpowiedź służy Twoim potrzebom, ustaw tryb żądania na "no-cors", aby pobrać zasób z wyłączonym mechanizmem CORS.

Aby zezwolić na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>określone nagłówki, zadzwoń:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [nagłówki żądań autora,](https://www.w3.org/TR/cors/#author-request-headers)zadzwoń:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Przeglądarki nie są spójne w `Access-Control-Request-Headers`sposobie ustawiania . Jeśli:

* Nagłówki są ustawione na coś innego niż`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jest wywoływana: `Accept`Dołącz `Content-Type`co `Origin`najmniej , i , plus wszystkie niestandardowe nagłówki, które chcesz obsługiwać.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Automatyczny kod żądania inspekcji wstępnej

Po zastosowaniu zasad CORS:

* Globalnie, `app.UseCors` `Startup.Configure`wywołując w .
* Przy `[EnableCors]` użyciu atrybutu.

ASP.NET Core odpowiada na żądanie opcji inspekcji wstępnej.

Włączenie mechanizmu CORS na podstawie `RequireCors` punktu końcowego przy użyciu obecnie ***nie*** obsługuje automatycznych żądań inspekcji wstępnej.

Sekcja [Test CORS](#testc) tego dokumentu demonstruje to zachowanie.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] atrybut dla żądań inspekcji wstępnej

Gdy mechanizm CORS jest włączony z odpowiednią zasadą, ASP.NET Core zazwyczaj automatycznie odpowiada na żądania inspekcji wstępnej CORS. W niektórych scenariuszach może to nie być przypadek. Na przykład przy użyciu [mechanizmu CORS z routingiem punktów końcowych](#ecors).

Poniższy kod używa atrybutu [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) do tworzenia punktów końcowych dla żądań OPCJI:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Zobacz [Test CORS z routingu punktu końcowego i [HttpOptions]](#tcer) instrukcje dotyczące testowania poprzedniego kodu.

### <a name="set-the-preflight-expiration-time"></a>Ustawianie czasu wygaśnięcia inspekcji wstępnej

Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana. Aby ustawić ten <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>nagłówek, zadzwoń:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak działa CORS

W tej sekcji opisano, co dzieje się w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie wiadomości HTTP.

* CORS **nie** jest funkcją zabezpieczeń. CORS to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.
  * Na przykład złośliwy aktor może użyć [skryptów krzyżowych (XSS)](xref:security/cross-site-scripting) przeciwko witrynie i wykonać żądanie między witrynami do witryny obsługującej mechanizm CORS, aby wykraść informacje.
* Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.
  * To do klienta (przeglądarki), aby wymusić CORS. Serwer wykonuje żądanie i zwraca odpowiedź, to klient zwraca błąd i blokuje odpowiedź. Na przykład dowolne z następujących narzędzi wyświetli odpowiedź serwera:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Przeglądarka internetowa, wprowadzając adres URL na pasku adresu.
* Jest to sposób dla serwera, aby umożliwić przeglądarkom wykonać żądanie [XHR xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub [fetch api,](https://developer.mozilla.org/docs/Web/API/Fetch_API) które w przeciwnym razie byłyby zabronione.
  * Przeglądarki bez CORS nie mogą wykonywać żądań cross-origin. Przed [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) był używany do obejścia tego ograniczenia. JSONP nie używa XHR, używa `<script>` tagu do odbierania odpowiedzi. Skrypty mogą być ładowane cross-origin.

Specyfikacja [CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania cross-origin. Jeśli przeglądarka obsługuje cors, ustawia te nagłówki automatycznie dla żądań cross-origin. Niestandardowy kod JavaScript nie jest wymagany do włączenia usługi CORS.

[Przycisk testu PUT](https://cors3.azurewebsites.net/test) na wdrożonym [przykładzie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Poniżej przedstawiono przykład żądania cross-origin z przycisku testu [wartości](https://cors3.azurewebsites.net/) do `https://cors1.azurewebsites.net/api/values`. Nagłówek: `Origin`

* Udostępnia domenę witryny, która tworzy żądanie.
* Jest to wymagane i musi być inny niż host.

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

W `OPTIONS` żądaniach serwer ustawia nagłówek **nagłówków** `Access-Control-Allow-Origin: {allowed origin}` odpowiedzi w odpowiedzi. Na przykład wdrożone [przykładowe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)żądanie przycisku `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) zawiera następujące nagłówki:

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

W poprzednich **nagłówkach odpowiedzi**serwer ustawia nagłówek [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) w odpowiedzi. Wartość `https://cors1.azurewebsites.net` tego nagłówka `Origin` pasuje do nagłówka z żądania.

Jeśli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> jest wywoływana, `Access-Control-Allow-Origin: *`zwracana jest wartość symboli wieloznacznych. `AllowAnyOrigin`dopuszcza dowolne pochodzenie.

Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami kończy się niepowodzeniem. W szczególności przeglądarka nie zezwala na żądanie. Nawet jeśli serwer zwraca pomyślną odpowiedź, przeglądarka nie udostępnia odpowiedzi do aplikacji klienckiej.

<a name="options"></a>

### <a name="display-options-requests"></a>Wyświetl żądania OPCJI

Domyślnie przeglądarki Chrome i Edge nie wyświetla żądań OPCJI na karcie sieciowe narzędzi F12. Aby wyświetlić żądania opcji w tych przeglądarkach:

* `chrome://flags/#out-of-blink-cors` lub `edge://flags/#out-of-blink-cors`
* wyłączyć flagę.
* Ponownie uruchomić.

Firefox domyślnie wyświetla żądania opcji.

## <a name="cors-in-iis"></a>CORS w iis

Podczas wdrażania w usługach IIS mechanizm CORS musi być uruchomiony przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy. Aby obsługiwać ten scenariusz, [moduł IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) musi zostać zainstalowany i skonfigurowany dla aplikacji.

<a name="testc"></a>

## <a name="test-cors"></a>Badanie CORS

[Przykładowe pobieranie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ma kod do testowania CORS. Zobacz, [jak pobrać](xref:index#how-to-download-a-sample)plik . Przykład jest projektem INTERFEJSU API z dodanymi stronami Razor:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`należy używać tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Poniżej `ValuesController` przedstawiono punkty końcowe do testowania:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) jest dostarczany przez [pakiet Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet i wyświetla informacje o trasie.

Przetestuj poprzedni przykładowy kod przy użyciu jednego z następujących podejść:

* Użyj wdrożonej przykładowej [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)aplikacji w pliku . Nie ma potrzeby pobierania próbki.
* Uruchom próbkę `dotnet run` przy użyciu `https://localhost:5001`domyślnego adresu URL programu .
* Uruchom przykład z programu Visual Studio z portem ustawionym na `https://localhost:44398`44398 dla adresu URL .

Korzystanie z przeglądarki z narzędziami F12:

* Wybierz przycisk **Wartości** i przejrzyj nagłówki na karcie **Sieć.**
* Wybierz przycisk **testu PUT.** Zobacz [Wyświetlanie opcji opcji, aby](#options) uzyskać instrukcje dotyczące wyświetlania żądania OPCJE. **Test PUT** tworzy dwa żądania, żądanie inspekcji wstępnej opcje i żądanie PUT.
* Wybierz **`GetValues2 [DisableCors]`** przycisk, aby wyzwolić nieudane żądanie CORS. Jak wspomniano w dokumencie, odpowiedź zwraca 200 sukcesów, ale żądanie CORS nie jest. Wybierz kartę **Konsola,** aby wyświetlić błąd CORS. W zależności od przeglądarki wyświetlany jest błąd podobny do następującego:

     Dostęp do pobierania `'https://cors1.azurewebsites.net/api/values/GetValues2'` od `'https://cors3.azurewebsites.net'` źródła został zablokowany przez zasady CORS: W żądanym zasobie nie ma nagłówka "Access-Control-Allow-Origin". Jeśli nieprzezroczysta odpowiedź służy Twoim potrzebom, ustaw tryb żądania na "no-cors", aby pobrać zasób z wyłączonym mechanizmem CORS.
     
Punkty końcowe obsługujące mechanizm CORS można testować za pomocą narzędzia, takiego jak [curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)lub [Postman](https://www.getpostman.com/). W przypadku korzystania z narzędzia pochodzenie żądania `Origin` określonego przez nagłówek musi się różnić od hosta odbierającego żądanie. Jeśli żądanie nie jest *cross-origin* na podstawie `Origin` wartości nagłówka:

* Nie ma potrzeby, aby oprogramowanie pośredniczące CORS przetwarzać żądanie.
* Nagłówki CORS nie są zwracane w odpowiedzi.

Następujące polecenie używa `curl` do wystawić żądanie OPTIONS z informacjami:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testowanie cors z routingu punktu końcowego i [HttpOptions]

Włączenie mechanizmu CORS dla punktu `RequireCors` końcowego przy użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf). Należy wziąć pod uwagę następujący kod, który używa [routingu punktu końcowego, aby włączyć CORS:](#ecors)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Poniżej `TodoItems1Controller` przedstawiono punkty końcowe do testowania:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=1) wdrożonego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).

**Przyciski Delete [EnableCors]** i **GET [EnableCors]** powiodą się, ponieważ punkty końcowe mają `[EnableCors]` żądania inspekcji wstępnej i odpowiadają na nie. Inne punkty końcowe kończy się niepowodzeniem. Przycisk **GET** kończy się niepowodzeniem, ponieważ [javascript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) wysyła:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Poniżej `TodoItems2Controller` przedstawiono podobne punkty końcowe, ale zawiera jawny kod, aby odpowiedzieć na żądania OPCJI:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=2) wdrożonego przykładu. Na liście rozwijanej **Kontroler** wybierz pozycję **Inspekcji wstępnej,** a następnie **ustaw kontroler**. Wszystkie wywołania CORS `TodoItems2Controller` do punktów końcowych zakończyć się pomyślnie.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Wprowadzenie do modułu IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym artykule pokazano, jak włączyć usługę CORS w aplikacji ASP.NET Core.

Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego pochodzenia*. Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny. Czasami możesz zezwolić innym witrynom na składanie żądań krzyżowych do aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Udostępnianie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):

* Jest standardem W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.
* **Nie** jest to funkcja zabezpieczeń, CORS rozluźnia zabezpieczenia. Interfejs API nie jest bezpieczniejsze, umożliwiając CORS. Aby uzyskać więcej informacji, zobacz [Jak działa CORS](#how-cors).
* Umożliwia serwerowi jawne zezwalanie na niektóre żądania między źródłami, jednocześnie odrzucając inne.
* Jest bezpieczniejsza i bardziej elastyczna niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>To samo pochodzenie

Dwa adresy URL mają to samo pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454).](https://tools.ietf.org/html/rfc6454)

Te dwa adresy URL mają to samo źródło:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Te adresy URL mają inne źródła niż poprzednie dwa adresy URL:

* `https://example.net`&ndash; Inna domena
* `https://www.example.com/foo.html`&ndash; Inna poddomena
* `http://example.com/foo.html`&ndash; Inny schemat
* `https://example.com:9000/foo.html`&ndash; Inny port

Program Internet Explorer nie bierze pod uwagę portu podczas porównywania źródeł.

## <a name="cors-with-named-policy-and-middleware"></a>CORS z nazwanymi zasadami i oprogramowaniem pośredniczącym

Oprogramowanie pośredniczące CORS obsługuje żądania cross-origin. Poniższy kod umożliwia CORS dla całej aplikacji z określonym źródłem:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Powyższy kod ma następujące działanie:

* Ustawia nazwę zasad\_na "myAllowSpecificOrigins". Nazwa zasad jest dowolna.
* Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia, która umożliwia CORS.
* Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda bierze <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt. [Opcje konfiguracji](#cors-policy-options), `WithOrigins`takie jak , są opisane w dalszej części tego artykułu.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Aby uzyskać więcej informacji, zobacz [opcje zasad CORS](#cpo) w tym dokumencie .

Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> może łańcuch metod, jak pokazano w poniższym kodzie:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Uwaga: Adres URL **nie** może zawierać`/`końcowego ukośnika ( ). Jeśli adres URL `/`zostanie zakończony `false` na , porównanie zwraca się i nie zwracany jest żaden nagłówek.

Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji za pośrednictwem oprogramowania pośredniczącego CORS:
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

Zobacz [Włączanie cors w razor strony, kontrolery i metody działania,](#ecors) aby zastosować zasady CORS na poziomie strony/kontrolera / akcji.

Zobacz [Test CORS](#test) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.

## <a name="enable-cors-with-attributes"></a>Włącz cors z atrybutami

[EnableCors&rbrack; Atrybut stanowi alternatywę dla stosowania CORS globalnie. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Atrybut `[EnableCors]` umożliwia CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych.

Służy `[EnableCors]` do określania `[EnableCors("{Policy String}")]` zasad domyślnych i określania zasad.

Atrybut `[EnableCors]` można zastosować do:

* Strona Razor`PageModel`
* Kontrolera
* Metoda akcji kontrolera

Można zastosować różne zasady do kontrolera/modelu `[EnableCors]` strony/akcji z atrybutem. Gdy `[EnableCors]` atrybut jest stosowany do controllers/page model/action method, a CORS jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane. Nie ***zalecamy*** łączenia zasad. Użyj `[EnableCors]` atrybutu lub oprogramowania pośredniczącego, ***nie oba**. Podczas `[EnableCors]`korzystania z programu **nie** należy definiować zasad domyślnych.

Poniższy kod stosuje inną zasadę do każdej metody:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Poniższy kod tworzy domyślną zasadę `"AnotherPolicy"`CORS i zasadę o nazwie:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Wyłącz cors

Atrybut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) wyłącza cors dla kontrolera/page-model/action.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Warianty polityki CORS

W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:

* [Ustawianie dozwolonych źródeł](#set-the-allowed-origins)
* [Ustawianie dozwolonych metod HTTP](#set-the-allowed-http-methods)
* [Ustawianie nagłówków dozwolonych żądań](#set-the-allowed-request-headers)
* [Ustawianie nagłówków narażonych odpowiedzi](#set-the-exposed-response-headers)
* [Poświadczenia w żądaniach między źródłami](#credentials-in-cross-origin-requests)
* [Ustawianie czasu wygaśnięcia inspekcji wstępnej](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`. W przypadku niektórych opcji warto najpierw przeczytać sekcję [Jak działa CORS.](#how-cors)

## <a name="set-the-allowed-origins"></a>Ustawianie dozwolonych źródeł

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umożliwia żądania CORS ze wszystkich źródeł`http` `https`z dowolnym schematem ( lub ). `AllowAnyOrigin`jest niezabezpieczona, ponieważ *każda witryna sieci Web* może składać żądania cross-origin do aplikacji.

> [!NOTE]
> Określanie `AllowAnyOrigin` `AllowCredentials` i jest niezabezpieczona konfiguracja i może spowodować fałszerstwa żądań między lokacjami. W przypadku bezpiecznej aplikacji określ dokładną listę źródeł, jeśli klient musi autoryzować się, aby uzyskać dostęp do zasobów serwera.

`AllowAnyOrigin`wpływa na żądania inspekcji `Access-Control-Allow-Origin` wstępnej i nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> właściwość zasad jako funkcję, która umożliwia początek, aby dopasować skonfigurowaną domenę symboli wieloznacznych podczas oceny, czy pochodzenie jest dozwolone.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Ustawianie dozwolonych metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Zezwala na dowolną metodę HTTP:
* Wpływa na żądania inspekcji `Access-Control-Allow-Methods` wstępnej i nagłówka. Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Ustawianie nagłówków dozwolonych żądań

Aby zezwolić na wysyłane określone nagłówki w żądaniu CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> nazywane *nagłówkami żądań autora*, zadzwoń i określ dozwolone nagłówki:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>nagłówki żądań autora, zadzwoń:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

To ustawienie ma wpływ na `Access-Control-Request-Headers` żądania inspekcji wstępnej i nagłówek. Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)

Oprogramowanie pośredniczące CORS zawsze `Access-Control-Request-Headers` umożliwia cztery nagłówki w celu wysłania niezależnie od wartości skonfigurowanych w CorsPolicy.Headers. Ta lista nagłówków zawiera:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Rozważmy na przykład aplikację skonfigurowaną w następujący sposób:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Oprogramowanie pośredniczące cors odpowiada pomyślnie na żądanie inspekcji `Content-Language` wstępnej z następującym nagłówkiem żądania, ponieważ jest zawsze na białej liście:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Ustawianie nagłówków narażonych odpowiedzi

Domyślnie przeglądarka nie udostępnia wszystkie nagłówki odpowiedzi do aplikacji. Aby uzyskać więcej informacji, zobacz [Udostępnianie zasobów W3C Cross-Origin (Terminologia): Nagłówek prostej odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).

Nagłówki odpowiedzi, które są domyślnie dostępne, to:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specyfikacja CORS wywołuje te nagłówki *proste nagłówki odpowiedzi*. Aby udostępnić aplikacji inne nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>zadzwoń do:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Poświadczenia w żądaniach między źródłami

Poświadczenia wymagają specjalnej obsługi w żądaniu CORS. Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem cross-origin. Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP. Aby wysłać poświadczenia z żądaniem cross-origin, klient musi ustawić `XMLHttpRequest.withCredentials` na `true`.

Korzystanie `XMLHttpRequest` bezpośrednio:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Korzystanie z jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Korzystanie z [interfejsu API pobierania:](https://developer.mozilla.org/docs/Web/API/Fetch_API)

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Serwer musi zezwolić na poświadczenia. Aby zezwolić na poświadczenia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>między źródłami, zadzwoń:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.

Jeśli przeglądarka wysyła poświadczenia, ale odpowiedź nie `Access-Control-Allow-Credentials` zawiera prawidłowego nagłówka, przeglądarka nie udostępnia odpowiedzi na aplikację, a żądanie między źródłami nie powiedzie się.

Zezwalanie na poświadczenia między źródłami jest zagrożeniem bezpieczeństwa. Witryna sieci Web w innej domenie może wysyłać poświadczeń zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specyfikacja CORS stwierdza również, `"*"` że ustawienie początku (wszystkie `Access-Control-Allow-Credentials` źródła) jest nieprawidłowe, jeśli nagłówek jest obecny.

### <a name="preflight-requests"></a>Żądania inspekcji wstępnej

W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie przed wysłaniem rzeczywistego żądania. To żądanie jest nazywane *żądaniem inspekcji wstępnej*. Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są następujące warunki:

* Metoda żądania to GET, HEAD lub POST.
* Aplikacja nie ustawia nagłówków żądań `Accept` `Accept-Language`innych `Content-Language` `Content-Type`niż `Last-Event-ID`, , , lub .
* Nagłówek, `Content-Type` jeśli jest ustawiony, ma jedną z następujących wartości:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Reguła nagłówków żądań ustawiona dla żądania klienta ma zastosowanie `setRequestHeader` do `XMLHttpRequest` nagłówków ustawianych przez aplikację, wywołując obiekt. Specyfikacja CORS wywołuje te nagłówki *nagłówków żądania autora nagłówków*. Reguła nie ma zastosowania do nagłówków, które `User-Agent` `Host`przeglądarka `Content-Length`może ustawić, takich jak , lub .

Oto przykład żądania inspekcji wstępnej:

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

Żądanie przed lotem używa metody OPCJE HTTP. Zawiera dwa specjalne nagłówki:

* `Access-Control-Request-Method`: Metoda HTTP, która będzie używana dla rzeczywistego żądania.
* `Access-Control-Request-Headers`: Lista nagłówków żądań ustawianych przez aplikację w rzeczywistym żądaniu. Jak wspomniano wcześniej, nie obejmuje to nagłówków, które `User-Agent`ustawia przeglądarka, takich jak .

<!-- I think this needs to be removed, was put here accidently -->

Gdy mechanizm CORS jest włączony z odpowiednią zasadą, ASP.NET Core zazwyczaj automatycznie odpowiada na żądania inspekcji wstępnej CORS. Zobacz [atrybut [HttpOptions] dla żądań inspekcji wstępnej](#pro).

Żądanie inspekcji wstępnej `Access-Control-Request-Headers` cors może zawierać nagłówek, który wskazuje serwerowi nagłówki, które są wysyłane z rzeczywistym żądaniem.

Aby zezwolić na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>określone nagłówki, zadzwoń:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>nagłówki żądań autora, zadzwoń:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Przeglądarki nie są całkowicie spójne w `Access-Control-Request-Headers`sposobie ustawiania. Jeśli nagłówki są ustawione na `"*"` coś <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>innego niż (lub `Accept` `Content-Type`użycie), `Origin`należy dołączyć co najmniej , i , plus wszystkie niestandardowe nagłówki, które chcesz obsługiwać.

Poniżej przedstawiono przykładową odpowiedź na żądanie inspekcji wstępnej (przy założeniu, że serwer zezwala na żądanie):

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

Odpowiedź zawiera `Access-Control-Allow-Methods` nagłówek, który zawiera listę dozwolonych `Access-Control-Allow-Headers` metod i opcjonalnie nagłówek, który zawiera listę dozwolonych nagłówków. Jeśli żądanie inspekcji wstępnej zakończy się pomyślnie, przeglądarka wysyła rzeczywiste żądanie.

Jeśli żądanie inspekcji wstępnej zostanie odrzucone, aplikacja zwraca odpowiedź *200 OK,* ale nie wysyła nagłówków CORS z powrotem. W związku z tym przeglądarka nie próbuje żądania cross-origin.

### <a name="set-the-preflight-expiration-time"></a>Ustawianie czasu wygaśnięcia inspekcji wstępnej

Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana. Aby ustawić ten <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>nagłówek, zadzwoń:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak działa CORS

W tej sekcji opisano, co dzieje się w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie wiadomości HTTP.

* CORS **nie** jest funkcją zabezpieczeń. CORS to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.
  * Na przykład złośliwy aktor może użyć [prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) przeciwko witrynie i wykonać żądanie między witrynami do witryny obsługującej cors do kradzieży informacji.
* Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.
  * To do klienta (przeglądarki), aby wymusić CORS. Serwer wykonuje żądanie i zwraca odpowiedź, to klient zwraca błąd i blokuje odpowiedź. Na przykład dowolne z następujących narzędzi wyświetli odpowiedź serwera:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Przeglądarka internetowa, wprowadzając adres URL na pasku adresu.
* Jest to sposób dla serwera, aby umożliwić przeglądarkom wykonać żądanie [XHR xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub [fetch api,](https://developer.mozilla.org/docs/Web/API/Fetch_API) które w przeciwnym razie byłyby zabronione.
  * Przeglądarki (bez CORS) nie mogą wykonywać żądań cross-origin. Przed [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) był używany do obejścia tego ograniczenia. JSONP nie używa XHR, używa `<script>` tagu do odbierania odpowiedzi. Skrypty mogą być ładowane cross-origin.

Specyfikacja [CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania cross-origin. Jeśli przeglądarka obsługuje cors, ustawia te nagłówki automatycznie dla żądań cross-origin. Niestandardowy kod JavaScript nie jest wymagany do włączenia usługi CORS.

Poniżej przedstawiono przykład żądania cross-origin. Nagłówek `Origin` udostępnia domenę witryny, która tworzy żądanie. Nagłówek `Origin` jest wymagany i musi być inny niż host.

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

Jeśli serwer zezwala na żądanie, `Access-Control-Allow-Origin` ustawia nagłówek w odpowiedzi. Wartość tego nagłówka jest `Origin` zgodna z nagłówkiem z żądania `"*"`lub jest wartością symboli wieloznacznych, co oznacza, że dowolne pochodzenie jest dozwolone:

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

Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami kończy się niepowodzeniem. W szczególności przeglądarka nie zezwala na żądanie. Nawet jeśli serwer zwraca pomyślną odpowiedź, przeglądarka nie udostępnia odpowiedzi do aplikacji klienckiej.

<a name="test"></a>

## <a name="test-cors"></a>Badanie CORS

Aby przetestować CORS:

1. [Tworzenie projektu interfejsu API](xref:tutorials/first-web-api). Alternatywnie można [pobrać przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Włącz CORS przy użyciu jednego z podejść w tym dokumencie. Przykład:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`należy używać tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Utwórz projekt aplikacji internetowej (Razor Pages lub MVC). W przykładzie użyto stron Razor. Aplikację sieci web można utworzyć w tym samym rozwiązaniu co projekt interfejsu API.
1. Dodaj następujący wyróżniony kod do pliku *Index.cshtml:*

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. W poprzednim kodzie `url: 'https://<web app>.azurewebsites.net/api/values/1',` zastąp adresem URL wdrożonej aplikacji.
1. Wdrażanie projektu interfejsu API. Na przykład [wdrożyć na platformie Azure](xref:host-and-deploy/azure-apps/index).
1. Uruchom stronę Razor Pages lub aplikację MVC z pulpitu i kliknij przycisk **Testuj.** Narzędzia F12 umożliwia przeglądanie komunikatów o błędach.
1. Usuń pochodzenie localhost `WithOrigins` z i wdrożyć aplikację. Alternatywnie uruchom aplikację kliencką z innym portem. Na przykład uruchom z programu Visual Studio.
1. Przetestuj za pomocą aplikacji klienckiej. Błędy mechanizmu CORS zwracają błąd, ale komunikat o błędzie nie jest dostępny w języku JavaScript. Użyj karty konsoli w narzędziach F12, aby zobaczyć błąd. W zależności od przeglądarki pojawia się błąd (w konsoli narzędzi F12) podobny do następującego:

   * Korzystanie z przeglądarki Microsoft Edge:

     **SEC7120: [CORS] `https://localhost:44375` Źródło nie `https://localhost:44375` został odnaleźć w nagłówku odpowiedzi Access-Control-Allow-Origin dla zasobu między źródłami w`https://webapi.azurewebsites.net/api/values/1`**

   * Korzystanie z Chrome:

     **Dostęp do protokołu XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` u źródła `https://localhost:44375` został zablokowany przez zasady CORS: W żądanym zasobie nie ma nagłówka "Access-Control-Allow-Origin".**
     
Punkty końcowe obsługujące mechanizm CORS można testować za pomocą narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) lub [Listonosz.](https://www.getpostman.com/) W przypadku korzystania z narzędzia pochodzenie żądania `Origin` określonego przez nagłówek musi się różnić od hosta odbierającego żądanie. Jeśli żądanie nie jest *cross-origin* na podstawie `Origin` wartości nagłówka:

* Nie ma potrzeby, aby oprogramowanie pośredniczące CORS przetwarzać żądanie.
* Nagłówki CORS nie są zwracane w odpowiedzi.

## <a name="cors-in-iis"></a>CORS w iis

Podczas wdrażania w usługach IIS mechanizm CORS musi być uruchomiony przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy. Aby obsługiwać ten scenariusz, [moduł IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) musi zostać zainstalowany i skonfigurowany dla aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Wprowadzenie do modułu IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
