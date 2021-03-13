---
title: Buforowanie oprogramowania pośredniczącego w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować i używać oprogramowania pośredniczącego buforowania odpowiedzi w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 11473bbf8b4e2d67d15798a5d87ee01761682f9a
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413473"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Buforowanie oprogramowania pośredniczącego w ASP.NET Core

Przez [Jan Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

W tym artykule opisano sposób konfigurowania oprogramowania pośredniczącego buforowania odpowiedzi w aplikacji ASP.NET Core. Oprogramowanie pośredniczące określa, kiedy odpowiedzi są buforowane, przechowuje odpowiedzi i obsługuje odpowiedzi z pamięci podręcznej. Wprowadzenie do buforowania HTTP i [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atrybutu można znaleźć w temacie [buforowanie odpowiedzi](xref:performance/caching/response).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfigurowanie

Oprogramowanie pośredniczące buforowania odpowiedzi jest niejawnie dostępne dla aplikacji ASP.NET Core za pośrednictwem udostępnionej platformy.

W programie `Startup.ConfigureServices` Dodaj oprogramowanie pośredniczące buforowania odpowiedzi do kolekcji usług:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Skonfiguruj aplikację do korzystania z oprogramowania pośredniczącego z <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> metodą rozszerzenia, która dodaje oprogramowanie pośredniczące do potoku przetwarzania żądań w `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> musi być wywoływana przed <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> użyciem [oprogramowania pośredniczącego CORS](xref:security/cors).

Przykładowa aplikacja dodaje nagłówki, aby kontrolować buforowanie w kolejnych żądaniach:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): buforuje odpowiedzi w pamięci podręcznej przez maksymalnie 10 sekund.
* [Zróżnicuj](https://tools.ietf.org/html/rfc7231#section-7.1.4): konfiguruje oprogramowanie pośredniczące, aby obsługiwało buforowaną odpowiedź tylko wtedy, gdy nagłówek [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) kolejnych żądań jest zgodny z oryginalnym żądaniem.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Poprzednie nagłówki nie są zapisywane w odpowiedzi i są zastępowane, gdy kontroler, Akcja lub Razor Strona:

* Ma atrybut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Dotyczy to nawet sytuacji, gdy właściwość nie jest ustawiona. Na przykład pominięcie właściwości [VaryByHeader](./response.md#vary) spowoduje usunięcie odpowiedniego nagłówka z odpowiedzi.

Buforowanie odpowiedzi w pamięci podręcznej tylko buforuje odpowiedzi serwera, które powodują, że jest to kod stanu 200 (OK). Wszystkie inne odpowiedzi, w tym [strony błędów](xref:fundamentals/error-handling), są ignorowane przez oprogramowanie pośredniczące.

> [!WARNING]
> Odpowiedzi zawierające zawartość uwierzytelnionych klientów muszą być oznaczone jako nieobsługujące pamięci podręcznej, aby uniemożliwić przechowywanie i obsługę tych odpowiedzi przez oprogramowanie pośredniczące. Zobacz [warunki buforowania](#conditions-for-caching) , aby uzyskać szczegółowe informacje na temat sposobu, w jaki oprogramowanie pośredniczące określa, czy odpowiedź jest buforowana.

## <a name="options"></a>Opcje

Opcje buforowania odpowiedzi przedstawiono w poniższej tabeli.

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Największy rozmiar pamięci podręcznej dla treści odpowiedzi (w bajtach). Wartość domyślna to `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Limit rozmiaru dla oprogramowania pośredniczącego pamięci podręcznej odpowiedzi w bajtach. Wartość domyślna to `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Określa, czy odpowiedzi są buforowane w przypadku ścieżek z uwzględnieniem wielkości liter. Wartość domyślna to `false`. |

Poniższy przykład konfiguruje oprogramowanie pośredniczące w następujący sposób:

* Buforuj odpowiedzi o rozmiarze treści mniejszym lub równym 1 024 bajtów.
* Przechowaj odpowiedzi w ścieżce z uwzględnieniem wielkości liter. Na przykład `/page1` i `/Page1` są przechowywane osobno.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

W przypadku używania kontrolerów MVC/Web API lub Razor modeli stron, [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atrybut określa parametry niezbędne do ustawiania odpowiednich nagłówków dla buforowania odpowiedzi. Jedyny parametr `[ResponseCache]` atrybutu, który ściśle wymaga oprogramowania pośredniczącego <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , to, który nie odpowiada rzeczywistemu nagłówkowi http. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response#responsecache-attribute>.

Gdy nie korzystasz z `[ResponseCache]` atrybutu, buforowanie odpowiedzi może być zróżnicowane z `VaryByQueryKeys` . Użyj <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> bezpośrednio z [funkcji HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Użycie pojedynczej wartości równej `*` w w programie `VaryByQueryKeys` zmienia pamięć podręczną przez wszystkie parametry zapytania żądania.

## <a name="http-headers-used-by-response-caching-middleware"></a>Nagłówki HTTP używane przez oprogramowanie pośredniczące buforowania odpowiedzi

Poniższa tabela zawiera informacje dotyczące nagłówków HTTP, które mają wpływ na buforowanie odpowiedzi.

| Nagłówek | Szczegóły |
| ------ | ------- |
| `Authorization` | Odpowiedź nie jest buforowana, jeśli nagłówek istnieje. |
| `Cache-Control` | Oprogramowanie pośredniczące traktuje tylko odpowiedzi w pamięci `public` podręcznej oznaczone za pomocą dyrektywy Cache. Sterowanie buforowaniem przy użyciu następujących parametrów:<ul><li>maks. wiek</li><li>Max-nieodświeżone&#8224;</li><li>min — nowy</li><li>musi ponownie sprawdzić poprawność</li><li>nie-pamięć podręczna</li><li>bez sklepu</li><li>tylko-w pamięci podręcznej</li><li>private</li><li>public</li><li>s-maxage</li><li>serwer proxy — ponowne sprawdzanie poprawności&#8225;</li></ul>&#8224;Jeśli żaden limit nie zostanie określony do `max-stale` , oprogramowanie pośredniczące nie podejmuje żadnych działań.<br>&#8225;`proxy-revalidate` ma ten sam skutek co `must-revalidate` .<br><br>Aby uzyskać więcej informacji, zobacz [RFC 7231: Request Cache-Control dyrektywy](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`Nagłówek w żądaniu daje ten sam skutek co `Cache-Control: no-cache` . Ten nagłówek jest zastępowany przez odpowiednie dyrektywy w `Cache-Control` nagłówku, jeśli jest obecny. Uwzględnianie zgodności z poprzednimi wersjami przy użyciu protokołu HTTP/1.0. |
| `Set-Cookie` | Odpowiedź nie jest buforowana, jeśli nagłówek istnieje. Wszystkie oprogramowanie pośredniczące w potoku przetwarzania żądań, które ustawia jeden lub więcej cookie s uniemożliwia buforowanie odpowiedzi przez oprogramowanie pośredniczące do buforowania odpowiedzi (na przykład [ cookie dostawcy TempData opartego na protokole](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary`Nagłówek jest używany do różnicowania buforowanej odpowiedzi przez inny nagłówek. Na przykład w pamięci podręcznej odpowiedzi są kodowane przez dołączenie `Vary: Accept-Encoding` nagłówka, w którym są buforowane odpowiedzi na żądania z nagłówkami `Accept-Encoding: gzip` i `Accept-Encoding: text/plain` oddzielnie. Odpowiedź z wartością nagłówka `*` nigdy nie jest przechowywana. |
| `Expires` | Odpowiedź uznana za nieodświeżoną przez ten nagłówek nie jest zapisywana ani pobierana, chyba że zostanie zastąpiona przez inne `Cache-Control` nagłówki. |
| `If-None-Match` | Pełna odpowiedź jest obsługiwana z pamięci podręcznej, jeśli wartość nie jest `*` , a `ETag` odpowiedź nie jest zgodna z żadną z podanych wartości. W przeciwnym razie jest obsługiwana odpowiedź 304 (nie modyfikowane). |
| `If-Modified-Since` | Jeśli `If-None-Match` nagłówek nie istnieje, jest obsługiwana Pełna odpowiedź z pamięci podręcznej, jeśli data odpowiedzi w pamięci podręcznej jest nowsza niż podana wartość. W przeciwnym razie jest obsługiwana odpowiedź *304 — nie zmodyfikowano* . |
| `Date` | W przypadku obsługi z pamięci podręcznej `Date` nagłówek jest ustawiany przez oprogramowanie pośredniczące, jeśli nie został podany w oryginalnej odpowiedzi. |
| `Content-Length` | W przypadku obsługi z pamięci podręcznej `Content-Length` nagłówek jest ustawiany przez oprogramowanie pośredniczące, jeśli nie został podany w oryginalnej odpowiedzi. |
| `Age` | `Age`Nagłówek wysłany w oryginalnej odpowiedzi jest ignorowany. Oprogramowanie pośredniczące oblicza nową wartość w przypadku obsługi odpowiedzi w pamięci podręcznej. |

## <a name="caching-respects-request-cache-control-directives"></a>Cache-Control dyrektywy z uwzględnieniem pamięci podręcznej

Oprogramowanie pośredniczące uwzględnia reguły [buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Reguły wymagają pamięci podręcznej, aby honorować prawidłowy `Cache-Control` nagłówek Wysłany przez klienta. W obszarze specyfikacji klient może wykonywać żądania z `no-cache` wartością nagłówka i wymusić, aby serwer generował nową odpowiedź dla każdego żądania. Obecnie nie ma kontroli nad tym zachowaniem buforowania podczas korzystania z oprogramowania pośredniczącego, ponieważ oprogramowanie pośredniczące jest zgodne z oficjalną specyfikacją buforowania.

Aby uzyskać większą kontrolę nad zachowaniem buforowania, zapoznaj się z innymi funkcjami buforowania ASP.NET Core. Zobacz następujące tematy:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zachowanie buforowania nie jest zgodne z oczekiwaniami, upewnij się, że odpowiedzi są buforowane i mogą być obsługiwane z pamięci podręcznej. Sprawdź przychodzące nagłówki żądania i wychodzące nagłówki odpowiedzi. Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby pomóc w debugowaniu.

Podczas testowania i rozwiązywania problemów z pamięcią podręczną przeglądarka może ustawić nagłówki żądań, które mają wpływ na buforowanie na różne sposoby. Na przykład przeglądarka może ustawić `Cache-Control` nagłówek na `no-cache` lub `max-age=0` podczas odświeżania strony. Poniższe narzędzia mogą jawnie ustawić nagłówki żądań i są preferowane w celu przeprowadzenia testu w pamięci podręcznej:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Warunki buforowania

* Żądanie musi spowodować odpowiedź serwera z kodem stanu 200 (OK).
* Metoda żądania musi mieć wartość GET lub $.
* W programie `Startup.Configure` oprogramowanie pośredniczące buforowania odpowiedzi należy umieścić przed oprogramowanie pośredniczące wymagające buforowania. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.
* `Authorization`Nagłówek nie może być obecny.
* `Cache-Control` parametry nagłówka muszą być prawidłowe, a odpowiedź musi być oznaczona jako `public` nieoznaczona `private` .
* `Pragma: no-cache`Nagłówek nie może być obecny `Cache-Control` , jeśli nagłówek nie istnieje, jako że `Cache-Control` nagłówek zastępuje nagłówek, `Pragma` gdy jest obecny.
* `Set-Cookie`Nagłówek nie może być obecny.
* `Vary` parametry nagłówka muszą być prawidłowe i nie mogą być równe `*` .
* `Content-Length`Wartość nagłówka (jeśli jest ustawiona) musi być zgodna z rozmiarem treści odpowiedzi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Nie jest używany.
* Odpowiedź nie może być nieodświeżona zgodnie z definicją `Expires` nagłówka i `max-age` `s-maxage` dyrektywy pamięci podręcznej.
* Buforowanie odpowiedzi musi się powieść. Rozmiar odpowiedzi musi być mniejszy niż skonfigurowany lub domyślny <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Rozmiar treści odpowiedzi musi być mniejszy niż skonfigurowany lub domyślny <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Odpowiedź musi być buforowana zgodnie ze specyfikacją [RFC 7234](https://tools.ietf.org/html/rfc7234) . Na przykład `no-store` dyrektywa nie może istnieć w polach żądania ani nagłówka odpowiedzi. Zobacz *sekcję 3: przechowywanie odpowiedzi w pamięci podręcznej* [RFC 7234](https://tools.ietf.org/html/rfc7234) , aby uzyskać szczegółowe informacje.

> [!NOTE]
> System antysfałszowany służący do generowania zabezpieczonych tokenów, aby zapobiec atakom przez wiele witryn (CSRF), w `Cache-Control` związku z czym `Pragma` `no-cache` odpowiedzi nie są buforowane. Aby uzyskać informacje na temat sposobu wyłączania tokenów antysfałszowanych dla elementów formularza HTML, zobacz <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule opisano sposób konfigurowania oprogramowania pośredniczącego buforowania odpowiedzi w aplikacji ASP.NET Core. Oprogramowanie pośredniczące określa, kiedy odpowiedzi są buforowane, przechowuje odpowiedzi i obsługuje odpowiedzi z pamięci podręcznej. Wprowadzenie do buforowania HTTP i [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atrybutu można znaleźć w temacie [buforowanie odpowiedzi](xref:performance/caching/response).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfigurowanie

Użyj pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

W programie `Startup.ConfigureServices` Dodaj oprogramowanie pośredniczące buforowania odpowiedzi do kolekcji usług:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Skonfiguruj aplikację do korzystania z oprogramowania pośredniczącego z <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> metodą rozszerzenia, która dodaje oprogramowanie pośredniczące do potoku przetwarzania żądań w `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Przykładowa aplikacja dodaje nagłówki, aby kontrolować buforowanie w kolejnych żądaniach:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): buforuje odpowiedzi w pamięci podręcznej przez maksymalnie 10 sekund.
* [Zróżnicuj](https://tools.ietf.org/html/rfc7231#section-7.1.4): konfiguruje oprogramowanie pośredniczące, aby obsługiwało buforowaną odpowiedź tylko wtedy, gdy nagłówek [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) kolejnych żądań jest zgodny z oryginalnym żądaniem.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Poprzednie nagłówki nie są zapisywane w odpowiedzi i są zastępowane, gdy kontroler, Akcja lub Razor Strona:

* Ma atrybut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Dotyczy to nawet sytuacji, gdy właściwość nie jest ustawiona. Na przykład pominięcie właściwości [VaryByHeader](./response.md#vary) spowoduje usunięcie odpowiedniego nagłówka z odpowiedzi.

Buforowanie odpowiedzi w pamięci podręcznej tylko buforuje odpowiedzi serwera, które powodują, że jest to kod stanu 200 (OK). Wszystkie inne odpowiedzi, w tym [strony błędów](xref:fundamentals/error-handling), są ignorowane przez oprogramowanie pośredniczące.

> [!WARNING]
> Odpowiedzi zawierające zawartość uwierzytelnionych klientów muszą być oznaczone jako nieobsługujące pamięci podręcznej, aby uniemożliwić przechowywanie i obsługę tych odpowiedzi przez oprogramowanie pośredniczące. Zobacz [warunki buforowania](#conditions-for-caching) , aby uzyskać szczegółowe informacje na temat sposobu, w jaki oprogramowanie pośredniczące określa, czy odpowiedź jest buforowana.

## <a name="options"></a>Opcje

Opcje buforowania odpowiedzi przedstawiono w poniższej tabeli.

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Największy rozmiar pamięci podręcznej dla treści odpowiedzi (w bajtach). Wartość domyślna to `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Limit rozmiaru dla oprogramowania pośredniczącego pamięci podręcznej odpowiedzi w bajtach. Wartość domyślna to `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Określa, czy odpowiedzi są buforowane w przypadku ścieżek z uwzględnieniem wielkości liter. Wartość domyślna to `false`. |

Poniższy przykład konfiguruje oprogramowanie pośredniczące w następujący sposób:

* Buforuj odpowiedzi o rozmiarze treści mniejszym lub równym 1 024 bajtów.
* Przechowaj odpowiedzi w ścieżce z uwzględnieniem wielkości liter. Na przykład `/page1` i `/Page1` są przechowywane osobno.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

W przypadku używania kontrolerów MVC/Web API lub Razor modeli stron, [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atrybut określa parametry niezbędne do ustawiania odpowiednich nagłówków dla buforowania odpowiedzi. Jedyny parametr `[ResponseCache]` atrybutu, który ściśle wymaga oprogramowania pośredniczącego <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , to, który nie odpowiada rzeczywistemu nagłówkowi http. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response#responsecache-attribute>.

Gdy nie korzystasz z `[ResponseCache]` atrybutu, buforowanie odpowiedzi może być zróżnicowane z `VaryByQueryKeys` . Użyj <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> bezpośrednio z [funkcji HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Użycie pojedynczej wartości równej `*` w w programie `VaryByQueryKeys` zmienia pamięć podręczną przez wszystkie parametry zapytania żądania.

## <a name="http-headers-used-by-response-caching-middleware"></a>Nagłówki HTTP używane przez oprogramowanie pośredniczące buforowania odpowiedzi

Poniższa tabela zawiera informacje dotyczące nagłówków HTTP, które mają wpływ na buforowanie odpowiedzi.

| Nagłówek | Szczegóły |
| ------ | ------- |
| `Authorization` | Odpowiedź nie jest buforowana, jeśli nagłówek istnieje. |
| `Cache-Control` | Oprogramowanie pośredniczące traktuje tylko odpowiedzi w pamięci `public` podręcznej oznaczone za pomocą dyrektywy Cache. Sterowanie buforowaniem przy użyciu następujących parametrów:<ul><li>maks. wiek</li><li>Max-nieodświeżone&#8224;</li><li>min — nowy</li><li>musi ponownie sprawdzić poprawność</li><li>nie-pamięć podręczna</li><li>bez sklepu</li><li>tylko-w pamięci podręcznej</li><li>private</li><li>public</li><li>s-maxage</li><li>serwer proxy — ponowne sprawdzanie poprawności&#8225;</li></ul>&#8224;Jeśli żaden limit nie zostanie określony do `max-stale` , oprogramowanie pośredniczące nie podejmuje żadnych działań.<br>&#8225;`proxy-revalidate` ma ten sam skutek co `must-revalidate` .<br><br>Aby uzyskać więcej informacji, zobacz [RFC 7231: Request Cache-Control dyrektywy](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`Nagłówek w żądaniu daje ten sam skutek co `Cache-Control: no-cache` . Ten nagłówek jest zastępowany przez odpowiednie dyrektywy w `Cache-Control` nagłówku, jeśli jest obecny. Uwzględnianie zgodności z poprzednimi wersjami przy użyciu protokołu HTTP/1.0. |
| `Set-Cookie` | Odpowiedź nie jest buforowana, jeśli nagłówek istnieje. Wszystkie oprogramowanie pośredniczące w potoku przetwarzania żądań, które ustawia jeden lub więcej cookie s uniemożliwia buforowanie odpowiedzi przez oprogramowanie pośredniczące do buforowania odpowiedzi (na przykład [ cookie dostawcy TempData opartego na protokole](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary`Nagłówek jest używany do różnicowania buforowanej odpowiedzi przez inny nagłówek. Na przykład w pamięci podręcznej odpowiedzi są kodowane przez dołączenie `Vary: Accept-Encoding` nagłówka, w którym są buforowane odpowiedzi na żądania z nagłówkami `Accept-Encoding: gzip` i `Accept-Encoding: text/plain` oddzielnie. Odpowiedź z wartością nagłówka `*` nigdy nie jest przechowywana. |
| `Expires` | Odpowiedź uznana za nieodświeżoną przez ten nagłówek nie jest zapisywana ani pobierana, chyba że zostanie zastąpiona przez inne `Cache-Control` nagłówki. |
| `If-None-Match` | Pełna odpowiedź jest obsługiwana z pamięci podręcznej, jeśli wartość nie jest `*` , a `ETag` odpowiedź nie jest zgodna z żadną z podanych wartości. W przeciwnym razie jest obsługiwana odpowiedź 304 (nie modyfikowane). |
| `If-Modified-Since` | Jeśli `If-None-Match` nagłówek nie istnieje, jest obsługiwana Pełna odpowiedź z pamięci podręcznej, jeśli data odpowiedzi w pamięci podręcznej jest nowsza niż podana wartość. W przeciwnym razie jest obsługiwana odpowiedź *304 — nie zmodyfikowano* . |
| `Date` | W przypadku obsługi z pamięci podręcznej `Date` nagłówek jest ustawiany przez oprogramowanie pośredniczące, jeśli nie został podany w oryginalnej odpowiedzi. |
| `Content-Length` | W przypadku obsługi z pamięci podręcznej `Content-Length` nagłówek jest ustawiany przez oprogramowanie pośredniczące, jeśli nie został podany w oryginalnej odpowiedzi. |
| `Age` | `Age`Nagłówek wysłany w oryginalnej odpowiedzi jest ignorowany. Oprogramowanie pośredniczące oblicza nową wartość w przypadku obsługi odpowiedzi w pamięci podręcznej. |

## <a name="caching-respects-request-cache-control-directives"></a>Cache-Control dyrektywy z uwzględnieniem pamięci podręcznej

Oprogramowanie pośredniczące uwzględnia reguły [buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Reguły wymagają pamięci podręcznej, aby honorować prawidłowy `Cache-Control` nagłówek Wysłany przez klienta. W obszarze specyfikacji klient może wykonywać żądania z `no-cache` wartością nagłówka i wymusić, aby serwer generował nową odpowiedź dla każdego żądania. Obecnie nie ma kontroli nad tym zachowaniem buforowania podczas korzystania z oprogramowania pośredniczącego, ponieważ oprogramowanie pośredniczące jest zgodne z oficjalną specyfikacją buforowania.

Aby uzyskać większą kontrolę nad zachowaniem buforowania, zapoznaj się z innymi funkcjami buforowania ASP.NET Core. Zobacz następujące tematy:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zachowanie buforowania nie jest zgodne z oczekiwaniami, upewnij się, że odpowiedzi są buforowane i mogą być obsługiwane z pamięci podręcznej. Sprawdź przychodzące nagłówki żądania i wychodzące nagłówki odpowiedzi. Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby pomóc w debugowaniu.

Podczas testowania i rozwiązywania problemów z pamięcią podręczną przeglądarka może ustawić nagłówki żądań, które mają wpływ na buforowanie na różne sposoby. Na przykład przeglądarka może ustawić `Cache-Control` nagłówek na `no-cache` lub `max-age=0` podczas odświeżania strony. Poniższe narzędzia mogą jawnie ustawić nagłówki żądań i są preferowane w celu przeprowadzenia testu w pamięci podręcznej:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Warunki buforowania

* Żądanie musi spowodować odpowiedź serwera z kodem stanu 200 (OK).
* Metoda żądania musi mieć wartość GET lub $.
* W programie `Startup.Configure` oprogramowanie pośredniczące buforowania odpowiedzi należy umieścić przed oprogramowanie pośredniczące wymagające buforowania. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.
* `Authorization`Nagłówek nie może być obecny.
* `Cache-Control` parametry nagłówka muszą być prawidłowe, a odpowiedź musi być oznaczona jako `public` nieoznaczona `private` .
* `Pragma: no-cache`Nagłówek nie może być obecny `Cache-Control` , jeśli nagłówek nie istnieje, jako że `Cache-Control` nagłówek zastępuje nagłówek, `Pragma` gdy jest obecny.
* `Set-Cookie`Nagłówek nie może być obecny.
* `Vary` parametry nagłówka muszą być prawidłowe i nie mogą być równe `*` .
* `Content-Length`Wartość nagłówka (jeśli jest ustawiona) musi być zgodna z rozmiarem treści odpowiedzi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Nie jest używany.
* Odpowiedź nie może być nieodświeżona zgodnie z definicją `Expires` nagłówka i `max-age` `s-maxage` dyrektywy pamięci podręcznej.
* Buforowanie odpowiedzi musi się powieść. Rozmiar odpowiedzi musi być mniejszy niż skonfigurowany lub domyślny <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Rozmiar treści odpowiedzi musi być mniejszy niż skonfigurowany lub domyślny <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Odpowiedź musi być buforowana zgodnie ze specyfikacją [RFC 7234](https://tools.ietf.org/html/rfc7234) . Na przykład `no-store` dyrektywa nie może istnieć w polach żądania ani nagłówka odpowiedzi. Zobacz *sekcję 3: przechowywanie odpowiedzi w pamięci podręcznej* [RFC 7234](https://tools.ietf.org/html/rfc7234) , aby uzyskać szczegółowe informacje.

> [!NOTE]
> System antysfałszowany służący do generowania zabezpieczonych tokenów, aby zapobiec atakom przez wiele witryn (CSRF), w `Cache-Control` związku z czym `Pragma` `no-cache` odpowiedzi nie są buforowane. Aby uzyskać informacje na temat sposobu wyłączania tokenów antysfałszowanych dla elementów formularza HTML, zobacz <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end