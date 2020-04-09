---
title: Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia
author: rick-anderson
description: Dowiedz się więcej o konfiguracji aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniach.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659384"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia

Przez [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu modułu rdzenia IIS/ASP.NET, Nginx lub Apache. Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu, zanim dotrze do aplikacji:

* Gdy żądania HTTPS są przełączone przez protokół HTTP, oryginalny schemat (HTTPS) jest tracony i musi być przekazytywnie w nagłówku.
* Ponieważ aplikacja odbiera żądanie z serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, źródłowy adres IP klienta musi być również przekazywane w nagłówku.

Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.

## <a name="forwarded-headers"></a>Nagłówki przekazywane dalej

Zgodnie z konwencją serwery proxy prześliją informacje w nagłówkach HTTP.

| Nagłówek | Opis |
| ------ | ----------- |
| X-Forwarded-For | Przechowuje informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy. Ten parametr może zawierać adresy IP (i opcjonalnie numery portów). W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, w którym żądanie zostało po raz pierwszy wykonane. Kolejne identyfikatory serwera proxy są następcami. Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów. Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportu. |
| X-Forwarded-Proto | Wartość schematu źródłowego (HTTP/HTTPS). Wartość może być również lista schematów, jeśli żądanie przechodziło przez wiele serwerów proxy. |
| X-Forwarded-Host | Oryginalna wartość pola Nagłówek hosta. Zazwyczaj serwery proxy nie modyfikują nagłówka hosta. Informacje na temat luki w zabezpieczeniach uprawnień, która dotyczy systemów, w których serwer proxy nie sprawdza poprawności ani nie ogranicza nagłówków hostów do znanych dobrych wartości, zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295) |

Oprogramowanie pośredniczące nagłówków przesyłanych dalej z pakietu [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) odczytuje te nagłówki <xref:Microsoft.AspNetCore.Http.HttpContext>i wypełnia skojarzone pola w programie .

Oprogramowanie pośredniczące jest aktualizowane:

