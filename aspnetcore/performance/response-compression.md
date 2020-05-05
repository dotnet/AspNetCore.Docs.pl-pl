---
title: Kompresja odpowiedzi w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o kompresji odpowiedzi i sposobach używania oprogramowania pośredniczącego kompresji odpowiedzi w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/response-compression
ms.openlocfilehash: 12a39ccfefdcaec6251a9804011aefde3bbae7b2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776672"
---
# <a name="response-compression-in-aspnet-core"></a>Kompresja odpowiedzi w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Przepustowość sieci jest ograniczonym zasobem. Zmniejszenie rozmiaru odpowiedzi zwykle zwiększa czas odpowiedzi aplikacji, często znacząco. Jednym ze sposobów zmniejszenia rozmiaru ładunku jest kompresowanie odpowiedzi aplikacji.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>Kiedy używać oprogramowania pośredniczącego kompresji odpowiedzi

Użyj technologii kompresji odpowiedzi opartej na serwerze w usługach IIS, Apache lub nginx. Wydajność oprogramowania pośredniczącego prawdopodobnie nie jest zgodna z tymi, które są używane w modułach serwera. Serwer [serwerowy http. sys](xref:fundamentals/servers/httpsys) i serwer [Kestrel](xref:fundamentals/servers/kestrel) nie oferują obecnie wbudowanej obsługi kompresji.

Użyj oprogramowania pośredniczącego kompresji odpowiedzi, gdy jesteś:

