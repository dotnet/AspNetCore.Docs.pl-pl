---
title: Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia
author: rick-anderson
description: Dowiedz się więcej o konfigurowaniu aplikacji hostowanych za serwerami proxy i usługami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniu.
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 28a802414fd59f684a56e2b735140438d33be740
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109952"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia

[Krzysztof Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache. Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:

* Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.
* Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.

Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.

## <a name="forwarded-headers"></a>Nagłówki przesłane dalej

Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.

| Nagłówek | Opis |
| ------ | ----------- |
| X-Forwarded-For | Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy. Ten parametr może zawierać adresy IP (i opcjonalnie numery portów). W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane. Kolejne identyfikatory serwerów proxy są następujące. Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów. Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej. |
| X-Forwarded-proto | Wartość schematu inicjującego (HTTP/HTTPS). Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy. |
| X-Forward-Host | Oryginalna wartość pola nagłówka hosta. Zazwyczaj proxy nie modyfikują nagłówka hosta. Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości. |

Przekazane nagłówki pośredniczące ( <xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware> ), odczytują te nagłówki i wypełniają pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .

Aktualizacje oprogramowania pośredniczącego:

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka. Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` . Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).
* [HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.
* [HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.

Aby uzyskać więcej informacji na ten temat, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/21615)w serwisie GitHub.

Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania. Ustawienia domyślne to:

* Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .
* Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.
* Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .

Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji. Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji. Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie. Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Usługi IIS/IIS Express i moduł ASP.NET Core

Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core. Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)). Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego. Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Inne scenariusze serwera proxy i modułu równoważenia obciążenia

Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone. Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> . Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a>Przekierowane nagłówki — zamówienie oprogramowania

Przekierowane nagłówki oprogramowanie pośredniczące powinno być uruchamiane przed innym programem pośredniczącym. Takie porządkowanie zapewnia, że oprogramowanie pośredniczące polegające na informacjach o przekazanych nagłówkach może zużywać wartości nagłówka do przetworzenia. Przekierowane nagłówki oprogramowanie pośredniczące może zostać uruchomione po obsłudze diagnostyki i błędów, ale należy je uruchomić przed wywołaniem `UseHsts` :

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

Alternatywnie Wywołaj `UseForwardedHeaders` przed diagnostyką:

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.

## <a name="nginx-configuration"></a>Konfiguracja Nginx

Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> . Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.

## <a name="apache-configuration"></a>Konfiguracja Apache

`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .

## <a name="forwarded-headers-middleware-options"></a>Przekazane nagłówki — Opcje oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków. Poniższy przykład zmienia wartości domyślne:

* Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .
* Dodaj znany adres serwera proxy `127.0.10.1` .
* Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.<ul><li>Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</li><li>Numery portów muszą być wykluczone.</li><li>Jeśli lista jest pusta, dozwolone są wszystkie hosty.</li><li>Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</li><li>Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną. Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</li><li>Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ). Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</li><li>Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</li></ul>Wartość domyślna jest pusta `IList<string>` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Określa, które usługi przesyłania dalej powinny być przetwarzane. Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie. Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .<br><br>Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane. Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane. Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.<br><br>Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej. Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.<br><br>Wartość domyślna to `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki. Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).<br><br>Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ). Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).<br><br>Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki. Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.<br><br>Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ). Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).<br><br>Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Wartość domyślna to `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Wartość domyślna to `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Wartość domyślna to `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .<br><br>Wartość domyślna w ASP.NET Core 1. x to `true` . Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` . |

## <a name="scenarios-and-use-cases"></a>Scenariusze i przypadki użycia

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne

W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji. Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania

Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo. Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:

```csharp
app.UsePathBase("/foo");
```

Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie. Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .

Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfiguracja serwera proxy, który używa innych nazw nagłówków

Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS

Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS. Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania. Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).

Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące. W programie `Startup.ConfigureServices` Użyj następującego kodu:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a>Przekazywanie certyfikatów 

### <a name="azure"></a>Azure

Aby skonfigurować Azure App Service na potrzeby przekazywania certyfikatów, zobacz [Konfigurowanie uwierzytelniania wzajemnego TLS dla Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth). Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.

W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :

```csharp
app.UseCertificateForwarding();
```


Skonfiguruj oprogramowanie pośredniczące do przekazywania certyfikatów, aby określić nazwę nagłówka używaną przez platformę Azure. W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Inne serwery proxy sieci Web

Jeśli używany jest serwer proxy, który nie jest programem IIS lub Azure App Service routingu żądań aplikacji (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu otrzymanego w nagłówku HTTP. W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :

```csharp
app.UseCertificateForwarding();
```

Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej certyfikatów, aby określić nazwę nagłówka. W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Jeśli serwer proxy nie może zakodować certyfikatu Base64 (podobnie jak w przypadku Nginx), ustaw `HeaderConverter` opcję. Rozważmy następujący przykład w `Startup.ConfigureServices` :

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index). Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer. Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków. 

Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Możesz pisać do dzienników zamiast treści odpowiedzi. Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.

Do zapisu dzienników, a nie do treści odpowiedzi:

* Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).
* Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` . Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej. Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.

Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków. To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy. Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

