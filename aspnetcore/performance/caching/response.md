---
title: Buforowanie odpowiedzi w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak korzystać z buforowania odpowiedzi, aby zmniejszyć wymagania dotyczące przepustowości i zwiększyć wydajność ASP.NET Core aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 2864de5b9931ed255569cb087c67c71004c4df92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059016"
---
# <a name="response-caching-in-aspnet-core"></a>Buforowanie odpowiedzi w ASP.NET Core

[John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Steve Smith](https://ardalis.com/)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Buforowanie odpowiedzi zmniejsza liczbę żądań wysyłanych przez klienta lub serwer proxy do serwera sieci Web. Buforowanie odpowiedzi zmniejsza również ilość pracy wykonywanej przez serwer sieci Web w celu wygenerowania odpowiedzi. Buforowanie odpowiedzi jest kontrolowane przez nagłówki, które określają sposób, w jaki klient, serwer proxy i oprogramowanie pośredniczące buforują odpowiedzi.

[Atrybut ResponseCache](#responsecache-attribute) uczestniczy w ustawianiu nagłówków buforowania odpowiedzi. Klienci i pośredniczące proxy powinny przestrzegać nagłówków do buforowania odpowiedzi w [specyfikacji buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234).

W przypadku buforowania po stronie serwera, które następuje zgodnie ze specyfikacją buforowania HTTP 1,1, należy użyć [oprogramowania pośredniczącego buforowania odpowiedzi](xref:performance/caching/middleware). Oprogramowanie pośredniczące może używać <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> właściwości, aby mieć wpływ na zachowanie buforowania po stronie serwera.

## <a name="http-based-response-caching"></a>Buforowanie odpowiedzi oparte na protokole HTTP

[Specyfikacja buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234) opisuje, jak należy zachować pamięć podręczną Internetu. Podstawowy nagłówek HTTP używany do buforowania to [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), który jest używany do określania *dyrektyw* pamięci podręcznej. Dyrektywy kontrolują zachowanie pamięci podręcznej w miarę jak żądania skierowane do klientów na serwery i jako odpowiedzi sprawiają, że serwery są z powrotem do klientów. Żądania i odpowiedzi przechodzą przez serwery proxy, a serwery proxy muszą również być zgodne ze specyfikacją buforowania HTTP 1,1.

Wspólne `Cache-Control` dyrektywy przedstawiono w poniższej tabeli.

| Dyrektywę                                                       | Akcja |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Pamięć podręczna może przechowywać odpowiedź. |
| [użytek](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Odpowiedź nie może być przechowywana w udostępnionej pamięci podręcznej. Prywatna pamięć podręczna może przechowywać i ponownie używać odpowiedzi. |
| [maks. wiek](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Klient nie akceptuje odpowiedzi, której wiek jest większy niż określona liczba sekund. Przykłady: `max-age=60` (60 sekund), `max-age=2592000` (1 miesiąc) |
| [nie-pamięć podręczna](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Na** żądanie: pamięć podręczna nie może używać zapisanej odpowiedzi w celu spełnienia żądania. Serwer pierwotny ponownie generuje odpowiedź dla klienta, a oprogramowanie pośredniczące aktualizuje zapisaną odpowiedź w jej pamięci podręcznej.<br><br>**W odpowiedzi** : nie można używać odpowiedzi dla kolejnych żądań bez sprawdzania poprawności na serwerze źródłowym. |
| [bez sklepu](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Żądania: pamięć** podręczna nie może przechowywać żądania.<br><br>**Odpowiedzi** : pamięć podręczna nie może przechowywać żadnej części odpowiedzi. |

W poniższej tabeli przedstawiono inne nagłówki pamięci podręcznej, które odgrywają rolę w buforowanie.

| Header                                                     | Funkcja |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | Oszacowanie czasu (w sekundach), po którym odpowiedź została wygenerowana lub pomyślnie zweryfikowana na serwerze pochodzenia. |
| [Wygasł](https://tools.ietf.org/html/rfc7234#section-5.3) | Czas, po którym odpowiedź jest uważana za przestarzałą. |
| [Pragm](https://tools.ietf.org/html/rfc7234#section-5.4)  | Istnieje w celu zachowania zgodności z poprzednimi wersjami z pamięcią podręczną protokołu HTTP/1.0 na potrzeby ustawienia `no-cache` zachowania. Jeśli `Cache-Control` nagłówek jest obecny, `Pragma` nagłówek jest ignorowany. |
| [Zmienia](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Określa, że nie można wysyłać buforowanej odpowiedzi, chyba że wszystkie `Vary` pola nagłówka są zgodne z oryginalnym żądaniem odpowiedzi w pamięci podręcznej i nowym żądaniu. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>W przypadku buforowania opartego na protokole HTTP Cache-Control dyrektywy

[Specyfikacja buforowania HTTP 1,1 dla nagłówka Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) wymaga pamięci podręcznej, aby honorować prawidłowy `Cache-Control` nagłówek Wysłany przez klienta. Klient może wykonywać żądania z `no-cache` wartością nagłówka i wymusić, aby serwer generował nową odpowiedź dla każdego żądania.

Zawsze `Cache-Control` , gdy nagłówki żądań klientów są brane pod uwagę, Jeśli rozważasz cel buforowania http. Zgodnie z oficjalną specyfikacją buforowanie jest przeznaczone do zmniejszenia opóźnień i obciążenia sieci w celu zaspokojenia żądań w sieci klientów, serwerów proxy i serwerów. Nie musi to być sposób sterowania obciążeniem na serwerze źródłowym.

Nie ma kontroli nad tym zachowaniem buforowania podczas korzystania z [oprogramowania pośredniczącego buforowania odpowiedzi](xref:performance/caching/middleware) , ponieważ oprogramowanie pośredniczące jest zgodne z oficjalną specyfikacją buforowania. [Planowane ulepszenia oprogramowania pośredniczącego](https://github.com/dotnet/AspNetCore/issues/2612) umożliwiają skonfigurowanie oprogramowania pośredniczącego do ignorowania `Cache-Control` nagłówka żądania podczas decydowania o konieczności przeprowadzenia buforowanej odpowiedzi. Planowane usprawnienia zapewniają możliwość lepszego sterowania obciążeniem serwera.

## <a name="other-caching-technology-in-aspnet-core"></a>Inna technologia buforowania w ASP.NET Core

### <a name="in-memory-caching"></a>Buforowanie w pamięci

Buforowanie w pamięci używa pamięci serwera do przechowywania buforowanych danych. Ten typ buforowania jest odpowiedni dla jednego serwera lub wielu serwerów używających *sesji programu Sticky Notes* . Sesje programu Sticky Notes oznaczają, że żądania od klienta są zawsze kierowane do tego samego serwera w celu przetworzenia.

Aby uzyskać więcej informacji, zobacz <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Rozproszona pamięć podręczna

Użyj rozproszonej pamięci podręcznej do przechowywania danych w pamięci, gdy aplikacja jest hostowana w chmurze lub w farmie serwerów. Pamięć podręczna jest współdzielona przez serwery, które przetwarzają żądania. Klient może przesłać żądanie, które jest obsługiwane przez dowolny serwer w grupie, jeśli dane przechowywane w pamięci podręcznej dla klienta są dostępne. ASP.NET Core współpracuje z rozproszonymi pamięciami podręcznymi SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)i [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Aby uzyskać więcej informacji, zobacz <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Pomocnik tagu pamięci podręcznej

Zawartość pamięci podręcznej ze widoku MVC lub Razor strony z pomocnikiem znacznika pamięci podręcznej. Pomocnik tagu pamięci podręcznej używa buforowania w pamięci do przechowywania danych.

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Pomocnik tagu rozproszonej pamięci podręcznej

Zawartość pamięci podręcznej ze widoku MVC lub ze Razor strony w scenariuszach rozproszonej chmury lub kolektywu serwerów sieci Web za pomocą pomocnika tagów rozproszonej pamięci podręcznej. Pomocnik tagów rozproszonej pamięci podręcznej używa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)lub [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) do przechowywania danych.

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>ResponseCache — atrybut

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Określa parametry niezbędne do ustawiania odpowiednich nagłówków w pamięci podręcznej odpowiedzi.

> [!WARNING]
> Wyłącz buforowanie zawartości zawierającej informacje dla uwierzytelnionych klientów. Buforowanie powinno być włączone tylko dla zawartości, która nie zmienia się na podstawie tożsamości użytkownika ani od tego, czy użytkownik jest zalogowany.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> zmienia zapisaną odpowiedź przez wartości podanej listy kluczy zapytań. Gdy podano jedną wartość `*` , oprogramowanie pośredniczące zmienia odpowiedzi przez wszystkie parametry ciągu zapytania żądania.

Aby ustawić właściwość, należy włączyć [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . W przeciwnym razie zostanie zgłoszony wyjątek czasu wykonywania. Brak odpowiadającego mu nagłówka HTTP dla <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> właściwości. Właściwość jest funkcją HTTP, która jest obsługiwana przez oprogramowanie pośredniczące buforowania odpowiedzi. Aby oprogramowanie pośredniczące obsługiwało buforowaną odpowiedź, ciąg zapytania i wartość ciągu zapytania muszą być zgodne z poprzednim żądaniem. Rozważmy na przykład sekwencję żądań i wyniki przedstawione w poniższej tabeli.

| Żądanie                          | Wynik                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Zwrócone z serwera. |
| `http://example.com?key1=value1` | Zwrócone z oprogramowania pośredniczącego. |
| `http://example.com?key1=value2` | Zwrócone z serwera. |

Pierwsze żądanie jest zwracane przez serwer i w pamięci podręcznej w oprogramowaniu pośredniczącym. Drugie żądanie jest zwracane przez oprogramowanie pośredniczące, ponieważ ciąg zapytania pasuje do poprzedniego żądania. Trzecie żądanie nie znajduje się w pamięci podręcznej pośredniczącej, ponieważ wartość ciągu zapytania nie pasuje do poprzedniego żądania.

Służy <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> do konfigurowania i tworzenia (za pośrednictwem <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` . `ResponseCacheFilter`Wykonuje zadanie aktualizowania odpowiednich nagłówków HTTP i funkcji odpowiedzi. Filtr:

* Usuwa wszystkie istniejące nagłówki dla `Vary` , `Cache-Control` i `Pragma` .
* Zapisuje odpowiednie nagłówki na podstawie właściwości ustawionych w <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .
* Aktualizuje funkcję HTTP buforowania odpowiedzi, jeśli <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> jest ustawiona.

### <a name="vary"></a>Zmienia

Ten nagłówek jest zapisywana tylko wtedy, gdy <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Właściwość jest ustawiona. Właściwość ustawiona na `Vary` wartość właściwości. Poniższy przykład używa <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Właściwości:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Za pomocą przykładowej aplikacji Wyświetl nagłówki odpowiedzi za pomocą narzędzi sieciowych przeglądarki. Następujące nagłówki odpowiedzi są wysyłane z odpowiedzią na stronę Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore i Location. None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> przesłania większość innych właściwości. Gdy ta właściwość jest ustawiona na `true` , `Cache-Control` nagłówek jest ustawiony na `no-store` . Jeśli <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> jest ustawiona na `None` :

* `Cache-Control` jest ustawiony na `no-store,no-cache` .
* `Pragma` jest ustawiony na `no-cache` .

Jeśli <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> jest `false` i <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ma `None` `Cache-Control` wartość, i `Pragma` są ustawione na `no-cache` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> jest zwykle ustawiony na `true` dla stron błędów. Strona Cache2 w przykładowej aplikacji generuje nagłówki odpowiedzi, które instruują klienta, aby nie przechowywał odpowiedzi.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Przykładowa aplikacja zwraca stronę Cache2 z następującymi nagłówkami:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Lokalizacja i czas trwania

Aby włączyć buforowanie, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> należy ustawić wartość dodatnią i <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> musi być `Any` (domyślna) lub `Client` . Struktura ustawia `Cache-Control` nagłówek na wartość lokalizacji, po której następuje `max-age` odpowiedź.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>Opcje `Any` i `Client` przetłumaczą `Cache-Control` odpowiednio wartości nagłówka `public` i `private` . Jak zostało to opisane w sekcji [NoStore and Location. None](#nostore-and-locationnone) , <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ustawienie `None` Ustawia zarówno, jak `Cache-Control` i `Pragma` nagłówki `no-cache` .

`Location.Any` ( `Cache-Control` ustawienie wartości `public` ) oznacza, że *klient lub dowolny pośredni serwer proxy* może buforować wartość, w tym [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware).

`Location.Client` ( `Cache-Control` ustawienie to `private` ) wskazuje, że *tylko klient* może buforować wartość. Żadna pośrednia pamięć podręczna powinna buforować wartość, w tym [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware).

Nagłówki kontroli pamięci podręcznej jedynie zapewniają wskazówki klientom i pośrednim serwerom proxy, kiedy i w jaki sposób należy buforować odpowiedzi. Nie ma gwarancji, że klienci i serwery proxy będą przestrzegać [specyfikacji buforowania HTTP 1,1](https://tools.ietf.org/html/rfc7234). [Oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/middleware) zawsze jest zgodne z regułami buforowania ustanowionymi przez specyfikację.

Poniższy przykład przedstawia model strony Cache3 z przykładowej aplikacji oraz nagłówki utworzone przez ustawienie <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> i opuszczające <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> wartość domyślną:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Przykładowa aplikacja zwraca stronę Cache3 z następującym nagłówkiem:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Profile pamięci podręcznej

Zamiast duplikowania ustawień pamięci podręcznej odpowiedzi w wielu atrybutach akcji kontrolera, profile pamięci podręcznej można skonfigurować jako opcje podczas konfigurowania MVC/ Razor Pages in `Startup.ConfigureServices` . Wartości Znalezione w profilu pamięci podręcznej, do którego istnieje odwołanie, są używane jako wartości domyślne przez <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> i są zastępowane przez wszystkie właściwości określone w atrybucie.

Skonfiguruj profil pamięci podręcznej. Poniższy przykład przedstawia 30-sekundowy profil pamięci podręcznej w przykładowej aplikacji `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

Model strony Cache4 aplikacji przykładowej odwołuje się do `Default30` profilu pamięci podręcznej:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Można zastosować do:

* Razor Strony: nie można zastosować atrybutów do metod obsługi.
* Kontrolery MVC.
* Metody akcji MVC: atrybuty poziomu metody zastępują ustawienia określone w atrybutach na poziomie klasy.

Otrzymany nagłówek zastosowany do odpowiedzi strony Cache4 przez `Default30` profil pamięci podręcznej:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przechowywanie odpowiedzi w pamięci podręcznej](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