* Nie można użyć następujących technologii kompresji opartych na serwerze:
  * [Moduł kompresji dynamicznej usług IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Moduł mod_deflate Apache](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Kompresja i dekompresja Nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hosting bezpośrednio na:
  * [Serwer http. sys](xref:fundamentals/servers/httpsys) (dawniej nazywany webListener)
  * [Serwer Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Kompresja odpowiedzi

Zazwyczaj Każda odpowiedź nieskompresowana natywnie może korzystać z kompresji odpowiedzi. Odpowiedzi nienatywnie skompresowane zazwyczaj obejmują: CSS, JavaScript, HTML, XML i JSON. Nie należy kompresować natywnie skompresowanych zasobów, takich jak pliki PNG. W przypadku próby przeprowadzenia dalszej kompresji natywnie skompresowanej odpowiedzi wszystkie niewielkie dodatkowe zmniejszenie rozmiaru i czasu transmisji będą prawdopodobnie przesłonięte przez czas potrzebny do przetworzenia kompresji. Nie Kompresuj plików mniejszych niż około 150-1000 bajtów (w zależności od zawartości pliku i wydajności kompresji). Narzuty kompresowania małych plików może generować skompresowany plik większy niż plik nieskompresowany.

Gdy klient może przetwarzać skompresowaną zawartość, klient musi poinformować serwer o swoich możliwościach, wysyłając `Accept-Encoding` nagłówek z żądaniem. Gdy serwer wysyła skompresowaną zawartość, musi zawierać informacje w `Content-Encoding` nagłówku dotyczące sposobu kodowania skompresowanej odpowiedzi. Oznaczenia kodowania zawartości obsługiwane przez oprogramowanie pośredniczące przedstawiono w poniższej tabeli.

| `Accept-Encoding`wartości nagłówka | Obsługiwane oprogramowanie pośredniczące | Opis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Tak (domyślnie)        | [Format skompresowanych danych Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nie                   | [WKLĘŚNIĘCIE — skompresowany format danych](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nie                   | [Wydajna wymiana XML](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Tak                  | [Format pliku gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Tak                  | Identyfikator "bez kodowania": odpowiedź nie może być zaszyfrowana. |
| `pack200-gzip`                  | Nie                   | [Format transferu sieciowego dla archiwów języka Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Tak                  | Wszystkie dostępne kodowanie zawartości nie jest jawnie wymagane |

Aby uzyskać więcej informacji, zapoznaj się z [listą oficjalnych kodowania zawartości organizacji Iana](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Oprogramowanie pośredniczące umożliwia dodanie dodatkowych dostawców kompresji dla niestandardowych `Accept-Encoding` wartości nagłówków. Aby uzyskać więcej informacji, zobacz [niestandardowe dostawcy](#custom-providers) poniżej.

Oprogramowanie pośredniczące może resłużyć do rozważenia wartości jakości (qvalue `q`), gdy są wysyłane przez klienta w celu określenia priorytetów schematów kompresji. Aby uzyskać więcej informacji, zobacz [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algorytmy kompresji są uzależnione od kompromisu między szybkością kompresji i skuteczności kompresji. *Efektywność* w tym kontekście odnosi się do rozmiaru danych wyjściowych po kompresji. Najmniejszy rozmiar jest osiągany przez najbardziej *optymalną* kompresję.

W poniższej tabeli opisano nagłówki dotyczące żądania, wysyłania, buforowania i otrzymywania zawartości skompresowanej.

| Nagłówek             | Rola |
| ------------------ | ---- |
| `Accept-Encoding`  | Wysyłany z klienta do serwera w celu wskazania schematów kodowania zawartości akceptowalnych dla klienta. |
| `Content-Encoding` | Wysyłany z serwera do klienta, aby wskazać kodowanie zawartości ładunku. |
| `Content-Length`   | W `Content-Length` przypadku kompresowania następuje usunięcie nagłówka, ponieważ zawartość treści zmienia się podczas kompresowania odpowiedzi. |
| `Content-MD5`      | W `Content-MD5` przypadku kompresowania następuje usunięcie nagłówka, ponieważ zawartość treści została zmieniona i wartość skrótu nie jest już prawidłowa. |
| `Content-Type`     | Określa typ MIME zawartości. Należy określić dla każdej odpowiedzi `Content-Type`. Oprogramowanie pośredniczące sprawdza tę wartość, aby określić, czy odpowiedź powinna być skompresowana. Oprogramowanie pośredniczące określa zestaw [domyślnych typów MIME](#mime-types) , które mogą być kodowane, ale można zastąpić lub dodać typy MIME. |
| `Vary`             | W `Accept-Encoding` `Vary` przypadku wysłania przez serwer z wartością do klientów i serwerów proxy nagłówek wskazuje na klienta lub serwer proxy, który powinien buforować (Zróżnicuj) odpowiedzi na podstawie wartości `Accept-Encoding` nagłówka żądania. Wynik zwrócenia zawartości z `Vary: Accept-Encoding` nagłówkiem polega na tym, że skompresowane i nieskompresowane odpowiedzi są buforowane osobno. |

Poznaj funkcje oprogramowania pośredniczącego kompresji odpowiedzi z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). Przykład ilustruje:

* Kompresja odpowiedzi aplikacji przy użyciu strumienia gzip i niestandardowych dostawców kompresji.
* Jak dodać typ MIME do domyślnej listy typów MIME do kompresji.

## <a name="package"></a>Pakiet

Oprogramowanie pośredniczące kompresji odpowiedzi jest dostarczane przez pakiet [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , który jest niejawnie uwzględniony w aplikacjach ASP.NET Core.

## <a name="configuration"></a>Konfigurowanie

Poniższy kod pokazuje, jak włączyć oprogramowanie pośredniczące kompresji odpowiedzi dla domyślnych typów MIME i dostawców kompresji ([Brotli](#brotli-compression-provider) i [gzip](#gzip-compression-provider)):

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Uwagi:

* `app.UseResponseCompression`musi być wywoływana przed dowolnym oprogramowanie pośredniczące, które kompresuje odpowiedzi. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#middleware-order>.
* Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)lub [Poster](https://www.getpostman.com/) , aby ustawić nagłówek `Accept-Encoding` żądania i zbadać nagłówki, rozmiar i treść odpowiedzi.

Prześlij żądanie do przykładowej aplikacji bez `Accept-Encoding` nagłówka i zwróć uwagę na to, że odpowiedź jest nieskompresowana. Nagłówki `Content-Encoding` i `Vary` nie są obecne w odpowiedzi.

![Okno programu Fiddler przedstawiające wynik żądania bez nagłówka Accept-Encoding. Odpowiedź nie jest skompresowana.](response-compression/_static/request-uncompressed.png)

Prześlij żądanie do aplikacji przykładowej przy użyciu `Accept-Encoding: br` nagłówka (kompresji Brotli) i sprawdź, czy odpowiedź jest skompresowana. W `Content-Encoding` odpowiedzi `Vary` znajdują się nagłówki i.

![Okno programu Fiddler przedstawiające wynik żądania z nagłówkiem Accept-Encoding i wartością br. Nagłówki różnic i kodowania zawartości są dodawane do odpowiedzi. Odpowiedź jest skompresowana.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>Dostawcy

### <a name="brotli-compression-provider"></a>Dostawca kompresji Brotli

Użyj, <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> Aby skompresować odpowiedzi w [formacie skompresowanych danych Brotli](https://tools.ietf.org/html/rfc7932).

Jeśli żaden dostawca kompresji nie zostanie jawnie dodany do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Dostawca kompresji Brotli jest domyślnie dodawany do tablicy dostawców kompresji wraz z [dostawcą kompresji gzip](#gzip-compression-provider).
* Wartość domyślna kompresji to kompresja Brotli, gdy klient obsługuje format skompresowanych danych Brotli. Jeśli klient Brotli nie jest obsługiwany przez klienta, kompresja jest domyślnie ustawiona na gzip, gdy klient obsługuje kompresję gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Dostawca kompresji Brotli należy dodać, gdy wszyscy dostawcy kompresji są jawnie dodani:

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

Ustaw poziom kompresji za pomocą <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>. Dostawca kompresji Brotli domyślnie jest najszybszym poziomem kompresji ([CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel)), który może nie generować najbardziej wydajnej kompresji. Jeśli wymagana jest najbardziej wydajna kompresja, Skonfiguruj oprogramowanie pośredniczące na potrzeby optymalnej kompresji.

| Poziom kompresji | Opis |
| ----------------- | ----------- |
| [CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel) | Kompresja powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli wynikowe dane wyjściowe nie są optymalnie skompresowane. |
| [CompressionLevel. nocompression](xref:System.IO.Compression.CompressionLevel) | Nie należy wykonywać kompresji. |
| [CompressionLevel. Optymalna](xref:System.IO.Compression.CompressionLevel) | Odpowiedzi powinny być kompresowane optymalnie, nawet jeśli kompresja zajmuje więcej czasu. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a>Dostawca kompresji gzip

Użyj, <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> Aby skompresować odpowiedzi w [formacie pliku gzip](https://tools.ietf.org/html/rfc1952).

Jeśli żaden dostawca kompresji nie zostanie jawnie dodany do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Dostawca kompresji gzip jest domyślnie dodawany do tablicy dostawców kompresji wraz z [dostawcą kompresji Brotli](#brotli-compression-provider).
* Wartość domyślna kompresji to kompresja Brotli, gdy klient obsługuje format skompresowanych danych Brotli. Jeśli klient Brotli nie jest obsługiwany przez klienta, kompresja jest domyślnie ustawiona na gzip, gdy klient obsługuje kompresję gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Dostawca kompresji gzip należy dodać, gdy wszyscy dostawcy kompresji są jawnie dodani:

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

Ustaw poziom kompresji za pomocą <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. Dostawca kompresji gzip domyślnie jest najszybszym poziomem kompresji ([CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel)), który może nie generować najbardziej wydajnej kompresji. Jeśli wymagana jest najbardziej wydajna kompresja, Skonfiguruj oprogramowanie pośredniczące na potrzeby optymalnej kompresji.

| Poziom kompresji | Opis |
| ----------------- | ----------- |
| [CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel) | Kompresja powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli wynikowe dane wyjściowe nie są optymalnie skompresowane. |
| [CompressionLevel. nocompression](xref:System.IO.Compression.CompressionLevel) | Nie należy wykonywać kompresji. |
| [CompressionLevel. Optymalna](xref:System.IO.Compression.CompressionLevel) | Odpowiedzi powinny być kompresowane optymalnie, nawet jeśli kompresja zajmuje więcej czasu. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Dostawcy niestandardowi

Tworzenie niestandardowych implementacji kompresji przy <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>użyciu programu. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Reprezentuje kodowanie zawartości, które `ICompressionProvider` tworzy. Oprogramowanie pośredniczące używa tych informacji do wybrania dostawcy w oparciu o listę określoną w `Accept-Encoding` nagłówku żądania.

Za pomocą przykładowej aplikacji klient przesyła żądanie z `Accept-Encoding: mycustomcompression` nagłówkiem. Oprogramowanie pośredniczące używa niestandardowej implementacji kompresji i zwraca odpowiedź z `Content-Encoding: mycustomcompression` nagłówkiem. Aby Implementacja kompresji niestandardowej działała, klient musi być w stanie zdekompresować niestandardowe kodowanie.

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


Prześlij żądanie do przykładowej aplikacji z `Accept-Encoding: mycustomcompression` nagłówkiem i obserwuj nagłówki odpowiedzi. W `Vary` odpowiedzi `Content-Encoding` znajdują się nagłówki i. Treść odpowiedzi (niepokazywana) nie jest skompresowana przez przykład. Brak implementacji kompresji w `CustomCompressionProvider` klasie przykładowej. Jednak przykład pokazuje, gdzie należy zaimplementować taki algorytm kompresji.

![Okno programu Fiddler przedstawiające wynik żądania z nagłówkiem Accept-Encoding i wartością mycustomcompression. Nagłówki różnic i kodowania zawartości są dodawane do odpowiedzi.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME, typy

Oprogramowanie pośredniczące określa domyślny zestaw typów MIME dla kompresji:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Zastąp lub Dołącz typy MIME z opcjami oprogramowania pośredniczącego kompresji odpowiedzi. Należy zauważyć, że symbole wieloznaczne MIME `text/*` , takie jak nie są obsługiwane. Przykładowa aplikacja dodaje typ MIME dla `image/svg+xml` i kompresuje i obsługuje obraz transparentu ASP.NET Core (*transparent. SVG*).

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>Kompresja z bezpiecznym protokołem

Skompresowane odpowiedzi za pośrednictwem bezpiecznych połączeń można kontrolować przy `EnableForHttps` użyciu opcji, która jest domyślnie wyłączona. Używanie kompresji z dynamicznie generowanymi stronami może prowadzić do problemów z zabezpieczeniami, takich jak ataki w ramach [przestępczości](https://wikipedia.org/wiki/CRIME_(security_exploit)) i [naruszeń](https://wikipedia.org/wiki/BREACH_(security_exploit)) .

## <a name="adding-the-vary-header"></a>Dodawanie nagłówka Vary

Podczas kompresowania odpowiedzi na podstawie `Accept-Encoding` nagłówka istnieje potencjalnie wiele skompresowanych wersji odpowiedzi i nieskompresowanej wersji. Aby wymusić, że w pamięci podręcznej klienta i serwera proxy istnieją różne wersje i `Vary` powinny być przechowywane, nagłówek `Accept-Encoding` zostanie dodany z wartością. W ASP.NET Core 2,0 lub nowszej, oprogramowanie pośredniczące automatycznie dodaje `Vary` nagłówek podczas kompresowania odpowiedzi.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problem dotyczący oprogramowania pośredniczącego w przypadku Nginx zwrotnego serwera proxy

Gdy żądanie jest przekazywane przez Nginx, `Accept-Encoding` nagłówek zostaje usunięty. Usunięcie `Accept-Encoding` nagłówka zapobiega kompresji odpowiedzi przez oprogramowanie pośredniczące. Aby uzyskać więcej informacji, zobacz [Nginx: kompresja i dekompresja](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Ten problem jest śledzony przez [ilustrację kompresji przekazującej dla Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Praca z kompresją dynamiczną usług IIS

Jeśli masz aktywny moduł dynamicznej kompresji usług IIS skonfigurowany na poziomie serwera, który chcesz wyłączyć dla aplikacji, wyłącz moduł z dodatkiem do pliku *Web. config* . Aby uzyskać więcej informacji, zobacz [wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)lub [Poster](https://www.getpostman.com/), które pozwala ustawić nagłówek `Accept-Encoding` żądania i zbadać nagłówki, rozmiar i treść odpowiedzi. Domyślnie oprogramowanie pośredniczące kompresji odpowiedzi kompresuje odpowiedzi, które spełniają następujące warunki:

* `Accept-Encoding` Nagłówek jest obecny z wartością `br`, `gzip`, `*`lub kodowaniem niestandardowym, które jest zgodne z ustanowionym przez Ciebie niestandardowym dostawcą kompresji. Wartość nie może być `identity` lub mieć ustawienie wartości jakości (qvalue, `q`) równe 0 (zero).
* Typ MIME (`Content-Type`) musi być ustawiony i musi być zgodny z typem MIME skonfigurowanym na <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Żądanie nie może zawierać `Content-Range` nagłówka.
* Żądanie musi korzystać z protokołu niezabezpieczonego (http), o ile nie skonfigurowano protokołu Secure Protocol (https) w opcjach oprogramowania pośredniczącego kompresji odpowiedzi. *Należy pamiętać o niebezpieczeństwie [opisanym powyżej](#compression-with-secure-protocol) podczas włączania bezpiecznej kompresji zawartości.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Sieć deweloperów Mozilla: akceptowanie kodowania](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Sekcja RFC 7231 3.1.2.1: kodowanie zawartości](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230, sekcja 4.2.3: kodowanie gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Specyfikacja formatu pliku GZIP w wersji 4,3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Przepustowość sieci jest ograniczonym zasobem. Zmniejszenie rozmiaru odpowiedzi zwykle zwiększa czas odpowiedzi aplikacji, często znacząco. Jednym ze sposobów zmniejszenia rozmiaru ładunku jest kompresowanie odpowiedzi aplikacji.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>Kiedy używać oprogramowania pośredniczącego kompresji odpowiedzi

Użyj technologii kompresji odpowiedzi opartej na serwerze w usługach IIS, Apache lub nginx. Wydajność oprogramowania pośredniczącego prawdopodobnie nie jest zgodna z tymi, które są używane w modułach serwera. Serwer [serwerowy http. sys](xref:fundamentals/servers/httpsys) i serwer [Kestrel](xref:fundamentals/servers/kestrel) nie oferują obecnie wbudowanej obsługi kompresji.

Użyj oprogramowania pośredniczącego kompresji odpowiedzi, gdy jesteś:

* Nie można użyć następujących technologii kompresji opartych na serwerze:
  * [Moduł kompresji dynamicznej usług IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Moduł mod_deflate Apache](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Kompresja i dekompresja Nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hosting bezpośrednio na:
  * [Serwer http. sys](xref:fundamentals/servers/httpsys) (dawniej nazywany webListener)
  * [Serwer Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Kompresja odpowiedzi

Zazwyczaj Każda odpowiedź nieskompresowana natywnie może korzystać z kompresji odpowiedzi. Odpowiedzi nienatywnie skompresowane zazwyczaj obejmują: CSS, JavaScript, HTML, XML i JSON. Nie należy kompresować natywnie skompresowanych zasobów, takich jak pliki PNG. W przypadku próby przeprowadzenia dalszej kompresji natywnie skompresowanej odpowiedzi wszystkie niewielkie dodatkowe zmniejszenie rozmiaru i czasu transmisji będą prawdopodobnie przesłonięte przez czas potrzebny do przetworzenia kompresji. Nie Kompresuj plików mniejszych niż około 150-1000 bajtów (w zależności od zawartości pliku i wydajności kompresji). Narzuty kompresowania małych plików może generować skompresowany plik większy niż plik nieskompresowany.

Gdy klient może przetwarzać skompresowaną zawartość, klient musi poinformować serwer o swoich możliwościach, wysyłając `Accept-Encoding` nagłówek z żądaniem. Gdy serwer wysyła skompresowaną zawartość, musi zawierać informacje w `Content-Encoding` nagłówku dotyczące sposobu kodowania skompresowanej odpowiedzi. Oznaczenia kodowania zawartości obsługiwane przez oprogramowanie pośredniczące przedstawiono w poniższej tabeli.

| `Accept-Encoding`wartości nagłówka | Obsługiwane oprogramowanie pośredniczące | Opis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Tak (domyślnie)        | [Format skompresowanych danych Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nie                   | [WKLĘŚNIĘCIE — skompresowany format danych](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nie                   | [Wydajna wymiana XML](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Tak                  | [Format pliku gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Tak                  | Identyfikator "bez kodowania": odpowiedź nie może być zaszyfrowana. |
| `pack200-gzip`                  | Nie                   | [Format transferu sieciowego dla archiwów języka Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Tak                  | Wszystkie dostępne kodowanie zawartości nie jest jawnie wymagane |

Aby uzyskać więcej informacji, zapoznaj się z [listą oficjalnych kodowania zawartości organizacji Iana](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Oprogramowanie pośredniczące umożliwia dodanie dodatkowych dostawców kompresji dla niestandardowych `Accept-Encoding` wartości nagłówków. Aby uzyskać więcej informacji, zobacz [niestandardowe dostawcy](#custom-providers) poniżej.

Oprogramowanie pośredniczące może resłużyć do rozważenia wartości jakości (qvalue `q`), gdy są wysyłane przez klienta w celu określenia priorytetów schematów kompresji. Aby uzyskać więcej informacji, zobacz [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algorytmy kompresji są uzależnione od kompromisu między szybkością kompresji i skuteczności kompresji. *Efektywność* w tym kontekście odnosi się do rozmiaru danych wyjściowych po kompresji. Najmniejszy rozmiar jest osiągany przez najbardziej *optymalną* kompresję.

W poniższej tabeli opisano nagłówki dotyczące żądania, wysyłania, buforowania i otrzymywania zawartości skompresowanej.

| Nagłówek             | Rola |
| ------------------ | ---- |
| `Accept-Encoding`  | Wysyłany z klienta do serwera w celu wskazania schematów kodowania zawartości akceptowalnych dla klienta. |
| `Content-Encoding` | Wysyłany z serwera do klienta, aby wskazać kodowanie zawartości ładunku. |
| `Content-Length`   | W `Content-Length` przypadku kompresowania następuje usunięcie nagłówka, ponieważ zawartość treści zmienia się podczas kompresowania odpowiedzi. |
| `Content-MD5`      | W `Content-MD5` przypadku kompresowania następuje usunięcie nagłówka, ponieważ zawartość treści została zmieniona i wartość skrótu nie jest już prawidłowa. |
| `Content-Type`     | Określa typ MIME zawartości. Należy określić dla każdej odpowiedzi `Content-Type`. Oprogramowanie pośredniczące sprawdza tę wartość, aby określić, czy odpowiedź powinna być skompresowana. Oprogramowanie pośredniczące określa zestaw [domyślnych typów MIME](#mime-types) , które mogą być kodowane, ale można zastąpić lub dodać typy MIME. |
| `Vary`             | W `Accept-Encoding` `Vary` przypadku wysłania przez serwer z wartością do klientów i serwerów proxy nagłówek wskazuje na klienta lub serwer proxy, który powinien buforować (Zróżnicuj) odpowiedzi na podstawie wartości `Accept-Encoding` nagłówka żądania. Wynik zwrócenia zawartości z `Vary: Accept-Encoding` nagłówkiem polega na tym, że skompresowane i nieskompresowane odpowiedzi są buforowane osobno. |

Poznaj funkcje oprogramowania pośredniczącego kompresji odpowiedzi z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). Przykład ilustruje:

* Kompresja odpowiedzi aplikacji przy użyciu strumienia gzip i niestandardowych dostawców kompresji.
* Jak dodać typ MIME do domyślnej listy typów MIME do kompresji.

## <a name="package"></a>Pakiet

Aby uwzględnić oprogramowanie pośredniczące w projekcie, Dodaj odwołanie do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), który obejmuje pakiet [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .

## <a name="configuration"></a>Konfigurowanie

Poniższy kod pokazuje, jak włączyć oprogramowanie pośredniczące kompresji odpowiedzi dla domyślnych typów MIME i dostawców kompresji ([Brotli](#brotli-compression-provider) i [gzip](#gzip-compression-provider)):

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Uwagi:

* `app.UseResponseCompression`musi być wywoływana przed dowolnym oprogramowanie pośredniczące, które kompresuje odpowiedzi. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#middleware-order>.
* Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)lub [Poster](https://www.getpostman.com/) , aby ustawić nagłówek `Accept-Encoding` żądania i zbadać nagłówki, rozmiar i treść odpowiedzi.

Prześlij żądanie do przykładowej aplikacji bez `Accept-Encoding` nagłówka i zwróć uwagę na to, że odpowiedź jest nieskompresowana. Nagłówki `Content-Encoding` i `Vary` nie są obecne w odpowiedzi.

![Okno programu Fiddler przedstawiające wynik żądania bez nagłówka Accept-Encoding. Odpowiedź nie jest skompresowana.](response-compression/_static/request-uncompressed.png)

Prześlij żądanie do aplikacji przykładowej przy użyciu `Accept-Encoding: br` nagłówka (kompresji Brotli) i sprawdź, czy odpowiedź jest skompresowana. W `Content-Encoding` odpowiedzi `Vary` znajdują się nagłówki i.

![Okno programu Fiddler przedstawiające wynik żądania z nagłówkiem Accept-Encoding i wartością br. Nagłówki różnic i kodowania zawartości są dodawane do odpowiedzi. Odpowiedź jest skompresowana.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>Dostawcy

### <a name="brotli-compression-provider"></a>Dostawca kompresji Brotli

Użyj, <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> Aby skompresować odpowiedzi w [formacie skompresowanych danych Brotli](https://tools.ietf.org/html/rfc7932).

Jeśli żaden dostawca kompresji nie zostanie jawnie dodany do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Dostawca kompresji Brotli jest domyślnie dodawany do tablicy dostawców kompresji wraz z [dostawcą kompresji gzip](#gzip-compression-provider).
* Wartość domyślna kompresji to kompresja Brotli, gdy klient obsługuje format skompresowanych danych Brotli. Jeśli klient Brotli nie jest obsługiwany przez klienta, kompresja jest domyślnie ustawiona na gzip, gdy klient obsługuje kompresję gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Dostawca kompresji Brotli należy dodać, gdy wszyscy dostawcy kompresji są jawnie dodani:

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

Ustaw poziom kompresji za pomocą <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>. Dostawca kompresji Brotli domyślnie jest najszybszym poziomem kompresji ([CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel)), który może nie generować najbardziej wydajnej kompresji. Jeśli wymagana jest najbardziej wydajna kompresja, Skonfiguruj oprogramowanie pośredniczące na potrzeby optymalnej kompresji.

| Poziom kompresji | Opis |
| ----------------- | ----------- |
| [CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel) | Kompresja powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli wynikowe dane wyjściowe nie są optymalnie skompresowane. |
| [CompressionLevel. nocompression](xref:System.IO.Compression.CompressionLevel) | Nie należy wykonywać kompresji. |
| [CompressionLevel. Optymalna](xref:System.IO.Compression.CompressionLevel) | Odpowiedzi powinny być kompresowane optymalnie, nawet jeśli kompresja zajmuje więcej czasu. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a>Dostawca kompresji gzip

Użyj, <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> Aby skompresować odpowiedzi w [formacie pliku gzip](https://tools.ietf.org/html/rfc1952).

Jeśli żaden dostawca kompresji nie zostanie jawnie dodany do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Dostawca kompresji gzip jest domyślnie dodawany do tablicy dostawców kompresji wraz z [dostawcą kompresji Brotli](#brotli-compression-provider).
* Wartość domyślna kompresji to kompresja Brotli, gdy klient obsługuje format skompresowanych danych Brotli. Jeśli klient Brotli nie jest obsługiwany przez klienta, kompresja jest domyślnie ustawiona na gzip, gdy klient obsługuje kompresję gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Dostawca kompresji gzip należy dodać, gdy wszyscy dostawcy kompresji są jawnie dodani:

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

Ustaw poziom kompresji za pomocą <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. Dostawca kompresji gzip domyślnie jest najszybszym poziomem kompresji ([CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel)), który może nie generować najbardziej wydajnej kompresji. Jeśli wymagana jest najbardziej wydajna kompresja, Skonfiguruj oprogramowanie pośredniczące na potrzeby optymalnej kompresji.

| Poziom kompresji | Opis |
| ----------------- | ----------- |
| [CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel) | Kompresja powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli wynikowe dane wyjściowe nie są optymalnie skompresowane. |
| [CompressionLevel. nocompression](xref:System.IO.Compression.CompressionLevel) | Nie należy wykonywać kompresji. |
| [CompressionLevel. Optymalna](xref:System.IO.Compression.CompressionLevel) | Odpowiedzi powinny być kompresowane optymalnie, nawet jeśli kompresja zajmuje więcej czasu. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Dostawcy niestandardowi

Tworzenie niestandardowych implementacji kompresji przy <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>użyciu programu. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Reprezentuje kodowanie zawartości, które `ICompressionProvider` tworzy. Oprogramowanie pośredniczące używa tych informacji do wybrania dostawcy w oparciu o listę określoną w `Accept-Encoding` nagłówku żądania.

Za pomocą przykładowej aplikacji klient przesyła żądanie z `Accept-Encoding: mycustomcompression` nagłówkiem. Oprogramowanie pośredniczące używa niestandardowej implementacji kompresji i zwraca odpowiedź z `Content-Encoding: mycustomcompression` nagłówkiem. Aby Implementacja kompresji niestandardowej działała, klient musi być w stanie zdekompresować niestandardowe kodowanie.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

Prześlij żądanie do przykładowej aplikacji z `Accept-Encoding: mycustomcompression` nagłówkiem i obserwuj nagłówki odpowiedzi. W `Vary` odpowiedzi `Content-Encoding` znajdują się nagłówki i. Treść odpowiedzi (niepokazywana) nie jest skompresowana przez przykład. Brak implementacji kompresji w `CustomCompressionProvider` klasie przykładowej. Jednak przykład pokazuje, gdzie należy zaimplementować taki algorytm kompresji.

![Okno programu Fiddler przedstawiające wynik żądania z nagłówkiem Accept-Encoding i wartością mycustomcompression. Nagłówki różnic i kodowania zawartości są dodawane do odpowiedzi.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME, typy

Oprogramowanie pośredniczące określa domyślny zestaw typów MIME dla kompresji:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Zastąp lub Dołącz typy MIME z opcjami oprogramowania pośredniczącego kompresji odpowiedzi. Należy zauważyć, że symbole wieloznaczne MIME `text/*` , takie jak nie są obsługiwane. Przykładowa aplikacja dodaje typ MIME dla `image/svg+xml` i kompresuje i obsługuje obraz transparentu ASP.NET Core (*transparent. SVG*).

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>Kompresja z bezpiecznym protokołem

Skompresowane odpowiedzi za pośrednictwem bezpiecznych połączeń można kontrolować przy `EnableForHttps` użyciu opcji, która jest domyślnie wyłączona. Używanie kompresji z dynamicznie generowanymi stronami może prowadzić do problemów z zabezpieczeniami, takich jak ataki w ramach [przestępczości](https://wikipedia.org/wiki/CRIME_(security_exploit)) i [naruszeń](https://wikipedia.org/wiki/BREACH_(security_exploit)) .

## <a name="adding-the-vary-header"></a>Dodawanie nagłówka Vary

Podczas kompresowania odpowiedzi na podstawie `Accept-Encoding` nagłówka istnieje potencjalnie wiele skompresowanych wersji odpowiedzi i nieskompresowanej wersji. Aby wymusić, że w pamięci podręcznej klienta i serwera proxy istnieją różne wersje i `Vary` powinny być przechowywane, nagłówek `Accept-Encoding` zostanie dodany z wartością. W ASP.NET Core 2,0 lub nowszej, oprogramowanie pośredniczące automatycznie dodaje `Vary` nagłówek podczas kompresowania odpowiedzi.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problem dotyczący oprogramowania pośredniczącego w przypadku Nginx zwrotnego serwera proxy

Gdy żądanie jest przekazywane przez Nginx, `Accept-Encoding` nagłówek zostaje usunięty. Usunięcie `Accept-Encoding` nagłówka zapobiega kompresji odpowiedzi przez oprogramowanie pośredniczące. Aby uzyskać więcej informacji, zobacz [Nginx: kompresja i dekompresja](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Ten problem jest śledzony przez [ilustrację kompresji przekazującej dla Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Praca z kompresją dynamiczną usług IIS

Jeśli masz aktywny moduł dynamicznej kompresji usług IIS skonfigurowany na poziomie serwera, który chcesz wyłączyć dla aplikacji, wyłącz moduł z dodatkiem do pliku *Web. config* . Aby uzyskać więcej informacji, zobacz [wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)lub [Poster](https://www.getpostman.com/), które pozwala ustawić nagłówek `Accept-Encoding` żądania i zbadać nagłówki, rozmiar i treść odpowiedzi. Domyślnie oprogramowanie pośredniczące kompresji odpowiedzi kompresuje odpowiedzi, które spełniają następujące warunki:

* `Accept-Encoding` Nagłówek jest obecny z wartością `br`, `gzip`, `*`lub kodowaniem niestandardowym, które jest zgodne z ustanowionym przez Ciebie niestandardowym dostawcą kompresji. Wartość nie może być `identity` lub mieć ustawienie wartości jakości (qvalue, `q`) równe 0 (zero).
* Typ MIME (`Content-Type`) musi być ustawiony i musi być zgodny z typem MIME skonfigurowanym na <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Żądanie nie może zawierać `Content-Range` nagłówka.
* Żądanie musi korzystać z protokołu niezabezpieczonego (http), o ile nie skonfigurowano protokołu Secure Protocol (https) w opcjach oprogramowania pośredniczącego kompresji odpowiedzi. *Należy pamiętać o niebezpieczeństwie [opisanym powyżej](#compression-with-secure-protocol) podczas włączania bezpiecznej kompresji zawartości.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Sieć deweloperów Mozilla: akceptowanie kodowania](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Sekcja RFC 7231 3.1.2.1: kodowanie zawartości](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230, sekcja 4.2.3: kodowanie gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Specyfikacja formatu pliku GZIP w wersji 4,3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Przepustowość sieci jest ograniczonym zasobem. Zmniejszenie rozmiaru odpowiedzi zwykle zwiększa czas odpowiedzi aplikacji, często znacząco. Jednym ze sposobów zmniejszenia rozmiaru ładunku jest kompresowanie odpowiedzi aplikacji.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>Kiedy używać oprogramowania pośredniczącego kompresji odpowiedzi

Użyj technologii kompresji odpowiedzi opartej na serwerze w usługach IIS, Apache lub nginx. Wydajność oprogramowania pośredniczącego prawdopodobnie nie jest zgodna z tymi, które są używane w modułach serwera. Serwer [serwerowy http. sys](xref:fundamentals/servers/httpsys) i serwer [Kestrel](xref:fundamentals/servers/kestrel) nie oferują obecnie wbudowanej obsługi kompresji.

Użyj oprogramowania pośredniczącego kompresji odpowiedzi, gdy jesteś:

* Nie można użyć następujących technologii kompresji opartych na serwerze:
  * [Moduł kompresji dynamicznej usług IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Moduł mod_deflate Apache](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Kompresja i dekompresja Nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hosting bezpośrednio na:
  * [Serwer http. sys](xref:fundamentals/servers/httpsys) (dawniej nazywany webListener)
  * [Serwer Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Kompresja odpowiedzi

Zazwyczaj Każda odpowiedź nieskompresowana natywnie może korzystać z kompresji odpowiedzi. Odpowiedzi nienatywnie skompresowane zazwyczaj obejmują: CSS, JavaScript, HTML, XML i JSON. Nie należy kompresować natywnie skompresowanych zasobów, takich jak pliki PNG. W przypadku próby przeprowadzenia dalszej kompresji natywnie skompresowanej odpowiedzi wszystkie niewielkie dodatkowe zmniejszenie rozmiaru i czasu transmisji będą prawdopodobnie przesłonięte przez czas potrzebny do przetworzenia kompresji. Nie Kompresuj plików mniejszych niż około 150-1000 bajtów (w zależności od zawartości pliku i wydajności kompresji). Narzuty kompresowania małych plików może generować skompresowany plik większy niż plik nieskompresowany.

Gdy klient może przetwarzać skompresowaną zawartość, klient musi poinformować serwer o swoich możliwościach, wysyłając `Accept-Encoding` nagłówek z żądaniem. Gdy serwer wysyła skompresowaną zawartość, musi zawierać informacje w `Content-Encoding` nagłówku dotyczące sposobu kodowania skompresowanej odpowiedzi. Oznaczenia kodowania zawartości obsługiwane przez oprogramowanie pośredniczące przedstawiono w poniższej tabeli.

| `Accept-Encoding`wartości nagłówka | Obsługiwane oprogramowanie pośredniczące | Opis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Nie                   | [Format skompresowanych danych Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nie                   | [WKLĘŚNIĘCIE — skompresowany format danych](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nie                   | [Wydajna wymiana XML](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Tak (domyślnie)        | [Format pliku gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Tak                  | Identyfikator "bez kodowania": odpowiedź nie może być zaszyfrowana. |
| `pack200-gzip`                  | Nie                   | [Format transferu sieciowego dla archiwów języka Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Tak                  | Wszystkie dostępne kodowanie zawartości nie jest jawnie wymagane |

Aby uzyskać więcej informacji, zapoznaj się z [listą oficjalnych kodowania zawartości organizacji Iana](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Oprogramowanie pośredniczące umożliwia dodanie dodatkowych dostawców kompresji dla niestandardowych `Accept-Encoding` wartości nagłówków. Aby uzyskać więcej informacji, zobacz [niestandardowe dostawcy](#custom-providers) poniżej.

Oprogramowanie pośredniczące może resłużyć do rozważenia wartości jakości (qvalue `q`), gdy są wysyłane przez klienta w celu określenia priorytetów schematów kompresji. Aby uzyskać więcej informacji, zobacz [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algorytmy kompresji są uzależnione od kompromisu między szybkością kompresji i skuteczności kompresji. *Efektywność* w tym kontekście odnosi się do rozmiaru danych wyjściowych po kompresji. Najmniejszy rozmiar jest osiągany przez najbardziej *optymalną* kompresję.

W poniższej tabeli opisano nagłówki dotyczące żądania, wysyłania, buforowania i otrzymywania zawartości skompresowanej.

| Nagłówek             | Rola |
| ------------------ | ---- |
| `Accept-Encoding`  | Wysyłany z klienta do serwera w celu wskazania schematów kodowania zawartości akceptowalnych dla klienta. |
| `Content-Encoding` | Wysyłany z serwera do klienta, aby wskazać kodowanie zawartości ładunku. |
| `Content-Length`   | W `Content-Length` przypadku kompresowania następuje usunięcie nagłówka, ponieważ zawartość treści zmienia się podczas kompresowania odpowiedzi. |
| `Content-MD5`      | W `Content-MD5` przypadku kompresowania następuje usunięcie nagłówka, ponieważ zawartość treści została zmieniona i wartość skrótu nie jest już prawidłowa. |
| `Content-Type`     | Określa typ MIME zawartości. Należy określić dla każdej odpowiedzi `Content-Type`. Oprogramowanie pośredniczące sprawdza tę wartość, aby określić, czy odpowiedź powinna być skompresowana. Oprogramowanie pośredniczące określa zestaw [domyślnych typów MIME](#mime-types) , które mogą być kodowane, ale można zastąpić lub dodać typy MIME. |
| `Vary`             | W `Accept-Encoding` `Vary` przypadku wysłania przez serwer z wartością do klientów i serwerów proxy nagłówek wskazuje na klienta lub serwer proxy, który powinien buforować (Zróżnicuj) odpowiedzi na podstawie wartości `Accept-Encoding` nagłówka żądania. Wynik zwrócenia zawartości z `Vary: Accept-Encoding` nagłówkiem polega na tym, że skompresowane i nieskompresowane odpowiedzi są buforowane osobno. |

Poznaj funkcje oprogramowania pośredniczącego kompresji odpowiedzi z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). Przykład ilustruje:

* Kompresja odpowiedzi aplikacji przy użyciu strumienia gzip i niestandardowych dostawców kompresji.
* Jak dodać typ MIME do domyślnej listy typów MIME do kompresji.

## <a name="package"></a>Pakiet

Aby uwzględnić oprogramowanie pośredniczące w projekcie, Dodaj odwołanie do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), który obejmuje pakiet [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .

## <a name="configuration"></a>Konfigurowanie

Poniższy kod pokazuje, jak włączyć oprogramowanie pośredniczące kompresji odpowiedzi dla domyślnych typów MIME i [dostawcy kompresji gzip](#gzip-compression-provider):

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Uwagi:

* `app.UseResponseCompression`musi być wywoływana przed dowolnym oprogramowanie pośredniczące, które kompresuje odpowiedzi. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#middleware-order>.
* Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)lub [Poster](https://www.getpostman.com/) , aby ustawić nagłówek `Accept-Encoding` żądania i zbadać nagłówki, rozmiar i treść odpowiedzi.

Prześlij żądanie do przykładowej aplikacji bez `Accept-Encoding` nagłówka i zwróć uwagę na to, że odpowiedź jest nieskompresowana. Nagłówki `Content-Encoding` i `Vary` nie są obecne w odpowiedzi.

![Okno programu Fiddler przedstawiające wynik żądania bez nagłówka Accept-Encoding. Odpowiedź nie jest skompresowana.](response-compression/_static/request-uncompressed.png)

Prześlij żądanie do przykładowej aplikacji z `Accept-Encoding: gzip` nagłówkiem i obserwuj, że odpowiedź jest skompresowana. W `Content-Encoding` odpowiedzi `Vary` znajdują się nagłówki i.

![Okno programu Fiddler przedstawiające wynik żądania z nagłówkiem Accept-Encoding i wartością gzip. Nagłówki różnic i kodowania zawartości są dodawane do odpowiedzi. Odpowiedź jest skompresowana.](response-compression/_static/request-compressed.png)

## <a name="providers"></a>Dostawcy

### <a name="gzip-compression-provider"></a>Dostawca kompresji gzip

Użyj, <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> Aby skompresować odpowiedzi w [formacie pliku gzip](https://tools.ietf.org/html/rfc1952).

Jeśli żaden dostawca kompresji nie zostanie jawnie dodany do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Dostawca kompresji gzip jest domyślnie dodawany do tablicy dostawców kompresji.
* Wartość domyślna kompresji to gzip, gdy klient obsługuje kompresję gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Dostawca kompresji gzip należy dodać, gdy wszyscy dostawcy kompresji są jawnie dodani:

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

Ustaw poziom kompresji za pomocą <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. Dostawca kompresji gzip domyślnie jest najszybszym poziomem kompresji ([CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel)), który może nie generować najbardziej wydajnej kompresji. Jeśli wymagana jest najbardziej wydajna kompresja, Skonfiguruj oprogramowanie pośredniczące na potrzeby optymalnej kompresji.

| Poziom kompresji | Opis |
| ----------------- | ----------- |
| [CompressionLevel. najszybszy](xref:System.IO.Compression.CompressionLevel) | Kompresja powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli wynikowe dane wyjściowe nie są optymalnie skompresowane. |
| [CompressionLevel. nocompression](xref:System.IO.Compression.CompressionLevel) | Nie należy wykonywać kompresji. |
| [CompressionLevel. Optymalna](xref:System.IO.Compression.CompressionLevel) | Odpowiedzi powinny być kompresowane optymalnie, nawet jeśli kompresja zajmuje więcej czasu. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Dostawcy niestandardowi

Tworzenie niestandardowych implementacji kompresji przy <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>użyciu programu. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Reprezentuje kodowanie zawartości, które `ICompressionProvider` tworzy. Oprogramowanie pośredniczące używa tych informacji do wybrania dostawcy w oparciu o listę określoną w `Accept-Encoding` nagłówku żądania.

Za pomocą przykładowej aplikacji klient przesyła żądanie z `Accept-Encoding: mycustomcompression` nagłówkiem. Oprogramowanie pośredniczące używa niestandardowej implementacji kompresji i zwraca odpowiedź z `Content-Encoding: mycustomcompression` nagłówkiem. Aby Implementacja kompresji niestandardowej działała, klient musi być w stanie zdekompresować niestandardowe kodowanie.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

Prześlij żądanie do przykładowej aplikacji z `Accept-Encoding: mycustomcompression` nagłówkiem i obserwuj nagłówki odpowiedzi. W `Vary` odpowiedzi `Content-Encoding` znajdują się nagłówki i. Treść odpowiedzi (niepokazywana) nie jest skompresowana przez przykład. Brak implementacji kompresji w `CustomCompressionProvider` klasie przykładowej. Jednak przykład pokazuje, gdzie należy zaimplementować taki algorytm kompresji.

![Okno programu Fiddler przedstawiające wynik żądania z nagłówkiem Accept-Encoding i wartością mycustomcompression. Nagłówki różnic i kodowania zawartości są dodawane do odpowiedzi.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME, typy

Oprogramowanie pośredniczące określa domyślny zestaw typów MIME dla kompresji:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Zastąp lub Dołącz typy MIME z opcjami oprogramowania pośredniczącego kompresji odpowiedzi. Należy zauważyć, że symbole wieloznaczne MIME `text/*` , takie jak nie są obsługiwane. Przykładowa aplikacja dodaje typ MIME dla `image/svg+xml` i kompresuje i obsługuje obraz transparentu ASP.NET Core (*transparent. SVG*).

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>Kompresja z bezpiecznym protokołem

Skompresowane odpowiedzi za pośrednictwem bezpiecznych połączeń można kontrolować przy `EnableForHttps` użyciu opcji, która jest domyślnie wyłączona. Używanie kompresji z dynamicznie generowanymi stronami może prowadzić do problemów z zabezpieczeniami, takich jak ataki w ramach [przestępczości](https://wikipedia.org/wiki/CRIME_(security_exploit)) i [naruszeń](https://wikipedia.org/wiki/BREACH_(security_exploit)) .

## <a name="adding-the-vary-header"></a>Dodawanie nagłówka Vary

Podczas kompresowania odpowiedzi na podstawie `Accept-Encoding` nagłówka istnieje potencjalnie wiele skompresowanych wersji odpowiedzi i nieskompresowanej wersji. Aby wymusić, że w pamięci podręcznej klienta i serwera proxy istnieją różne wersje i `Vary` powinny być przechowywane, nagłówek `Accept-Encoding` zostanie dodany z wartością. W ASP.NET Core 2,0 lub nowszej, oprogramowanie pośredniczące automatycznie dodaje `Vary` nagłówek podczas kompresowania odpowiedzi.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problem dotyczący oprogramowania pośredniczącego w przypadku Nginx zwrotnego serwera proxy

Gdy żądanie jest przekazywane przez Nginx, `Accept-Encoding` nagłówek zostaje usunięty. Usunięcie `Accept-Encoding` nagłówka zapobiega kompresji odpowiedzi przez oprogramowanie pośredniczące. Aby uzyskać więcej informacji, zobacz [Nginx: kompresja i dekompresja](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Ten problem jest śledzony przez [ilustrację kompresji przekazującej dla Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Praca z kompresją dynamiczną usług IIS

Jeśli masz aktywny moduł dynamicznej kompresji usług IIS skonfigurowany na poziomie serwera, który chcesz wyłączyć dla aplikacji, wyłącz moduł z dodatkiem do pliku *Web. config* . Aby uzyskać więcej informacji, zobacz [wyłączanie modułów IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)lub [Poster](https://www.getpostman.com/), które pozwala ustawić nagłówek `Accept-Encoding` żądania i zbadać nagłówki, rozmiar i treść odpowiedzi. Domyślnie oprogramowanie pośredniczące kompresji odpowiedzi kompresuje odpowiedzi, które spełniają następujące warunki:

* `Accept-Encoding` Nagłówek jest obecny z wartością `gzip` `*`lub kodowaniem niestandardowym, które pasuje do utworzonego niestandardowego dostawcy kompresji. Wartość nie może być `identity` lub mieć ustawienie wartości jakości (qvalue, `q`) równe 0 (zero).
* Typ MIME (`Content-Type`) musi być ustawiony i musi być zgodny z typem MIME skonfigurowanym na <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Żądanie nie może zawierać `Content-Range` nagłówka.
* Żądanie musi korzystać z protokołu niezabezpieczonego (http), o ile nie skonfigurowano protokołu Secure Protocol (https) w opcjach oprogramowania pośredniczącego kompresji odpowiedzi. *Należy pamiętać o niebezpieczeństwie [opisanym powyżej](#compression-with-secure-protocol) podczas włączania bezpiecznej kompresji zawartości.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Sieć deweloperów Mozilla: akceptowanie kodowania](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Sekcja RFC 7231 3.1.2.1: kodowanie zawartości](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230, sekcja 4.2.3: kodowanie gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Specyfikacja formatu pliku GZIP w wersji 4,3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