W poprzednim przykładzie 10.0.0.100 jest serwerem proxy. Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` . Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom. W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:host-and-deploy/web-farm>
* [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache. Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:

* Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.
* Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.

Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.

## <a name="forwarded-headers"></a>Nagłówki przesłane dalej

Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.

| Nagłówek | Opis |
| ------ | ----------- |
| X-Forwarded-For | Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy. Ten parametr może zawierać adresy IP (i opcjonalnie numery portów). W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane. Kolejne identyfikatory serwerów proxy są następujące. Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów. Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej. |
| X-Forwarded-proto | Wartość schematu inicjującego (HTTP/HTTPS). Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy. |
| X-Forward-Host | Oryginalna wartość pola nagłówka hosta. Zazwyczaj proxy nie modyfikują nagłówka hosta. Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości. |

Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .

Aktualizacje oprogramowania pośredniczącego:

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka. Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` . Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).
* [HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.
* [HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.

Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania. Ustawienia domyślne to:

* Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .
* Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.
* Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .

Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji. Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji. Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie. Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Usługi IIS/IIS Express i moduł ASP.NET Core

Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core. Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)). Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego. Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Inne scenariusze serwera proxy i modułu równoważenia obciążenia

Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone. Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> . Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` . Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.

## <a name="nginx-configuration"></a>Konfiguracja Nginx

Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> . Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.

## <a name="apache-configuration"></a>Konfiguracja Apache

`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .

## <a name="forwarded-headers-middleware-options"></a>Przekazane nagłówki — Opcje oprogramowania pośredniczącego

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków. Poniższy przykład zmienia wartości domyślne:

* Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .
* Dodaj znany adres serwera proxy `127.0.10.1` .
* Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.<ul><li>Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</li><li>Numery portów muszą być wykluczone.</li><li>Jeśli lista jest pusta, dozwolone są wszystkie hosty.</li><li>Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</li><li>Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną. Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</li><li>Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ). Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</li><li>Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</li></ul>Wartość domyślna jest pusta `IList<string>` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Określa, które usługi przesyłania dalej powinny być przetwarzane. Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie. Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .<br><br>Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane. Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane. Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.<br><br>Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej. Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.<br><br>Wartość domyślna to `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki. Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).<br><br>Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ). Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki. Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.<br><br>Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ). Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Wartość domyślna to `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Wartość domyślna to `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Wartość domyślna to `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .<br><br>Wartość domyślna w ASP.NET Core 1. x to `true` . Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` . |

## <a name="scenarios-and-use-cases"></a>Scenariusze i przypadki użycia

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne

W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji. Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania

Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo. Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:

```csharp
app.UsePathBase("/foo");
```

Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie. Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .

Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfiguracja serwera proxy, który używa innych nazw nagłówków

Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6

Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ). Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.

Adres IPv4: `10.11.12.1/8`

Przekonwertowany adres IPv6: `::ffff:10.11.12.1`  
Konwertowana długość prefiksu: 104

Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ). Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS

Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS. Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania. Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).

Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące. W programie `Startup.ConfigureServices` Użyj następującego kodu:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index). Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer. Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków. 

Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Możesz pisać do dzienników zamiast treści odpowiedzi. Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.

Do zapisu dzienników, a nie do treści odpowiedzi:

* Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).
* Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` . Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej. Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.

Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków. To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy. Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

W poprzednim przykładzie 10.0.0.100 jest serwerem proxy. Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` . Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom. W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:host-and-deploy/web-farm>
* [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