* [Zestaw adresów HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; przy użyciu wartości nagłówka. `X-Forwarded-For` Dodatkowe ustawienia wpływają na `RemoteIpAddress`sposób ustawiania oprogramowania pośredniczącego . Aby uzyskać szczegółowe informacje, zobacz [opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).
* [Zestaw httpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; `X-Forwarded-Proto` przy użyciu wartości nagłówka.
* [Zestaw httpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; `X-Forwarded-Host` przy użyciu wartości nagłówka.

Można skonfigurować [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania pośredniczącego oprogramowania nagłówkowego przesyłane dalej. Ustawienia domyślne to:

* Istnieje tylko *jeden serwer proxy* między aplikacją a źródłem żądań.
* Tylko adresy sprzężenia zwrotnego są skonfigurowane dla znanych serwerów proxy i znanych sieci.
* Przesłane dalej nagłówki `X-Forwarded-For` mają `X-Forwarded-Proto`nazwy i .

Nie wszystkie urządzenia sieciowe dodają `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` nagłówki bez dodatkowej konfiguracji. Jeśli żądania pośredniczące nie zawierają tych nagłówków po dotarciu do aplikacji, należy zapoznać się ze wskazówkami producenta urządzenia. Jeśli urządzenie używa innych nazw `X-Forwarded-For` `X-Forwarded-Proto`nagłówków <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> niż <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> i ustawia opcje i opcje, aby były zgodne z nazwami nagłówków używanymi przez urządzenie. Aby uzyskać więcej informacji, zobacz [Opcje oprogramowania pośredniczącego nagłówków przesyłania dalej](#forwarded-headers-middleware-options) i [Konfiguracja serwera proxy, który używa różnych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Moduł podstawowy IIS/IIS Express i ASP.NET

Oprogramowanie pośredniczące przekazywania nagłówków jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usługi IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za iIS i modułem ASP.NET Core. Oprogramowanie pośredniczące nagłówków przesyłanych dalej jest aktywowane w celu uruchomienia jako pierwsze w potoku oprogramowania pośredniczącego z ograniczoną konfiguracją specyficzną dla ASP.NET Modułu Rdzenia z powodu obaw dotyczących zaufania z przesłałymi nagłówkami (na przykład [faofing IP).](https://www.iplocation.net/ip-spoofing) Oprogramowanie pośredniczące jest skonfigurowane do przekazywania `X-Forwarded-For` nagłówków i `X-Forwarded-Proto` i jest ograniczone do jednego serwera proxy localhost. Jeśli wymagana jest dodatkowa [konfiguracja, zobacz opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Inne scenariusze serwera proxy i modułu równoważenia obciążenia

Poza używaniem [integracji iis](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model)oprogramowanie pośredniczące nagłówków przesyłanych dalej nie jest domyślnie włączone. Oprogramowanie pośredniczące nagłówków przesyłanych dalej musi być włączone, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikacja przetwarzała przesłane nagłówki za pomocą pliku . Po włączeniu oprogramowania <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pośredniczącego, jeśli oprogramowanie pośredniczące nie jest określone, domyślne [forwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Skonfiguruj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące, aby przesłać `X-Forwarded-For` dalej nagłówki i `X-Forwarded-Proto` nagłówki w pliku `Startup.ConfigureServices`. Wywołać <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:

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
> Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie są `Startup.ConfigureServices` określone w lub bezpośrednio <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>do metody rozszerzenia z , domyślne nagłówki do przekazania są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Właściwość <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musi być skonfigurowana z nagłówkami do przekazania.

## <a name="nginx-configuration"></a>Konfiguracja Nginx

Aby `X-Forwarded-For` przesłać `X-Forwarded-Proto` dalej nagłówki <xref:host-and-deploy/linux-nginx#configure-nginx>i nagłówki, zobacz . Aby uzyskać więcej informacji, zobacz [NGINX: Korzystanie z nagłówka Przesyłania dalej](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfiguracja Apache

`X-Forwarded-For`jest dodawany automatycznie (patrz [Mod_proxy modułu Apache: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Aby uzyskać informacje na `X-Forwarded-Proto` temat <xref:host-and-deploy/linux-apache#configure-apache>przekazywania nagłówka, zobacz .

## <a name="forwarded-headers-middleware-options"></a>Opcje oprogramowania pośredniczącego nagłówków z przekazywaniem dalej

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>kontrolować zachowanie oprogramowania pośredniczącego nagłówków przesyłanych dalej. Poniższy przykład zmienia wartości domyślne:

* Ogranicz liczbę wpisów w nagłówkach `2`przekazy.
* Dodaj znany adres `127.0.10.1`serwera proxy .
* Zmień nazwę nagłówka przesyłane `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name`dalej z domyślnej na .

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
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ogranicza hosty `X-Forwarded-Host` przez nagłówek do podanych wartości.<ul><li>Wartości są porównywane przy użyciu liczby porządkowej-ignore-case.</li><li>Należy wykluczyć numery portów.</li><li>Jeśli lista jest pusta, wszystkie hosty są dozwolone.</li><li>Symbol wieloznaczny `*` najwyższego poziomu umożliwia wszystkim niepustym hostom.</li><li>Symbole wieloznaczne poddomeny są dozwolone, ale nie pasują do domeny głównej. Na przykład `*.contoso.com` pasuje do `foo.contoso.com` poddomeny, `contoso.com`ale nie do domeny głównej .</li><li>Nazwy hostów Unicode są dozwolone, ale są konwertowane na [Punycode](https://tools.ietf.org/html/rfc3492) do dopasowania.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`być w [formie konwencjonalnej](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład). Adresy IPv6 nie są specjalne cased, aby sprawdzić, czy logiczna równość między różnymi formatami i nie jest wykonywana kanonizacja.</li><li>Niepodtrzymanie dozwolonych hostów może zezwolić osobie atakującej na fałszowanie łączy generowanych przez usługę.</li></ul>Wartość domyślna to `IList<string>`pusta . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-For` ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Określa, które podmioty przekazu powinny być przetwarzane. Zobacz [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie. Typowe wartości przypisane do `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tej właściwości to .<br><br>Wartością domyślną jest [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Host` ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Proto` ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane. Ustaw, `null` aby wyłączyć limit, ale należy `KnownProxies` to `KnownNetworks` zrobić tylko wtedy, gdy lub są skonfigurowane. Ustawienie wartości`null` niewartościowej jest środkiem ostrożności (ale nie gwarancją) w celu ochrony przed nieprawidłowo skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.<br><br>Przekazywane nagłówki Oprogramowanie pośredniczące przetwarza nagłówki w odwrotnej kolejności od prawej do lewej. Jeśli używana jest`1`wartość domyślna ( ) przetwarzana jest tylko wartość po `ForwardLimit` prawej stronie z nagłówków, chyba że wartość jest zwiększana.<br><br>Wartość domyślna to `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Zakresy adresów znanych sieci do akceptowania przesłanych dalej nagłówków. Podaj zakresy adresów IP przy użyciu notacji CIDR (Classless Interdomain Routing).<br><br>Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ). Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Wartość domyślna `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> to> zawierający pojedynczy `IPAddress.Loopback`wpis dla . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy znanych serwerów proxy do akceptowania przesłanych dalej nagłówków. Służy `KnownProxies` do określania dokładnych dopasowań adresów IP.<br><br>Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ). Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Wartość domyślna `IList` \< <xref:System.Net.IPAddress> to> zawierający pojedynczy `IPAddress.IPv6Loopback`wpis dla . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Wartość domyślna to `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Wartość domyślna to `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Wartość domyślna to `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Wymagaj, aby liczba wartości nagłówka była synchronizowana między [przetwarzanego pliku ForwardedHeadersOptions.ForwardedHeaders.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)<br><br>Wartość domyślna w ASP.NET Core 1.x to `true`. Wartość domyślna w ASP.NET Core 2.0 `false`lub nowszym to . |

## <a name="scenarios-and-use-cases"></a>Scenariusze i przypadki użycia

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Gdy nie można dodać nagłówków przesyłanych dalej, a wszystkie żądania są bezpieczne

W niektórych przypadkach może nie być możliwe dodanie przesłanych dalej nagłówków do żądań przesłanych do aplikacji. Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są `Startup.Configure` HTTPS, schemat można ustawić ręcznie przed użyciem dowolnego typu oprogramowania pośredniczącego:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku deweloperskim lub przejściowym.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Radzenie sobie z bazą ścieżki i serwerami proxy, które zmieniają ścieżkę żądania

Niektóre serwery proxy przechodzą ścieżkę w stanie nienaruszonym, ale ze ścieżką podstawową aplikacji, która powinna zostać usunięta, aby routing działał poprawnie. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) oprogramowanie pośredniczące dzieli ścieżkę na [httpRequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę podstawową aplikacji do [httpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Jeśli `/foo` jest to ścieżka podstawowa aplikacji `/foo/api/1`dla ścieżki `Request.PathBase` proxy `/foo` `Request.Path` przekazywane `/api/1` jako , oprogramowanie pośredniczące ustawia się do i do następujących poleceń:

```csharp
app.UsePathBase("/foo");
```

Oryginalna ścieżka i podstawa ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące jest wywoływane ponownie w odwrotnej kolejności. Aby uzyskać więcej informacji na <xref:fundamentals/middleware/index>temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz .

Jeśli serwer proxy przycina ścieżkę (na przykład przekierowanie `/foo/api/1` do `/api/1`), napraw przekierowania i łącza, ustawiając właściwość [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Jeśli serwer proxy dodaje dane ścieżki, odrzuć część ścieżki, aby naprawić przekierowania i łącza za pomocą <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywanie do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> właściwości:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfiguracja serwera proxy, który używa różnych nazw nagłówków

Jeśli serwer proxy nie używa `X-Forwarded-For` nagłówków o nazwie i `X-Forwarded-Proto` do przekazywania dalej <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> informacji o adresie/porcie i początkowym schemacie serwera proxy, ustaw i opcje zgodne z nazwami nagłówków używanymi przez serwer proxy:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6

Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1` `::ffff:a00:1`IPv4 reprezentowanym w IPv6 jako lub ). Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

W poniższym przykładzie adres sieciowy, który dostarcza przekazane `KnownNetworks` nagłówki, jest dodawany do listy w formacie IPv6.

Adres IPv4:`10.11.12.1/8`

Przekonwertowany adres IPv6:`::ffff:10.11.12.1`  
Przekonwertowana długość prefiksu: 104

Można również podać adres w formacie szesnastkowym (reprezentowanym`10.11.12.1` w IPv6 jako `::ffff:0a0b:0c01`). Podczas konwertowania adresu IPv4 na protokół IPv6 dodaj 96 do prefiksu CIDR Length (w`8` przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Przesyłanie dalej schematu dla odwrotnych serwerów proxy systemu Linux i innych niż IIS

Aplikacje, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> które wywołują i umieszczają witrynę w nieskończonej pętli, jeśli są wdrażane w usłudze Azure Linux App Service, maszynie wirtualnej systemu Azure Linux (VM) lub za dowolnym innym serwerem proxy wstecznym oprócz usług IIS. TLS jest zakończony przez odwrotny serwer proxy, a Kestrel nie jest świadomy prawidłowego schematu żądań. OAuth i OIDC również nie w tej konfiguracji, ponieważ generują niepoprawne przekierowania. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej podczas uruchamiania za usługami IIS, ale nie ma pasującej automatycznej konfiguracji dla systemu Linux (integracja Apache lub Nginx).

Aby przesłać schemat dalej z serwera proxy w scenariuszach innych niż IIS, dodaj i skonfiguruj oprogramowanie pośredniczące nagłówków przesłanych dalej. W `Startup.ConfigureServices`, użyj następującego kodu:

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

Aby skonfigurować usługę Azure App Service do przekazywania certyfikatów, zobacz [Konfigurowanie wzajemnego uwierzytelniania TLS dla usługi Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth). Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.

W `Startup.Configure`, dodaj następujący kod `app.UseAuthentication();`przed wywołaniem do:

```csharp
app.UseCertificateForwarding();
```


Skonfiguruj oprogramowanie pośredniczące przekazywania certyfikatów, aby określić nazwę nagłówka używana przez platformę Azure. W `Startup.ConfigureServices`polu Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące tworzy certyfikat:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Inne serwery proxy sieci Web

Jeśli używany jest serwer proxy, który nie jest usługami IIS lub routingiem żądań aplikacji usługi Azure App Service (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu, który otrzymał w nagłówku HTTP. W `Startup.Configure`, dodaj następujący kod `app.UseAuthentication();`przed wywołaniem do:

```csharp
app.UseCertificateForwarding();
```

Skonfiguruj oprogramowanie pośredniczące przekazywania certyfikatów, aby określić nazwę nagłówka. W `Startup.ConfigureServices`polu Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące tworzy certyfikat:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Jeśli serwer proxy nie jest base64-kodowanie certyfikatu (jak w przypadku `HeaderConverter` Nginx), należy ustawić opcję. Rozważmy poniższy `Startup.ConfigureServices`przykład w:

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

Jeśli nagłówki nie są przekazywane dalej zgodnie z oczekiwaniami, włącz [rejestrowanie](xref:fundamentals/logging/index). Jeśli dzienniki nie zawierają wystarczających informacji, aby rozwiązać problem, wylicz nagłówki żądań odebrane przez serwer. Użyj wbudowanego oprogramowania pośredniczącego, aby zapisywać nagłówki żądań do odpowiedzi aplikacji lub rejestrować nagłówki. 

Aby zapisać nagłówki do odpowiedzi aplikacji, umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminalu natychmiast po wywołaniu w :

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

Można zapisywać do dzienników zamiast treści odpowiedzi. Zapisywanie do dzienników umożliwia normalnie funkcjonować witryny podczas debugowania.

Aby napisać dzienniki, a nie do treści odpowiedzi:

* Wstrzyknąć `ILogger<Startup>` do klasy, `Startup` jak opisano w Tworzenie [dzienników w starcie](xref:fundamentals/logging/index#create-logs-in-startup).
* Umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`natychmiast po wywołaniu w .

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

Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są `X-Original-{For|Proto|Host}`przenoszone do . Jeśli w danym nagłówku znajduje się wiele wartości, oprogramowanie pośredniczące nagłówków przesyłanych dalej przetwarza nagłówki w odwrotnej kolejności od prawej do lewej. Wartość `ForwardLimit` domyślna jest `1` (jeden), więc tylko po prawej stronie wartość `ForwardLimit` z nagłówków jest przetwarzany, chyba że wartość jest zwiększona.

Oryginalny zdalny adres IP żądania musi `KnownProxies` być `KnownNetworks` zgodny z wpisem na listach lub przed przetworzeniem nagłówków przesyłanych dalej. Ogranicza to fałszowanie nagłówka przez nieuchównienie przesyłania dalej od niezaufanych serwerów proxy. Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

W poprzednim przykładzie 10.0.0.100 jest serwerem proxy. Jeśli serwer jest zaufanym serwerem proxy, dodaj `KnownProxies` adres IP serwera `KnownNetworks`do `Startup.ConfigureServices`(lub dodaj zaufaną sieć) w pliku . Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego nagłówków dalej.](#forwarded-headers-middleware-options)

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Zezwalaj tylko zaufanym serwerom proxy i sieciom na przekazywanie nagłówków. W przeciwnym razie możliwe są ataki [fałszowania adresów IP.](https://www.iplocation.net/ip-spoofing)

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:host-and-deploy/web-farm>
* [Poradnia zabezpieczeń firmy Microsoft CVE-2018-0787: luka w zabezpieczeniach ASP.NET rdzeń z podniesieniem uprawnień](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu modułu rdzenia IIS/ASP.NET, Nginx lub Apache. Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu, zanim dotrze do aplikacji:

* Gdy żądania HTTPS są przełączone przez protokół HTTP, oryginalny schemat (HTTPS) jest tracony i musi być przekazytywnie w nagłówku.
* Ponieważ aplikacja odbiera żądanie z serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, źródłowy adres IP klienta musi być również przekazywane w nagłówku.

Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.

## <a name="forwarded-headers"></a>Nagłówki przekazywane dalej

Zgodnie z konwencją serwery proxy prześliją informacje w nagłówkach HTTP.

| Nagłówek | Opis |
| ------ | ----------- |
| X-Forwarded-For | Przechowuje informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy. Ten parametr może zawierać adresy IP (i opcjonalnie numery portów). W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, w którym żądanie zostało po raz pierwszy wykonane. Kolejne identyfikatory serwera proxy są następcami. Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów. Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportu. |
| X-Forwarded-Proto | Wartość schematu źródłowego (HTTP/HTTPS). Wartość może być również lista schematów, jeśli żądanie przechodziło przez wiele serwerów proxy. |
| X-Forwarded-Host | Oryginalna wartość pola Nagłówek hosta. Zazwyczaj serwery proxy nie modyfikują nagłówka hosta. Informacje na temat luki w zabezpieczeniach uprawnień, która dotyczy systemów, w których serwer proxy nie sprawdza poprawności ani nie ogranicza nagłówków hostów do znanych dobrych wartości, zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295) |

Oprogramowanie pośredniczące nagłówków przesyłanych dalej z pakietu [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) odczytuje te nagłówki <xref:Microsoft.AspNetCore.Http.HttpContext>i wypełnia skojarzone pola w programie .

Oprogramowanie pośredniczące jest aktualizowane:

* [Zestaw adresów HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; przy użyciu wartości nagłówka. `X-Forwarded-For` Dodatkowe ustawienia wpływają na `RemoteIpAddress`sposób ustawiania oprogramowania pośredniczącego . Aby uzyskać szczegółowe informacje, zobacz [opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).
* [Zestaw httpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; `X-Forwarded-Proto` przy użyciu wartości nagłówka.
* [Zestaw httpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; `X-Forwarded-Host` przy użyciu wartości nagłówka.

Można skonfigurować [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania pośredniczącego oprogramowania nagłówkowego przesyłane dalej. Ustawienia domyślne to:

* Istnieje tylko *jeden serwer proxy* między aplikacją a źródłem żądań.
* Tylko adresy sprzężenia zwrotnego są skonfigurowane dla znanych serwerów proxy i znanych sieci.
* Przesłane dalej nagłówki `X-Forwarded-For` mają `X-Forwarded-Proto`nazwy i .

Nie wszystkie urządzenia sieciowe dodają `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` nagłówki bez dodatkowej konfiguracji. Jeśli żądania pośredniczące nie zawierają tych nagłówków po dotarciu do aplikacji, należy zapoznać się ze wskazówkami producenta urządzenia. Jeśli urządzenie używa innych nazw `X-Forwarded-For` `X-Forwarded-Proto`nagłówków <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> niż <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> i ustawia opcje i opcje, aby były zgodne z nazwami nagłówków używanymi przez urządzenie. Aby uzyskać więcej informacji, zobacz [Opcje oprogramowania pośredniczącego nagłówków przesyłania dalej](#forwarded-headers-middleware-options) i [Konfiguracja serwera proxy, który używa różnych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Moduł podstawowy IIS/IIS Express i ASP.NET

Oprogramowanie pośredniczące przekazywania nagłówków jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usługi IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za iIS i modułem ASP.NET Core. Oprogramowanie pośredniczące nagłówków przesyłanych dalej jest aktywowane w celu uruchomienia jako pierwsze w potoku oprogramowania pośredniczącego z ograniczoną konfiguracją specyficzną dla ASP.NET Modułu Rdzenia z powodu obaw dotyczących zaufania z przesłałymi nagłówkami (na przykład [faofing IP).](https://www.iplocation.net/ip-spoofing) Oprogramowanie pośredniczące jest skonfigurowane do przekazywania `X-Forwarded-For` nagłówków i `X-Forwarded-Proto` i jest ograniczone do jednego serwera proxy localhost. Jeśli wymagana jest dodatkowa [konfiguracja, zobacz opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Inne scenariusze serwera proxy i modułu równoważenia obciążenia

Poza używaniem [integracji iis](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model)oprogramowanie pośredniczące nagłówków przesyłanych dalej nie jest domyślnie włączone. Oprogramowanie pośredniczące nagłówków przesyłanych dalej musi być włączone, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikacja przetwarzała przesłane nagłówki za pomocą pliku . Po włączeniu oprogramowania <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pośredniczącego, jeśli oprogramowanie pośredniczące nie jest określone, domyślne [forwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Skonfiguruj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące, aby przesłać `X-Forwarded-For` dalej nagłówki i `X-Forwarded-Proto` nagłówki w pliku `Startup.ConfigureServices`. Wywołać <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:

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
> Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie są `Startup.ConfigureServices` określone w lub bezpośrednio <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>do metody rozszerzenia z , domyślne nagłówki do przekazania są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Właściwość <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musi być skonfigurowana z nagłówkami do przekazania.

## <a name="nginx-configuration"></a>Konfiguracja Nginx

Aby `X-Forwarded-For` przesłać `X-Forwarded-Proto` dalej nagłówki <xref:host-and-deploy/linux-nginx#configure-nginx>i nagłówki, zobacz . Aby uzyskać więcej informacji, zobacz [NGINX: Korzystanie z nagłówka Przesyłania dalej](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfiguracja Apache

`X-Forwarded-For`jest dodawany automatycznie (patrz [Mod_proxy modułu Apache: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Aby uzyskać informacje na `X-Forwarded-Proto` temat <xref:host-and-deploy/linux-apache#configure-apache>przekazywania nagłówka, zobacz .

## <a name="forwarded-headers-middleware-options"></a>Opcje oprogramowania pośredniczącego nagłówków z przekazywaniem dalej

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>kontrolować zachowanie oprogramowania pośredniczącego nagłówków przesyłanych dalej. Poniższy przykład zmienia wartości domyślne:

* Ogranicz liczbę wpisów w nagłówkach `2`przekazy.
* Dodaj znany adres `127.0.10.1`serwera proxy .
* Zmień nazwę nagłówka przesyłane `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name`dalej z domyślnej na .

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
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ogranicza hosty `X-Forwarded-Host` przez nagłówek do podanych wartości.<ul><li>Wartości są porównywane przy użyciu liczby porządkowej-ignore-case.</li><li>Należy wykluczyć numery portów.</li><li>Jeśli lista jest pusta, wszystkie hosty są dozwolone.</li><li>Symbol wieloznaczny `*` najwyższego poziomu umożliwia wszystkim niepustym hostom.</li><li>Symbole wieloznaczne poddomeny są dozwolone, ale nie pasują do domeny głównej. Na przykład `*.contoso.com` pasuje do `foo.contoso.com` poddomeny, `contoso.com`ale nie do domeny głównej .</li><li>Nazwy hostów Unicode są dozwolone, ale są konwertowane na [Punycode](https://tools.ietf.org/html/rfc3492) do dopasowania.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`być w [formie konwencjonalnej](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład). Adresy IPv6 nie są specjalne cased, aby sprawdzić, czy logiczna równość między różnymi formatami i nie jest wykonywana kanonizacja.</li><li>Niepodtrzymanie dozwolonych hostów może zezwolić osobie atakującej na fałszowanie łączy generowanych przez usługę.</li></ul>Wartość domyślna to `IList<string>`pusta . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-For` ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Określa, które podmioty przekazu powinny być przetwarzane. Zobacz [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie. Typowe wartości przypisane do `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tej właściwości to .<br><br>Wartością domyślną jest [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Host` ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Proto` ale używa innego nagłówka do przekazywania informacji.<br><br>Wartość domyślna to `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane. Ustaw, `null` aby wyłączyć limit, ale należy `KnownProxies` to `KnownNetworks` zrobić tylko wtedy, gdy lub są skonfigurowane. Ustawienie wartości`null` niewartościowej jest środkiem ostrożności (ale nie gwarancją) w celu ochrony przed nieprawidłowo skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.<br><br>Przekazywane nagłówki Oprogramowanie pośredniczące przetwarza nagłówki w odwrotnej kolejności od prawej do lewej. Jeśli używana jest`1`wartość domyślna ( ) przetwarzana jest tylko wartość po `ForwardLimit` prawej stronie z nagłówków, chyba że wartość jest zwiększana.<br><br>Wartość domyślna to `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Zakresy adresów znanych sieci do akceptowania przesłanych dalej nagłówków. Podaj zakresy adresów IP przy użyciu notacji CIDR (Classless Interdomain Routing).<br><br>Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ). Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Wartość domyślna `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> to> zawierający pojedynczy `IPAddress.Loopback`wpis dla . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy znanych serwerów proxy do akceptowania przesłanych dalej nagłówków. Służy `KnownProxies` do określania dokładnych dopasowań adresów IP.<br><br>Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ). Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Wartość domyślna `IList` \< <xref:System.Net.IPAddress> to> zawierający pojedynczy `IPAddress.IPv6Loopback`wpis dla . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Wartość domyślna to `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Wartość domyślna to `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Wartość domyślna to `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Wymagaj, aby liczba wartości nagłówka była synchronizowana między [przetwarzanego pliku ForwardedHeadersOptions.ForwardedHeaders.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)<br><br>Wartość domyślna w ASP.NET Core 1.x to `true`. Wartość domyślna w ASP.NET Core 2.0 `false`lub nowszym to . |

## <a name="scenarios-and-use-cases"></a>Scenariusze i przypadki użycia

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Gdy nie można dodać nagłówków przesyłanych dalej, a wszystkie żądania są bezpieczne

W niektórych przypadkach może nie być możliwe dodanie przesłanych dalej nagłówków do żądań przesłanych do aplikacji. Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są `Startup.Configure` HTTPS, schemat można ustawić ręcznie przed użyciem dowolnego typu oprogramowania pośredniczącego:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku deweloperskim lub przejściowym.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Radzenie sobie z bazą ścieżki i serwerami proxy, które zmieniają ścieżkę żądania

Niektóre serwery proxy przechodzą ścieżkę w stanie nienaruszonym, ale ze ścieżką podstawową aplikacji, która powinna zostać usunięta, aby routing działał poprawnie. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) oprogramowanie pośredniczące dzieli ścieżkę na [httpRequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę podstawową aplikacji do [httpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Jeśli `/foo` jest to ścieżka podstawowa aplikacji `/foo/api/1`dla ścieżki `Request.PathBase` proxy `/foo` `Request.Path` przekazywane `/api/1` jako , oprogramowanie pośredniczące ustawia się do i do następujących poleceń:

```csharp
app.UsePathBase("/foo");
```

Oryginalna ścieżka i podstawa ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące jest wywoływane ponownie w odwrotnej kolejności. Aby uzyskać więcej informacji na <xref:fundamentals/middleware/index>temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz .

Jeśli serwer proxy przycina ścieżkę (na przykład przekierowanie `/foo/api/1` do `/api/1`), napraw przekierowania i łącza, ustawiając właściwość [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Jeśli serwer proxy dodaje dane ścieżki, odrzuć część ścieżki, aby naprawić przekierowania i łącza za pomocą <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywanie do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> właściwości:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfiguracja serwera proxy, który używa różnych nazw nagłówków

Jeśli serwer proxy nie używa `X-Forwarded-For` nagłówków o nazwie i `X-Forwarded-Proto` do przekazywania dalej <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> informacji o adresie/porcie i początkowym schemacie serwera proxy, ustaw i opcje zgodne z nazwami nagłówków używanymi przez serwer proxy:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6

Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1` `::ffff:a00:1`IPv4 reprezentowanym w IPv6 jako lub ). Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

W poniższym przykładzie adres sieciowy, który dostarcza przekazane `KnownNetworks` nagłówki, jest dodawany do listy w formacie IPv6.

Adres IPv4:`10.11.12.1/8`

Przekonwertowany adres IPv6:`::ffff:10.11.12.1`  
Przekonwertowana długość prefiksu: 104

Można również podać adres w formacie szesnastkowym (reprezentowanym`10.11.12.1` w IPv6 jako `::ffff:0a0b:0c01`). Podczas konwertowania adresu IPv4 na protokół IPv6 dodaj 96 do prefiksu CIDR Length (w`8` przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Przesyłanie dalej schematu dla odwrotnych serwerów proxy systemu Linux i innych niż IIS

Aplikacje, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> które wywołują i umieszczają witrynę w nieskończonej pętli, jeśli są wdrażane w usłudze Azure Linux App Service, maszynie wirtualnej systemu Azure Linux (VM) lub za dowolnym innym serwerem proxy wstecznym oprócz usług IIS. TLS jest zakończony przez odwrotny serwer proxy, a Kestrel nie jest świadomy prawidłowego schematu żądań. OAuth i OIDC również nie w tej konfiguracji, ponieważ generują niepoprawne przekierowania. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej podczas uruchamiania za usługami IIS, ale nie ma pasującej automatycznej konfiguracji dla systemu Linux (integracja Apache lub Nginx).

Aby przesłać schemat dalej z serwera proxy w scenariuszach innych niż IIS, dodaj i skonfiguruj oprogramowanie pośredniczące nagłówków przesłanych dalej. W `Startup.ConfigureServices`, użyj następującego kodu:

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

Jeśli nagłówki nie są przekazywane dalej zgodnie z oczekiwaniami, włącz [rejestrowanie](xref:fundamentals/logging/index). Jeśli dzienniki nie zawierają wystarczających informacji, aby rozwiązać problem, wylicz nagłówki żądań odebrane przez serwer. Użyj wbudowanego oprogramowania pośredniczącego, aby zapisywać nagłówki żądań do odpowiedzi aplikacji lub rejestrować nagłówki. 

Aby zapisać nagłówki do odpowiedzi aplikacji, umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminalu natychmiast po wywołaniu w :

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

Można zapisywać do dzienników zamiast treści odpowiedzi. Zapisywanie do dzienników umożliwia normalnie funkcjonować witryny podczas debugowania.

Aby napisać dzienniki, a nie do treści odpowiedzi:

* Wstrzyknąć `ILogger<Startup>` do klasy, `Startup` jak opisano w Tworzenie [dzienników w starcie](xref:fundamentals/logging/index#create-logs-in-startup).
* Umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`natychmiast po wywołaniu w .

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

Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są `X-Original-{For|Proto|Host}`przenoszone do . Jeśli w danym nagłówku znajduje się wiele wartości, oprogramowanie pośredniczące nagłówków przesyłanych dalej przetwarza nagłówki w odwrotnej kolejności od prawej do lewej. Wartość `ForwardLimit` domyślna jest `1` (jeden), więc tylko po prawej stronie wartość `ForwardLimit` z nagłówków jest przetwarzany, chyba że wartość jest zwiększona.

Oryginalny zdalny adres IP żądania musi `KnownProxies` być `KnownNetworks` zgodny z wpisem na listach lub przed przetworzeniem nagłówków przesyłanych dalej. Ogranicza to fałszowanie nagłówka przez nieuchównienie przesyłania dalej od niezaufanych serwerów proxy. Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

W poprzednim przykładzie 10.0.0.100 jest serwerem proxy. Jeśli serwer jest zaufanym serwerem proxy, dodaj `KnownProxies` adres IP serwera `KnownNetworks`do `Startup.ConfigureServices`(lub dodaj zaufaną sieć) w pliku . Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego nagłówków dalej.](#forwarded-headers-middleware-options)

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Zezwalaj tylko zaufanym serwerom proxy i sieciom na przekazywanie nagłówków. W przeciwnym razie możliwe są ataki [fałszowania adresów IP.](https://www.iplocation.net/ip-spoofing)

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:host-and-deploy/web-farm>
* [Poradnia zabezpieczeń firmy Microsoft CVE-2018-0787: luka w zabezpieczeniach ASP.NET rdzeń z podniesieniem uprawnień](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
