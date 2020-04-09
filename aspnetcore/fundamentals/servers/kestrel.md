---
title: Implementacja serwera www Kestrel w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o Kestrel, międzyplatformowym serwerze www dla ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 18846d60fd5c29f17cb4e59192795fd92251e2d0
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976771"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementacja serwera www Kestrel w ASP.NET Core

Przez [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher)i Stephen [Halter](https://twitter.com/halter73)

::: moniker range=">= aspnetcore-3.0"

Pustułka to wieloplatformowy [serwer www dla ASP.NET Core.](xref:fundamentals/servers/index) Pustułka to serwer www, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.

Pustułka obsługuje następujące scenariusze:

* HTTPS
* Nieprzezroczyste uaktualnienie używane do włączania [websockets](https://github.com/aspnet/websockets)
* Gniazda Unix o wysokiej wydajności za Nginx
* HTTP/2 (z wyjątkiem&dagger;systemu macOS)

&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.

Pustułka jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez program .NET Core.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* System operacyjny&dagger;
  * Windows Server 2016/Windows 10 lub nowsze&Dagger;
  * Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16.04 lub nowszym)
* Struktura docelowa: .NET Core 2.2 lub nowsza
* Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1.2 lub nowsze

&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.
&Dagger;Pustułka ma ograniczoną obsługę protokołu HTTP/2 w systemach Windows Server 2012 R2 i Windows 8.1. Obsługa jest ograniczona, ponieważ lista obsługiwanych pakietów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona. Certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego krzywej eliptycznej (ECDSA) może być wymagany do zabezpieczenia połączeń TLS.

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.

Protokół HTTP/2 jest domyślnie wyłączony. Aby uzyskać więcej informacji na temat konfiguracji, zobacz [opcje Kestrel](#kestrel-options) i [ListenOptions.Protocols](#listenoptionsprotocols) sekcje.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kiedy używać Kestrel z odwrotnym serwerem proxy

Pustułka może być używana samodzielnie lub z *odwrotnym serwerem proxy,* takim jak [Internetowe Usługi Informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/). Serwer odwrotnego serwera proxy odbiera żądania HTTP z sieci i przekazuje je do Kestrel.

Pustułka używana jako serwer internetowy edge (web-facing):

![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

Pustułka używana w konfiguracji odwrotnego serwera proxy:

![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

Konfiguracja z odwrotnym serwerem proxy lub bez niego jest obsługiwaną konfiguracją hostingu.

Pustułka używana jako serwer brzegowy bez odwrotnego serwera proxy nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami. Gdy Kestrel jest skonfigurowany do nasłuchiwać na porcie, Kestrel obsługuje `Host` cały ruch dla tego portu, niezależnie od nagłówków żądań. Odwrotny serwer proxy, który może udostępniać porty, ma możliwość przesyłania dalej żądań do Kestrel na unikatowym adresie IP i porcie.

Nawet jeśli serwer odwrotnego serwera proxy nie jest wymagany, dobrym wyborem może być użycie odwrotnego serwera proxy.

Odwrotny serwer proxy:

* Można ograniczyć narażony obszar powierzchni publicznej aplikacji, które obsługuje.
* Zapewnij dodatkową warstwę konfiguracji i obrony.
* Może lepiej zintegrować się z istniejącą infrastrukturą.
* Uprość równoważenie obciążenia i bezpieczną komunikację (HTTPS). Tylko serwer odwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.

> [!WARNING]
> Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).

## <a name="kestrel-in-aspnet-core-apps"></a>Pustułka w aplikacjach ASP.NET Core

ASP.NET szablony projektów Core domyślnie używają Pustułki. W *Program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metoda wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

Aby uzyskać więcej informacji na temat tworzenia hosta, zobacz sekcje <xref:fundamentals/host/generic-host#set-up-a-host> *Konfigurowanie hosta* i *Domyślne ustawienia konstruktora* w obszarze .

Aby zapewnić dodatkową `ConfigureWebHostDefaults`konfigurację `ConfigureKestrel`po wywołaniu , użyj :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a>Opcje pustułki

Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w wdrożeniach internetowych.

Ustaw ograniczenia właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy. Właściwość `Limits` zawiera wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.

W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obszaru nazw:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

W przykładach pokazano w dalszej części tego artykułu Opcje Kestrel są skonfigurowane w kodzie języka C#. Opcje pustułki można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index). Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację kestrelu z *pliku appsettings.json* lub *appsettings.{ Środowisko}.json* plik:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfiguracji punktu końcowego](#endpoint-configuration) można konfigurować od dostawców konfiguracji. Pozostała konfiguracja pustułka musi być skonfigurowana w kodzie C#.

Użyj **jednego** z następujących podejść:

* Skonfiguruj `Startup.ConfigureServices`Pustułkę w :

  1. Wstrzyknąć `IConfiguration` `Startup` wystąpienie do klasy. W poniższym przykładzie przyjęto założenie, że `Configuration` wstrzyknięta konfiguracja jest przypisana do właściwości.
  2. W `Startup.ConfigureServices`programie `Kestrel` załaduj sekcję konfiguracji do konfiguracji Kestrela:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Konfigurowanie Pustułki podczas tworzenia hosta:

  W *Program.cs*załaduj `Kestrel` sekcję konfiguracji do konfiguracji Kestrela:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

Oba powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji.](xref:fundamentals/configuration/index)

### <a name="keep-alive-timeout"></a>Limit czasu utrzymywanie aktywności przy życiu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Pobiera lub ustawia [limit czasu keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). Wartość domyślna to 2 minuty.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Maksymalna liczba połączeń klientów

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maksymalną liczbę równoczesnych otwartych połączeń TCP można ustawić dla całej aplikacji za pomocą następującego kodu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Istnieje oddzielny limit dla połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu WebSockets). Po uaktualnieniu połączenia nie jest wliczane `MaxConcurrentConnections` do limitu.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maksymalna liczba połączeń jest domyślnie nieograniczona (zerowa).

### <a name="maximum-request-body-size"></a>Maksymalny rozmiar treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.

Zalecane podejście do zastąpienia limitu w ASP.NET Core MVC aplikacji jest <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji na każde żądanie:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Zastąp ustawienie dla określonego żądania w oprogramowaniu pośredniczącym:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Wyjątek jest zgłaszany, jeśli aplikacja konfiguruje limit żądania po aplikacji zaczął odczytywać żądanie. Istnieje `IsReadOnly` właściwość, która wskazuje, `MaxRequestBodySize` czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Gdy aplikacja jest prowadzona [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [Core Module](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączona, ponieważ iIS już ustawia limit.

### <a name="minimum-request-body-data-rate"></a>Minimalna szybkość danych treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Pustułka sprawdza co sekundę, czy dane są przychodzące z określoną szybkością w bajtach/sekundę. Jeśli szybkość spadnie poniżej minimum, upłynie limit czasu połączenia. Okres prolongaty to czas, który Kestrel daje klientowi na zwiększenie szybkości wysyłania do minimum; stawka nie jest sprawdzana w tym czasie. Okres prolongaty pomaga uniknąć porzucania połączeń, które początkowo wysyłają dane z powolnym tempem ze względu na spowolnienie tcp.

Domyślna minimalna stawka wynosi 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.

Minimalna stawka ma również zastosowanie do odpowiedzi. Kod, aby ustawić limit żądania i limit odpowiedzi `RequestBody` jest `Response` taka sama, z wyjątkiem posiadania lub w nazwy właściwości i interfejsu.

Oto przykład, który pokazuje, jak skonfigurować minimalne szybkości transmisji danych w *Program.cs:*

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Zastąp minimalne limity szybkości na żądanie w oprogramowaniu pośredniczącym:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Odwołuje <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> się w poprzednim przykładzie `HttpContext.Features` nie jest obecny w dla żądań HTTP/2, ponieważ modyfikowanie limitów szybkości na podstawie żądania na ogół nie jest obsługiwany dla PROTOKOŁU HTTP/2 ze względu na obsługę protokołu dla multipleksowania żądania. Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> jest nadal `HttpContext.Features` obecny dla żądań HTTP/2, ponieważ limit szybkości odczytu nadal można `IHttpMinRequestBodyDataRateFeature.MinDataRate` całkowicie `null` *wyłączyć* na podstawie żądania, ustawiając nawet dla żądania HTTP/2. Próba odczytu `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby skonfigurowania go na `null` wartość inną `NotSupportedException` niż spowoduje, że zostanie wrzucony, biorąc pod uwagę żądanie HTTP/2.

Limity szybkości dla całego `KestrelServerOptions.Limits` serwera skonfigurowane za pośrednictwem nadal mają zastosowanie zarówno do połączeń HTTP/1.x, jak i HTTP/2.

### <a name="request-headers-timeout"></a>Limit czasu nagłówków żądań

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Pobiera lub ustawia maksymalną ilość czasu serwer spędza odbieranie nagłówków żądań. Wartość domyślna to 30 sekund.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Maksymalna liczba strumieni na połączenie

`Http2.MaxStreamsPerConnection`ogranicza liczbę strumieni żądań równoczesnych na połączenie HTTP/2. Nadmiar strumieni są odrzucane.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Wartość domyślna to 100.

### <a name="header-table-size"></a>Rozmiar tabeli nagłówka

Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2. `Http2.HeaderTableSize`ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK. Wartość jest dostarczana w oktetach i musi być większa od zera (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Wartość domyślna to 4096.

### <a name="maximum-frame-size"></a>Maksymalny rozmiar klatki

`Http2.MaxFrameSize`wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer. Wartość jest dostarczana w oktetach i musi zawierać się w przedziale od 2^14 (16 384) do 2^24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Wartość domyślna to 2^14 (16 384).

### <a name="maximum-request-header-size"></a>Maksymalny rozmiar nagłówka żądania

`Http2.MaxRequestHeaderFieldSize`wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania. Ten limit ma zastosowanie zarówno do nazwy, jak i wartości w ich skompresowanych i nieskompresowanych reprezentacjach. Wartość musi być większa od zera (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Wartość domyślna to 8192.

### <a name="initial-connection-window-size"></a>Początkowy rozmiar okna połączenia

`Http2.InitialConnectionWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w tym samym czasie agregowane przez wszystkie żądania (strumienie) na połączenie. Liczba wniosków jest `Http2.InitialStreamWindowSize`ograniczona przez . Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Wartość domyślna to 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Początkowy rozmiar okna strumienia

`Http2.InitialStreamWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w jednym czasie na żądanie (strumień). Liczba wniosków jest `Http2.InitialConnectionWindowSize`ograniczona przez . Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Wartość domyślna to 96 KB (98 304).

### <a name="synchronous-io"></a>Synchroniczne operacje we/wy

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>określa, czy synchroniczne we/wy są dozwolone dla żądania i odpowiedzi. Wartością domyślną jest `false`.

> [!WARNING]
> Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do głodu puli wątków, co sprawia, że aplikacja nie odpowiada. Włącz `AllowSynchronousIO` tylko podczas korzystania z biblioteki, która nie obsługuje asynchronicznych we/wy.

Poniższy przykład umożliwia synchroniczne we/wy:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Aby uzyskać informacje o innych opcjach i ograniczeniach kestrel, zobacz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

Domyślnie ASP.NET Core wiąże się z:

* `http://localhost:5000`
* `https://localhost:5001`(gdy certyfikat rozwoju lokalnego jest obecny)

Określ adresy URL za pomocą:

* `ASPNETCORE_URLS`zmienną środowiskową.
* `--urls`argument wiersza polecenia.
* `urls`klucz konfiguracji hosta.
* `UseUrls`metody rozszerzenia.

Wartość podana przy użyciu tych metod może być co najmniej jeden punkt końcowy HTTP i HTTPS (HTTPS, jeśli dostępny jest domyślny certyfikat). Skonfiguruj wartość jako listę oddzieloną średnikami `"Urls": "http://localhost:8000;http://localhost:8001"`(na przykład ).

Aby uzyskać więcej informacji na temat tych metod, zobacz [Adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastępowanie konfiguracji](xref:fundamentals/host/web-host#override-configuration).

Tworzony jest certyfikat dewelopera:

* Po zainstalowaniu [pliku .NET Core SDK.](/dotnet/core/sdk)
* [Narzędzie dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.

Niektóre przeglądarki wymagają udzielenia jawnego uprawnienia do ufania certyfikatowi lokalnego rozwoju.

Szablony projektów domyślnie konfigurują aplikacje do uruchamiania na https i obejmują [przekierowanie HTTPS i obsługę HSTS](xref:security/enforcing-ssl).

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody, aby skonfigurować prefiksy adresów URL i porty dla Kestrel.

`UseUrls`argument `--urls` wiersza polecenia, `urls` klucz konfiguracji hosta i zmienna `ASPNETCORE_URLS` środowiskowa również działają, ale mają ograniczenia odnotowane w dalszej części tej sekcji (domyślny certyfikat musi być dostępny dla konfiguracji punktu końcowego HTTPS).

`KestrelServerOptions`Konfiguracji:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(>)\<

Określa konfigurację `Action` uruchamianą dla każdego określonego punktu końcowego. Wywołanie `ConfigureEndpointDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>Konfigurowanie>httpsdefaults(akcja\<httpsconnectionadapteroptions)

Określa konfigurację `Action` uruchamianą dla każdego punktu końcowego HTTPS. Wywołanie `ConfigureHttpsDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.

### <a name="configureiconfiguration"></a>Konfigurowanie(Konfiguracja IConfiguration)

Tworzy moduł ładujący konfiguracji do konfigurowania <xref:Microsoft.Extensions.Configuration.IConfiguration> Kestrel, który przyjmuje jako dane wejściowe. Konfiguracja musi być w zakresie do sekcji konfiguracji dla Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Skonfiguruj Kestrel do używania protokołu HTTPS.

`ListenOptions.UseHttps`Rozszerzenia:

* `UseHttps`&ndash; Skonfiguruj Kestrel do używania protokołu HTTPS z certyfikatem domyślnym. Zgłasza wyjątek, jeśli nie skonfigurowano certyfikatu domyślnego.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`Parametry:

* `filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.
* `password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.
* `configureOptions`jest `Action` skonfigurowaniem `HttpsConnectionAdapterOptions`pliku . Zwraca `ListenOptions`wartość .
* `storeName`to magazyn certyfikatów, z którego ma być załadowany certyfikat.
* `subject`jest nazwą podmiotu certyfikatu.
* `allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.
* `location`to lokalizacja magazynu, z wyładowania certyfikatu.
* `serverCertificate`to certyfikat X.509.

W produkcji protokół HTTPS musi być jawnie skonfigurowany. Należy co najmniej dostarczyć certyfikat domyślny.

Obsługiwane konfiguracje opisane dalej:

* Brak konfiguracji
* Zastępowanie certyfikatu domyślnego z konfiguracji
* Zmienianie wartości domyślnych w kodzie

*Brak konfiguracji*

Kestrel `http://localhost:5000` nasłuchuje `https://localhost:5001` i (jeśli dostępny jest domyślny certyfikat).

<a name="configuration"></a>

*Zastępowanie certyfikatu domyślnego z konfiguracji*

`CreateDefaultBuilder`domyślnie `Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel. Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel. Skonfiguruj wiele punktów końcowych, w tym adresy URL i certyfikaty do użycia, z pliku na dysku lub z magazynu certyfikatów.

W poniższym *przykładzie appsettings.json:*

* Ustaw **AllowInvalid,** aby `true` zezwolić na używanie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).
* Każdy punkt końcowy HTTPS, który nie określa certyfikatu **(HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowanego w obszarze **Domyślne** **certyfikaty** > lub certyfikat dewelopera.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternatywą dla **używania ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów. Na przykład certyfikat**domyślny** **certyfikat certyfikatów** > można określić jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Uwagi dotyczące schematu:

* Nazwy punktów końcowych są bez uwzględniania wielkości liter. Na przykład `HTTPS` `Https` i są prawidłowe.
* Parametr `Url` jest wymagany dla każdego punktu końcowego. Format dla tego parametru jest taki `Urls` sam jak parametr konfiguracji najwyższego poziomu, z tą różnicą, że jest on ograniczony do pojedynczej wartości.
* Te punkty końcowe zastępują te `Urls` zdefiniowane w konfiguracji najwyższego poziomu, a nie do ich dodawania. Punkty końcowe zdefiniowane `Listen` w kodzie za pośrednictwem kumulują się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.
* Sekcja `Certificate` jest opcjonalna. Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach. Jeśli nie są dostępne żadne ustawienia domyślne, serwer zgłasza wyjątek i nie można go uruchomić.
* Sekcja `Certificate` obsługuje **Path**&ndash;zarówno**certyfikaty Path Password, jak** i **Subject**&ndash;**Store.**
* Dowolna liczba punktów końcowych może być zdefiniowana w ten sposób, o ile nie powodują konfliktów portów.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` metodę, `.Endpoint(string name, listenOptions => { })` która może służyć do uzupełniania ustawień skonfigurowanego punktu końcowego:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do dalszej iteracji na istniejącym ładowarce, takim jak ten dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>program .

* Sekcja konfiguracji dla każdego punktu końcowego jest `Endpoint` dostępna na opcje w metodzie, dzięki czemu ustawienia niestandardowe mogą być odczytywane.
* Wiele konfiguracji może być `options.Configure(context.Configuration.GetSection("{SECTION}"))` ładowanych przez ponowne wywołanie z inną sekcją. Używana jest tylko ostatnia `Load` konfiguracja, chyba że jest jawnie wywoływana w poprzednich wystąpieniach. Metapakiet nie jest `Load` wywoływany, więc jego domyślna sekcja konfiguracji może zostać zastąpiona.
* `KestrelConfigurationLoader`dubluje `Listen` rodzinę interfejsów `KestrelServerOptions` `Endpoint` API z jako przeciążenia, więc kod i config punktów końcowych mogą być skonfigurowane w tym samym miejscu. Te przeciążenia nie używają nazw i zużywają tylko ustawienia domyślne z konfiguracji.

*Zmienianie wartości domyślnych w kodzie*

`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany `ListenOptions` `HttpsConnectionAdapterOptions`ustawień domyślnych dla i , w tym zastąpienie domyślnego certyfikatu określonego w poprzednim scenariuszu. `ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

*Obsługa pustułki dla SNI*

[Wskazanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do obsługi wielu domen na tym samym adresie IP i porcie. Aby usługa SNI działała, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania protokołu TLS, aby serwer mógł dostarczyć poprawny certyfikat. Klient używa dostarczonego certyfikatu do szyfrowanej komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu TLS.

Kestrel obsługuje SNI `ServerCertificateSelector` za pomocą wywołania zwrotnego. Wywołanie zwrotne jest wywoływane raz na połączenie, aby umożliwić aplikacji sprawdzenie nazwy hosta i wybranie odpowiedniego certyfikatu.

Obsługa SNI wymaga:

* Uruchamianie w `netcoreapp2.1` ramach docelowej lub nowszej. Włączone `net461` lub późniejsze wywołanie zwrotne `name` jest `null`wywoływane, ale jest zawsze . Jest `name` również, `null` jeśli klient nie udostępnia parametru nazwy hosta w uzgadnianiu TLS.
* Wszystkie strony internetowe działają w tym samym wystąpieniu Kestrel. Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez odwrotnego serwera proxy.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a>Rejestrowanie połączeń

Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> emitowania dzienników poziomu debugowania dla komunikacji na poziomie bajtów w połączeniu. Rejestrowanie połączeń jest przydatne do rozwiązywania problemów w komunikacji niskiego poziomu, takich jak podczas szyfrowania TLS i za serwerami proxy. Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony przed , zaszyfrowany ruch jest rejestrowany. Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony po , odszyfrowany ruch jest rejestrowany.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Powiązanie z gniazdem TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X.509:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

W przykładzie konfiguruje protokół <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS dla punktu końcowego za pomocą pliku . Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Powiązanie z gniazdem Unixa

Słuchaj na gnieździe Unix z <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lepszej wydajności z Nginx, jak pokazano w tym przykładzie:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* W pliku konfiguracyjnym `server`  >  `location`  >  `proxy_pass` Nginx ustaw wpis na `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`jest nazwą gniazda dostarczonego <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (na `kestrel-test.sock` przykład w poprzednim przykładzie).
* Upewnij się, że gniazdo jest zapisywalne przez `chmod go+w /tmp/kestrel-test.sock`Nginx (na przykład).

### <a name="port-0"></a>Port 0

Po określeniu numeru `0` portu Kestrel dynamicznie wiąże się z dostępnym portem. W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie związany w czasie wykonywania:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, do którego można uzyskać kontakt z aplikacją:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Ograniczenia

Konfigurowanie punktów końcowych przy następujących podejściach:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument wiersza polecenia
* `urls`klucz konfiguracji hosta
* `ASPNETCORE_URLS`zmienna środowiskowa

Te metody są przydatne do tworzenia kodu pracy z serwerami innymi niż Kestrel. Należy jednak pamiętać o następujących ograniczeniach:

* Protokołu HTTPS nie można używać z tymi podejściami, chyba że domyślny certyfikat jest `KestrelServerOptions` podany w konfiguracji punktu końcowego HTTPS (na przykład przy użyciu konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).
* Gdy zarówno `Listen` `UseUrls` i podejścia są używane `Listen` jednocześnie, punkty końcowe zastępują punkty `UseUrls` końcowe.

### <a name="iis-endpoint-configuration"></a>Konfiguracja punktu końcowego usług IIS

Podczas korzystania z usługi IIS powiązania adresów URL dla powiązań `Listen` zastępowania usługi IIS są ustawiane przez jedną lub `UseUrls`. Aby uzyskać więcej informacji, zobacz [ASP.NET temat modułu podstawowego.](xref:host-and-deploy/aspnet-core-module)

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

Właściwość `Protocols` ustanawia protokoły HTTP`HttpProtocols`( ) włączone w punkcie końcowym połączenia lub dla serwera. Przypisz wartość `Protocols` do właściwości `HttpProtocols` z wyliczenia.

| `HttpProtocols`wartość wyliczenia | Dozwolony protokół połączenia |
| -------------------------- | ----------------------------- |
| `Http1`                    | Tylko http/1.1. Może być używany z tls lub bez. |
| `Http2`                    | Tylko HTTP/2. Może być używany bez protokołu TLS tylko wtedy, gdy klient obsługuje [tryb wcześniejszej wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 i HTTP/2. Protokół HTTP/2 wymaga, aby klient wybrał protokół HTTP/2 w uzgadnianiu [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) protokołu TLS; w przeciwnym razie połączenie jest domyślnie dozwolone na HTTP/1.1. |

Wartością `ListenOptions.Protocols` domyślną dla `HttpProtocols.Http1AndHttp2`dowolnego punktu końcowego jest .

Ograniczenia TLS dla protokołu HTTP/2:

* TLS w wersji 1.2 lub nowszej
* Renegocjacja wyłączona
* Kompresja wyłączona
* Minimalne rozmiary efemerycznej wymiany kluczy:
  * Krzywa eliptyczna Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minimum
  * Pole skończone Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimum 2048 bitów
* Pakiet szyfrowania nie znajduje się na czarnej liście

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` z krzywą &rbrack; eliptyczną P-256 jest domyślnie obsługiwana. `TLS-ECDHE` &rbrack;

Poniższy przykład zezwala na połączenia HTTP/1.1 i HTTP/2 na porcie 8000. Połączenia są zabezpieczone tls z dostarczonym certyfikatem:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Użyj oprogramowania pośredniczącego połączenia, aby w razie potrzeby filtrować uzgadniania TLS dla poszczególnych szyfrów.

Poniższy przykład <xref:System.NotSupportedException> jest rzuty dla dowolnego algorytmu szyfrowania, który aplikacja nie obsługuje. Alternatywnie zdefiniuj i porównaj [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) z listą dopuszczalnych mechanizmów szyfrowania.

Szyfrowanie nie jest używane z algorytmem szyfrowania [CipherAlgorithmType.Null.](xref:System.Security.Authentication.CipherAlgorithmType)

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

Filtrowanie połączeń można również skonfigurować za pomocą <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

W systemie <xref:System.Net.Security.CipherSuitesPolicy> Linux, może służyć do filtrowania uzgadniania TLS na podstawie połączenia:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

*Ustawianie protokołu z konfiguracji*

`CreateDefaultBuilder`domyślnie `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.

Poniższy przykład *appsettings.json* ustanawia protokół HTTP/1.1 jako domyślny protokół połączenia dla wszystkich punktów końcowych:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Poniższy przykład *appsettings.json* ustanawia protokół połączenia HTTP/1.1 dla określonego punktu końcowego:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Protokoły określone w kodzie zastępują wartości ustawione przez konfigurację.

## <a name="transport-configuration"></a>Konfiguracja transportu

W przypadku projektów wymagających stosowania<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>leku Libuv ( ):

* Dodaj zależność pakietu [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* Zadzwoń <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na: `IWebHostBuilder`

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a>Prefiksy adresów URL

W `UseUrls`przypadku `--urls` korzystania z `urls` argumentu wiersza `ASPNETCORE_URLS` polecenia, klucza konfiguracji hosta lub zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w dowolnym z następujących formatów.

Tylko prefiksy adresów URL HTTP są prawidłowe. Pustułka nie obsługuje protokołu HTTPS podczas `UseUrls`konfigurowania powiązań adresów URL przy użyciu pliku .

* Adres IPv4 z numerem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`jest specjalnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.

* Adres IPv6 z numerem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`jest odpowiednikiem IPv6 IPv4 `0.0.0.0`.

* Nazwa hosta z numerem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Nazwy hostów `*` `+`, i , nie są specjalne. Wszystko, co nie jest `localhost` rozpoznawane jako prawidłowy adres IP lub wiąże się ze wszystkimi adresami IPv4 i IPv6. Aby powiązać różne nazwy hostów z różnymi aplikacjami ASP.NET Core na tym samym porcie, użyj [protokołu HTTP.sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.

  > [!WARNING]
  > Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).

* Nazwa `localhost` hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Po `localhost` określeniu Kestrel próbuje powiązać interfejsy sprzężenia zwrotnego IPv4 i IPv6. Jeśli żądany port jest używany przez inną usługę w interfejsie sprzężenia zwrotnego, kestrel nie może się uruchomić. Jeśli którykolwiek z interfejsów sprzężenia zwrotnego jest niedostępny z jakiegokolwiek innego powodu (najczęściej dlatego, że IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.

## <a name="host-filtering"></a>Filtrowanie hostów

Gdy Kestrel obsługuje konfigurację opartą `http://example.com:5000`na prefiksach, takich jak Kestrel, w dużej mierze ignoruje nazwę hosta. Host `localhost` jest specjalnym przypadkiem używanym do wiązania adresów sprzężenia zwrotnego. Każdy host inny niż jawny adres IP wiąże się ze wszystkimi publicznymi adresami IP. `Host`nagłówki nie są sprawdzane.

Aby obejść ten problem, użyj oprogramowania pośredniczącego filtrowania hostów. Oprogramowanie pośredniczące filtrowania hosta jest dostarczane przez pakiet [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) który jest niejawnie przewidziany dla aplikacji ASP.NET Core. Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>jest dodawane przez , który wywołuje: <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Oprogramowanie pośredniczące filtrowania hosta jest domyślnie wyłączone. Aby włączyć oprogramowanie pośredniczące, `AllowedHosts` zdefiniuj klucz w *appsettings.json*/*appsettings.\< EnvironmentName>.json*. Wartość jest listą nazw hostów rozdzielanych średnikami bez numerów portów:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Oprogramowanie pośredniczące nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) również ma <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję. Oprogramowanie pośredniczące nagłówków przesyłanych dalej i oprogramowanie pośredniczące filtrowania hostów mają podobną funkcjonalność w różnych scenariuszach. Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego `Host` Nagłówków przesyłanych dalej jest właściwe, gdy nagłówek nie jest zachowywany podczas przekazywania żądań za pomocą serwera odwrotnego serwera proxy lub modułu równoważenia obciążenia. Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego filtrowania hostów jest właściwe, gdy `Host` Kestrel jest używany jako serwer brzegowy skierowany do publicznej wiadomości lub gdy nagłówek jest bezpośrednio przekazywaniem dalej.
>
> Aby uzyskać więcej informacji na temat <xref:host-and-deploy/proxy-load-balancer>oprogramowania pośredniczącego nagłówków przesyłanych dalej, zobacz .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Pustułka to wieloplatformowy [serwer www dla ASP.NET Core.](xref:fundamentals/servers/index) Pustułka to serwer www, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.

Pustułka obsługuje następujące scenariusze:

* HTTPS
* Nieprzezroczyste uaktualnienie używane do włączania [websockets](https://github.com/aspnet/websockets)
* Gniazda Unix o wysokiej wydajności za Nginx
* HTTP/2 (z wyjątkiem&dagger;systemu macOS)

&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.

Pustułka jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez program .NET Core.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* System operacyjny&dagger;
  * Windows Server 2016/Windows 10 lub nowsze&Dagger;
  * Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16.04 lub nowszym)
* Struktura docelowa: .NET Core 2.2 lub nowsza
* Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1.2 lub nowsze

&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.
&Dagger;Pustułka ma ograniczoną obsługę protokołu HTTP/2 w systemach Windows Server 2012 R2 i Windows 8.1. Obsługa jest ograniczona, ponieważ lista obsługiwanych pakietów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona. Certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego krzywej eliptycznej (ECDSA) może być wymagany do zabezpieczenia połączeń TLS.

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.

Protokół HTTP/2 jest domyślnie wyłączony. Aby uzyskać więcej informacji na temat konfiguracji, zobacz [opcje Kestrel](#kestrel-options) i [ListenOptions.Protocols](#listenoptionsprotocols) sekcje.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kiedy używać Kestrel z odwrotnym serwerem proxy

Pustułka może być używana samodzielnie lub z *odwrotnym serwerem proxy,* takim jak [Internetowe Usługi Informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/). Serwer odwrotnego serwera proxy odbiera żądania HTTP z sieci i przekazuje je do Kestrel.

Pustułka używana jako serwer internetowy edge (web-facing):

![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

Pustułka używana w konfiguracji odwrotnego serwera proxy:

![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

Konfiguracja z odwrotnym serwerem proxy lub bez niego jest obsługiwaną konfiguracją hostingu.

Pustułka używana jako serwer brzegowy bez odwrotnego serwera proxy nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami. Gdy Kestrel jest skonfigurowany do nasłuchiwać na porcie, Kestrel obsługuje `Host` cały ruch dla tego portu, niezależnie od nagłówków żądań. Odwrotny serwer proxy, który może udostępniać porty, ma możliwość przesyłania dalej żądań do Kestrel na unikatowym adresie IP i porcie.

Nawet jeśli serwer odwrotnego serwera proxy nie jest wymagany, dobrym wyborem może być użycie odwrotnego serwera proxy.

Odwrotny serwer proxy:

* Można ograniczyć narażony obszar powierzchni publicznej aplikacji, które obsługuje.
* Zapewnij dodatkową warstwę konfiguracji i obrony.
* Może lepiej zintegrować się z istniejącą infrastrukturą.
* Uprość równoważenie obciążenia i bezpieczną komunikację (HTTPS). Tylko serwer odwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.

> [!WARNING]
> Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Jak używać Kestrel w aplikacjach ASP.NET Core

Pakiet [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

ASP.NET szablony projektów Core domyślnie używają Pustułki. W *Program.cs*, wywoła kod <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>szablonu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , który wywołuje za kulisami.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Aby uzyskać `CreateDefaultBuilder` więcej informacji na temat i tworzenia hosta, zobacz *sekcję Konfigurowanie hosta* w programie <xref:fundamentals/host/web-host#set-up-a-host>.

Aby zapewnić dodatkową `CreateDefaultBuilder`konfigurację `ConfigureKestrel`po wywołaniu , użyj :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Jeśli aplikacja nie `CreateDefaultBuilder` wywołuje, aby skonfigurować <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> hosta, zadzwoń **przed** wywołaniem: `ConfigureKestrel`

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a>Opcje pustułki

Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w wdrożeniach internetowych.

Ustaw ograniczenia właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy. Właściwość `Limits` zawiera wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.

W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obszaru nazw:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Opcje pustułki, które są skonfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index). Na przykład dostawca konfiguracji plików może załadować konfigurację kestrelu z *pliku appsettings.json* lub *appsettings.{ Środowisko}.json* plik:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

Użyj **jednego** z następujących podejść:

* Skonfiguruj `Startup.ConfigureServices`Pustułkę w :

  1. Wstrzyknąć `IConfiguration` `Startup` wystąpienie do klasy. W poniższym przykładzie przyjęto założenie, że `Configuration` wstrzyknięta konfiguracja jest przypisana do właściwości.
  2. W `Startup.ConfigureServices`programie `Kestrel` załaduj sekcję konfiguracji do konfiguracji Kestrela:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Konfigurowanie Pustułki podczas tworzenia hosta:

  W *Program.cs*załaduj `Kestrel` sekcję konfiguracji do konfiguracji Kestrela:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

Oba powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji.](xref:fundamentals/configuration/index)

### <a name="keep-alive-timeout"></a>Limit czasu utrzymywanie aktywności przy życiu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Pobiera lub ustawia [limit czasu keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). Wartość domyślna to 2 minuty.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>Maksymalna liczba połączeń klientów

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maksymalną liczbę równoczesnych otwartych połączeń TCP można ustawić dla całej aplikacji za pomocą następującego kodu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Istnieje oddzielny limit dla połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu WebSockets). Po uaktualnieniu połączenia nie jest wliczane `MaxConcurrentConnections` do limitu.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maksymalna liczba połączeń jest domyślnie nieograniczona (zerowa).

### <a name="maximum-request-body-size"></a>Maksymalny rozmiar treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.

Zalecane podejście do zastąpienia limitu w ASP.NET Core MVC aplikacji jest <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji na każde żądanie:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Zastąp ustawienie dla określonego żądania w oprogramowaniu pośredniczącym:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Wyjątek jest zgłaszany, jeśli aplikacja konfiguruje limit żądania po aplikacji zaczął odczytywać żądanie. Istnieje `IsReadOnly` właściwość, która wskazuje, `MaxRequestBodySize` czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Gdy aplikacja jest prowadzona [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [Core Module](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączona, ponieważ iIS już ustawia limit.

### <a name="minimum-request-body-data-rate"></a>Minimalna szybkość danych treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Pustułka sprawdza co sekundę, czy dane są przychodzące z określoną szybkością w bajtach/sekundę. Jeśli szybkość spadnie poniżej minimum, upłynie limit czasu połączenia. Okres prolongaty to czas, który Kestrel daje klientowi na zwiększenie szybkości wysyłania do minimum; stawka nie jest sprawdzana w tym czasie. Okres prolongaty pomaga uniknąć porzucania połączeń, które początkowo wysyłają dane z powolnym tempem ze względu na spowolnienie tcp.

Domyślna minimalna stawka wynosi 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.

Minimalna stawka ma również zastosowanie do odpowiedzi. Kod, aby ustawić limit żądania i limit odpowiedzi `RequestBody` jest `Response` taka sama, z wyjątkiem posiadania lub w nazwy właściwości i interfejsu.

Oto przykład, który pokazuje, jak skonfigurować minimalne szybkości transmisji danych w *Program.cs:*

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Zastąp minimalne limity szybkości na żądanie w oprogramowaniu pośredniczącym:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Żadna z funkcji rate, do których `HttpContext.Features` odwołuje się w poprzednim przykładzie, nie jest obecna w przypadku żądań HTTP/2, ponieważ modyfikowanie limitów szybkości dla na żądanie nie jest obsługiwane dla protokołu HTTP/2 ze względu na obsługę protokołu dla multipleksowania żądań. Limity szybkości dla całego `KestrelServerOptions.Limits` serwera skonfigurowane za pośrednictwem nadal mają zastosowanie zarówno do połączeń HTTP/1.x, jak i HTTP/2.

### <a name="request-headers-timeout"></a>Limit czasu nagłówków żądań

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Pobiera lub ustawia maksymalną ilość czasu serwer spędza odbieranie nagłówków żądań. Wartość domyślna to 30 sekund.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Maksymalna liczba strumieni na połączenie

`Http2.MaxStreamsPerConnection`ogranicza liczbę strumieni żądań równoczesnych na połączenie HTTP/2. Nadmiar strumieni są odrzucane.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

Wartość domyślna to 100.

### <a name="header-table-size"></a>Rozmiar tabeli nagłówka

Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2. `Http2.HeaderTableSize`ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK. Wartość jest dostarczana w oktetach i musi być większa od zera (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

Wartość domyślna to 4096.

### <a name="maximum-frame-size"></a>Maksymalny rozmiar klatki

`Http2.MaxFrameSize`wskazuje maksymalny rozmiar ładunku ramki połączenia HTTP/2 do odebrania. Wartość jest dostarczana w oktetach i musi zawierać się w przedziale od 2^14 (16 384) do 2^24-1 (16 777 215).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

Wartość domyślna to 2^14 (16 384).

### <a name="maximum-request-header-size"></a>Maksymalny rozmiar nagłówka żądania

`Http2.MaxRequestHeaderFieldSize`wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania. Ten limit ma zastosowanie zarówno do nazwy, jak i wartości razem w ich skompresowanych i nieskompresowanych reprezentacjach. Wartość musi być większa od zera (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

Wartość domyślna to 8192.

### <a name="initial-connection-window-size"></a>Początkowy rozmiar okna połączenia

`Http2.InitialConnectionWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w tym samym czasie agregowane przez wszystkie żądania (strumienie) na połączenie. Liczba wniosków jest `Http2.InitialStreamWindowSize`ograniczona przez . Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Wartość domyślna to 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Początkowy rozmiar okna strumienia

`Http2.InitialStreamWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w jednym czasie na żądanie (strumień). Liczba wniosków jest `Http2.InitialStreamWindowSize`ograniczona przez . Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

Wartość domyślna to 96 KB (98 304).

### <a name="synchronous-io"></a>Synchroniczne operacje we/wy

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>określa, czy synchroniczne we/wy są dozwolone dla żądania i odpowiedzi. Wartością domyślną jest `true`.

> [!WARNING]
> Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do głodu puli wątków, co sprawia, że aplikacja nie odpowiada. Włącz `AllowSynchronousIO` tylko podczas korzystania z biblioteki, która nie obsługuje asynchronicznych we/wy.

Poniższy przykład umożliwia synchroniczne we/wy:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Aby uzyskać informacje o innych opcjach i ograniczeniach kestrel, zobacz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

Domyślnie ASP.NET Core wiąże się z:

* `http://localhost:5000`
* `https://localhost:5001`(gdy certyfikat rozwoju lokalnego jest obecny)

Określ adresy URL za pomocą:

* `ASPNETCORE_URLS`zmienną środowiskową.
* `--urls`argument wiersza polecenia.
* `urls`klucz konfiguracji hosta.
* `UseUrls`metody rozszerzenia.

Wartość podana przy użyciu tych metod może być co najmniej jeden punkt końcowy HTTP i HTTPS (HTTPS, jeśli dostępny jest domyślny certyfikat). Skonfiguruj wartość jako listę oddzieloną średnikami `"Urls": "http://localhost:8000;http://localhost:8001"`(na przykład ).

Aby uzyskać więcej informacji na temat tych metod, zobacz [Adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastępowanie konfiguracji](xref:fundamentals/host/web-host#override-configuration).

Tworzony jest certyfikat dewelopera:

* Po zainstalowaniu [pliku .NET Core SDK.](/dotnet/core/sdk)
* [Narzędzie dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.

Niektóre przeglądarki wymagają udzielenia jawnego uprawnienia do ufania certyfikatowi lokalnego rozwoju.

Szablony projektów domyślnie konfigurują aplikacje do uruchamiania na https i obejmują [przekierowanie HTTPS i obsługę HSTS](xref:security/enforcing-ssl).

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody, aby skonfigurować prefiksy adresów URL i porty dla Kestrel.

`UseUrls`argument `--urls` wiersza polecenia, `urls` klucz konfiguracji hosta i zmienna `ASPNETCORE_URLS` środowiskowa również działają, ale mają ograniczenia odnotowane w dalszej części tej sekcji (domyślny certyfikat musi być dostępny dla konfiguracji punktu końcowego HTTPS).

`KestrelServerOptions`Konfiguracji:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(>)\<

Określa konfigurację `Action` uruchamianą dla każdego określonego punktu końcowego. Wywołanie `ConfigureEndpointDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>Konfigurowanie>httpsdefaults(akcja\<httpsconnectionadapteroptions)

Określa konfigurację `Action` uruchamianą dla każdego punktu końcowego HTTPS. Wywołanie `ConfigureHttpsDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.


### <a name="configureiconfiguration"></a>Konfigurowanie(Konfiguracja IConfiguration)

Tworzy moduł ładujący konfiguracji do konfigurowania <xref:Microsoft.Extensions.Configuration.IConfiguration> Kestrel, który przyjmuje jako dane wejściowe. Konfiguracja musi być w zakresie do sekcji konfiguracji dla Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Skonfiguruj Kestrel do używania protokołu HTTPS.

`ListenOptions.UseHttps`Rozszerzenia:

* `UseHttps`&ndash; Skonfiguruj Kestrel do używania protokołu HTTPS z certyfikatem domyślnym. Zgłasza wyjątek, jeśli nie skonfigurowano certyfikatu domyślnego.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`Parametry:

* `filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.
* `password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.
* `configureOptions`jest `Action` skonfigurowaniem `HttpsConnectionAdapterOptions`pliku . Zwraca `ListenOptions`wartość .
* `storeName`to magazyn certyfikatów, z którego ma być załadowany certyfikat.
* `subject`jest nazwą podmiotu certyfikatu.
* `allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.
* `location`to lokalizacja magazynu, z wyładowania certyfikatu.
* `serverCertificate`to certyfikat X.509.

W produkcji protokół HTTPS musi być jawnie skonfigurowany. Należy co najmniej dostarczyć certyfikat domyślny.

Obsługiwane konfiguracje opisane dalej:

* Brak konfiguracji
* Zastępowanie certyfikatu domyślnego z konfiguracji
* Zmienianie wartości domyślnych w kodzie

*Brak konfiguracji*

Kestrel `http://localhost:5000` nasłuchuje `https://localhost:5001` i (jeśli dostępny jest domyślny certyfikat).

<a name="configuration"></a>

*Zastępowanie certyfikatu domyślnego z konfiguracji*

`CreateDefaultBuilder`domyślnie `Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel. Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel. Skonfiguruj wiele punktów końcowych, w tym adresy URL i certyfikaty do użycia, z pliku na dysku lub z magazynu certyfikatów.

W poniższym *przykładzie appsettings.json:*

* Ustaw **AllowInvalid,** aby `true` zezwolić na używanie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).
* Każdy punkt końcowy HTTPS, który nie określa certyfikatu **(HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowanego w obszarze **Domyślne** **certyfikaty** > lub certyfikat dewelopera.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternatywą dla **używania ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów. Na przykład certyfikat**domyślny** **certyfikat certyfikatów** > można określić jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Uwagi dotyczące schematu:

* Nazwy punktów końcowych są bez uwzględniania wielkości liter. Na przykład `HTTPS` `Https` i są prawidłowe.
* Parametr `Url` jest wymagany dla każdego punktu końcowego. Format dla tego parametru jest taki `Urls` sam jak parametr konfiguracji najwyższego poziomu, z tą różnicą, że jest on ograniczony do pojedynczej wartości.
* Te punkty końcowe zastępują te `Urls` zdefiniowane w konfiguracji najwyższego poziomu, a nie do ich dodawania. Punkty końcowe zdefiniowane `Listen` w kodzie za pośrednictwem kumulują się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.
* Sekcja `Certificate` jest opcjonalna. Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach. Jeśli nie są dostępne żadne ustawienia domyślne, serwer zgłasza wyjątek i nie można go uruchomić.
* Sekcja `Certificate` obsługuje **Path**&ndash;zarówno**certyfikaty Path Password, jak** i **Subject**&ndash;**Store.**
* Dowolna liczba punktów końcowych może być zdefiniowana w ten sposób, o ile nie powodują konfliktów portów.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` metodę, `.Endpoint(string name, listenOptions => { })` która może służyć do uzupełniania ustawień skonfigurowanego punktu końcowego:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do dalszej iteracji na istniejącym ładowarce, takim jak ten dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>program .

* Sekcja konfiguracji dla każdego punktu końcowego jest `Endpoint` dostępna na opcje w metodzie, dzięki czemu ustawienia niestandardowe mogą być odczytywane.
* Wiele konfiguracji może być `options.Configure(context.Configuration.GetSection("{SECTION}"))` ładowanych przez ponowne wywołanie z inną sekcją. Używana jest tylko ostatnia `Load` konfiguracja, chyba że jest jawnie wywoływana w poprzednich wystąpieniach. Metapakiet nie jest `Load` wywoływany, więc jego domyślna sekcja konfiguracji może zostać zastąpiona.
* `KestrelConfigurationLoader`dubluje `Listen` rodzinę interfejsów `KestrelServerOptions` `Endpoint` API z jako przeciążenia, więc kod i config punktów końcowych mogą być skonfigurowane w tym samym miejscu. Te przeciążenia nie używają nazw i zużywają tylko ustawienia domyślne z konfiguracji.

*Zmienianie wartości domyślnych w kodzie*

`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany `ListenOptions` `HttpsConnectionAdapterOptions`ustawień domyślnych dla i , w tym zastąpienie domyślnego certyfikatu określonego w poprzednim scenariuszu. `ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

*Obsługa pustułki dla SNI*

[Wskazanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do obsługi wielu domen na tym samym adresie IP i porcie. Aby usługa SNI działała, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania protokołu TLS, aby serwer mógł dostarczyć poprawny certyfikat. Klient używa dostarczonego certyfikatu do szyfrowanej komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu TLS.

Kestrel obsługuje SNI `ServerCertificateSelector` za pomocą wywołania zwrotnego. Wywołanie zwrotne jest wywoływane raz na połączenie, aby umożliwić aplikacji sprawdzenie nazwy hosta i wybranie odpowiedniego certyfikatu.

Obsługa SNI wymaga:

* Uruchamianie w `netcoreapp2.1` ramach docelowej lub nowszej. Włączone `net461` lub późniejsze wywołanie zwrotne `name` jest `null`wywoływane, ale jest zawsze . Jest `name` również, `null` jeśli klient nie udostępnia parametru nazwy hosta w uzgadnianiu TLS.
* Wszystkie strony internetowe działają w tym samym wystąpieniu Kestrel. Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez odwrotnego serwera proxy.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a>Rejestrowanie połączeń

Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> emitowania dzienników poziomu debugowania dla komunikacji na poziomie bajtów w połączeniu. Rejestrowanie połączeń jest przydatne do rozwiązywania problemów w komunikacji niskiego poziomu, takich jak podczas szyfrowania TLS i za serwerami proxy. Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony przed , zaszyfrowany ruch jest rejestrowany. Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony po , odszyfrowany ruch jest rejestrowany.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Powiązanie z gniazdem TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X.509:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

W przykładzie konfiguruje protokół <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS dla punktu końcowego za pomocą pliku . Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Powiązanie z gniazdem Unixa

Słuchaj na gnieździe Unix z <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lepszej wydajności z Nginx, jak pokazano w tym przykładzie:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* W pliku nginx confiuguration `server`  >  `location`  >  `proxy_pass` ustaw `http://unix:/tmp/{KESTREL SOCKET}:/;`wpis na . `{KESTREL SOCKET}`jest nazwą gniazda dostarczonego <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (na `kestrel-test.sock` przykład w poprzednim przykładzie).
* Upewnij się, że gniazdo jest zapisywalne przez `chmod go+w /tmp/kestrel-test.sock`Nginx (na przykład). 

### <a name="port-0"></a>Port 0

Po określeniu numeru `0` portu Kestrel dynamicznie wiąże się z dostępnym portem. W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie związany w czasie wykonywania:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, do którego można uzyskać kontakt z aplikacją:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Ograniczenia

Konfigurowanie punktów końcowych przy następujących podejściach:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument wiersza polecenia
* `urls`klucz konfiguracji hosta
* `ASPNETCORE_URLS`zmienna środowiskowa

Te metody są przydatne do tworzenia kodu pracy z serwerami innymi niż Kestrel. Należy jednak pamiętać o następujących ograniczeniach:

* Protokołu HTTPS nie można używać z tymi podejściami, chyba że domyślny certyfikat jest `KestrelServerOptions` podany w konfiguracji punktu końcowego HTTPS (na przykład przy użyciu konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).
* Gdy zarówno `Listen` `UseUrls` i podejścia są używane `Listen` jednocześnie, punkty końcowe zastępują punkty `UseUrls` końcowe.

### <a name="iis-endpoint-configuration"></a>Konfiguracja punktu końcowego usług IIS

Podczas korzystania z usługi IIS powiązania adresów URL dla powiązań `Listen` zastępowania usługi IIS są ustawiane przez jedną lub `UseUrls`. Aby uzyskać więcej informacji, zobacz [ASP.NET temat modułu podstawowego.](xref:host-and-deploy/aspnet-core-module)

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

Właściwość `Protocols` ustanawia protokoły HTTP`HttpProtocols`( ) włączone w punkcie końcowym połączenia lub dla serwera. Przypisz wartość `Protocols` do właściwości `HttpProtocols` z wyliczenia.

| `HttpProtocols`wartość wyliczenia | Dozwolony protokół połączenia |
| -------------------------- | ----------------------------- |
| `Http1`                    | Tylko http/1.1. Może być używany z tls lub bez. |
| `Http2`                    | Tylko HTTP/2. Może być używany bez protokołu TLS tylko wtedy, gdy klient obsługuje [tryb wcześniejszej wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 i HTTP/2. Protokół HTTP/2 wymaga połączenia TLS i negocjacji [protokołu warstwy aplikacji (ALPN);](https://tools.ietf.org/html/rfc7301#section-3) w przeciwnym razie połączenie jest domyślnie dozwolone na HTTP/1.1. |

Domyślnym protokołem jest HTTP/1.1.

Ograniczenia TLS dla protokołu HTTP/2:

* TLS w wersji 1.2 lub nowszej
* Renegocjacja wyłączona
* Kompresja wyłączona
* Minimalne rozmiary efemerycznej wymiany kluczy:
  * Krzywa eliptyczna Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minimum
  * Pole skończone Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimum 2048 bitów
* Pakiet szyfrowania nie znajduje się na czarnej liście

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` z krzywą &rbrack; eliptyczną P-256 jest domyślnie obsługiwana. `TLS-ECDHE` &rbrack;

Poniższy przykład zezwala na połączenia HTTP/1.1 i HTTP/2 na porcie 8000. Połączenia są zabezpieczone tls z dostarczonym certyfikatem:

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Opcjonalnie utwórz `IConnectionAdapter` implementację do filtrowania uzgadniania TLS na podstawie połączenia dla określonych szyfrów:

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

*Ustawianie protokołu z konfiguracji*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>domyślnie `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.

W poniższym przykładzie *appsettings.json* dla wszystkich punktów końcowych Kestrel ustanawia się domyślny protokół połączenia (HTTP/1.1 i HTTP/2):

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

Poniższy przykład pliku konfiguracji ustanawia protokół połączenia dla określonego punktu końcowego:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

Protokoły określone w kodzie zastępują wartości ustawione przez konfigurację.

## <a name="transport-configuration"></a>Konfiguracja transportu

Wraz z wydaniem ASP.NET Core 2.1 domyślny transport Kestrel nie jest już oparty na Libuv, ale zamiast tego opiera się na zarządzanych gniazdach. Jest to przełomowa zmiana dla ASP.NET aplikacje Core 2.0 uaktualniania <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> do 2.1, które wywołują i zależą od jednego z następujących pakietów:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)
* [Aplikacja Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

W przypadku projektów, które wymagają użycia libuv:

* Dodaj zależność pakietu [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Zadzwoń <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a>Prefiksy adresów URL

W `UseUrls`przypadku `--urls` korzystania z `urls` argumentu wiersza `ASPNETCORE_URLS` polecenia, klucza konfiguracji hosta lub zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w dowolnym z następujących formatów.

Tylko prefiksy adresów URL HTTP są prawidłowe. Pustułka nie obsługuje protokołu HTTPS podczas `UseUrls`konfigurowania powiązań adresów URL przy użyciu pliku .

* Adres IPv4 z numerem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`jest specjalnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.

* Adres IPv6 z numerem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`jest odpowiednikiem IPv6 IPv4 `0.0.0.0`.

* Nazwa hosta z numerem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Nazwy hostów `*` `+`, i , nie są specjalne. Wszystko, co nie jest `localhost` rozpoznawane jako prawidłowy adres IP lub wiąże się ze wszystkimi adresami IPv4 i IPv6. Aby powiązać różne nazwy hostów z różnymi aplikacjami ASP.NET Core na tym samym porcie, użyj [protokołu HTTP.sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.

  > [!WARNING]
  > Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).

* Nazwa `localhost` hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Po `localhost` określeniu Kestrel próbuje powiązać interfejsy sprzężenia zwrotnego IPv4 i IPv6. Jeśli żądany port jest używany przez inną usługę w interfejsie sprzężenia zwrotnego, kestrel nie może się uruchomić. Jeśli którykolwiek z interfejsów sprzężenia zwrotnego jest niedostępny z jakiegokolwiek innego powodu (najczęściej dlatego, że IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.

## <a name="host-filtering"></a>Filtrowanie hostów

Gdy Kestrel obsługuje konfigurację opartą `http://example.com:5000`na prefiksach, takich jak Kestrel, w dużej mierze ignoruje nazwę hosta. Host `localhost` jest specjalnym przypadkiem używanym do wiązania adresów sprzężenia zwrotnego. Każdy host inny niż jawny adres IP wiąże się ze wszystkimi publicznymi adresami IP. `Host`nagłówki nie są sprawdzane.

Aby obejść ten problem, użyj oprogramowania pośredniczącego filtrowania hostów. Oprogramowanie pośredniczące filtrowania hostów jest dostarczane przez pakiet [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub 2.2). Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>jest dodawane przez , który wywołuje: <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Oprogramowanie pośredniczące filtrowania hosta jest domyślnie wyłączone. Aby włączyć oprogramowanie pośredniczące, `AllowedHosts` zdefiniuj klucz w *appsettings.json*/*appsettings.\< EnvironmentName>.json*. Wartość jest listą nazw hostów rozdzielanych średnikami bez numerów portów:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Oprogramowanie pośredniczące nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) również ma <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję. Oprogramowanie pośredniczące nagłówków przesyłanych dalej i oprogramowanie pośredniczące filtrowania hostów mają podobną funkcjonalność w różnych scenariuszach. Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego `Host` Nagłówków przesyłanych dalej jest właściwe, gdy nagłówek nie jest zachowywany podczas przekazywania żądań za pomocą serwera odwrotnego serwera proxy lub modułu równoważenia obciążenia. Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego filtrowania hostów jest właściwe, gdy `Host` Kestrel jest używany jako serwer brzegowy skierowany do publicznej wiadomości lub gdy nagłówek jest bezpośrednio przekazywaniem dalej.
>
> Aby uzyskać więcej informacji na temat <xref:host-and-deploy/proxy-load-balancer>oprogramowania pośredniczącego nagłówków przesyłanych dalej, zobacz .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Pustułka to wieloplatformowy [serwer www dla ASP.NET Core.](xref:fundamentals/servers/index) Pustułka to serwer www, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.

Pustułka obsługuje następujące scenariusze:

* HTTPS
* Nieprzezroczyste uaktualnienie używane do włączania [websockets](https://github.com/aspnet/websockets)
* Gniazda Unix o wysokiej wydajności za Nginx

Pustułka jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez program .NET Core.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kiedy używać Kestrel z odwrotnym serwerem proxy

Pustułka może być używana samodzielnie lub z *odwrotnym serwerem proxy,* takim jak [Internetowe Usługi Informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/). Serwer odwrotnego serwera proxy odbiera żądania HTTP z sieci i przekazuje je do Kestrel.

Pustułka używana jako serwer internetowy edge (web-facing):

![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

Pustułka używana w konfiguracji odwrotnego serwera proxy:

![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

Konfiguracja z odwrotnym serwerem proxy lub bez niego jest obsługiwaną konfiguracją hostingu.

Pustułka używana jako serwer brzegowy bez odwrotnego serwera proxy nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami. Gdy Kestrel jest skonfigurowany do nasłuchiwać na porcie, Kestrel obsługuje `Host` cały ruch dla tego portu, niezależnie od nagłówków żądań. Odwrotny serwer proxy, który może udostępniać porty, ma możliwość przesyłania dalej żądań do Kestrel na unikatowym adresie IP i porcie.

Nawet jeśli serwer odwrotnego serwera proxy nie jest wymagany, dobrym wyborem może być użycie odwrotnego serwera proxy.

Odwrotny serwer proxy:

* Można ograniczyć narażony obszar powierzchni publicznej aplikacji, które obsługuje.
* Zapewnij dodatkową warstwę konfiguracji i obrony.
* Może lepiej zintegrować się z istniejącą infrastrukturą.
* Uprość równoważenie obciążenia i bezpieczną komunikację (HTTPS). Tylko serwer odwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.

> [!WARNING]
> Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Jak używać Kestrel w aplikacjach ASP.NET Core

Pakiet [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

ASP.NET szablony projektów Core domyślnie używają Pustułki. W *Program.cs*, wywoła kod <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>szablonu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , który wywołuje za kulisami.

Aby zapewnić dodatkową `CreateDefaultBuilder`konfigurację <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>po wywołaniu , zadzwoń:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Aby uzyskać `CreateDefaultBuilder` więcej informacji na temat i tworzenia hosta, zobacz *sekcję Konfigurowanie hosta* w programie <xref:fundamentals/host/web-host#set-up-a-host>.

## <a name="kestrel-options"></a>Opcje pustułki

Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w wdrożeniach internetowych.

Ustaw ograniczenia właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy. Właściwość `Limits` zawiera wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.

W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obszaru nazw:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Opcje pustułki, które są skonfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index). Na przykład dostawca konfiguracji plików może załadować konfigurację kestrelu z *pliku appsettings.json* lub *appsettings.{ Środowisko}.json* plik:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

Użyj **jednego** z następujących podejść:

* Skonfiguruj `Startup.ConfigureServices`Pustułkę w :

  1. Wstrzyknąć `IConfiguration` `Startup` wystąpienie do klasy. W poniższym przykładzie przyjęto założenie, że `Configuration` wstrzyknięta konfiguracja jest przypisana do właściwości.
  2. W `Startup.ConfigureServices`programie `Kestrel` załaduj sekcję konfiguracji do konfiguracji Kestrela:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Konfigurowanie Pustułki podczas tworzenia hosta:

  W *Program.cs*załaduj `Kestrel` sekcję konfiguracji do konfiguracji Kestrela:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

Oba powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji.](xref:fundamentals/configuration/index)

### <a name="keep-alive-timeout"></a>Limit czasu utrzymywanie aktywności przy życiu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Pobiera lub ustawia [limit czasu keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). Wartość domyślna to 2 minuty.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a>Maksymalna liczba połączeń klientów

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maksymalną liczbę równoczesnych otwartych połączeń TCP można ustawić dla całej aplikacji za pomocą następującego kodu:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

Istnieje oddzielny limit dla połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu WebSockets). Po uaktualnieniu połączenia nie jest wliczane `MaxConcurrentConnections` do limitu.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

Maksymalna liczba połączeń jest domyślnie nieograniczona (zerowa).

### <a name="maximum-request-body-size"></a>Maksymalny rozmiar treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.

Zalecane podejście do zastąpienia limitu w ASP.NET Core MVC aplikacji jest <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji na każde żądanie:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Zastąp ustawienie dla określonego żądania w oprogramowaniu pośredniczącym:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Wyjątek jest zgłaszany, jeśli aplikacja konfiguruje limit żądania po aplikacji zaczął odczytywać żądanie. Istnieje `IsReadOnly` właściwość, która wskazuje, `MaxRequestBodySize` czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Gdy aplikacja jest prowadzona [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [Core Module](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączona, ponieważ iIS już ustawia limit.

### <a name="minimum-request-body-data-rate"></a>Minimalna szybkość danych treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Pustułka sprawdza co sekundę, czy dane są przychodzące z określoną szybkością w bajtach/sekundę. Jeśli szybkość spadnie poniżej minimum, upłynie limit czasu połączenia. Okres prolongaty to czas, który Kestrel daje klientowi na zwiększenie szybkości wysyłania do minimum; stawka nie jest sprawdzana w tym czasie. Okres prolongaty pomaga uniknąć porzucania połączeń, które początkowo wysyłają dane z powolnym tempem ze względu na spowolnienie tcp.

Domyślna minimalna stawka wynosi 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.

Minimalna stawka ma również zastosowanie do odpowiedzi. Kod, aby ustawić limit żądania i limit odpowiedzi `RequestBody` jest `Response` taka sama, z wyjątkiem posiadania lub w nazwy właściwości i interfejsu.

Oto przykład, który pokazuje, jak skonfigurować minimalne szybkości transmisji danych w *Program.cs:*

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a>Limit czasu nagłówków żądań

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Pobiera lub ustawia maksymalną ilość czasu serwer spędza odbieranie nagłówków żądań. Wartość domyślna to 30 sekund.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a>Synchroniczne operacje we/wy

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>określa, czy synchroniczne we/wy są dozwolone dla żądania i odpowiedzi. Wartością domyślną jest `true`.

> [!WARNING]
> Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do głodu puli wątków, co sprawia, że aplikacja nie odpowiada. Włącz `AllowSynchronousIO` tylko podczas korzystania z biblioteki, która nie obsługuje asynchronicznych we/wy.

Poniższy przykład wyłącza synchroniczne we/wy:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

Aby uzyskać informacje o innych opcjach i ograniczeniach kestrel, zobacz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

Domyślnie ASP.NET Core wiąże się z:

* `http://localhost:5000`
* `https://localhost:5001`(gdy certyfikat rozwoju lokalnego jest obecny)

Określ adresy URL za pomocą:

* `ASPNETCORE_URLS`zmienną środowiskową.
* `--urls`argument wiersza polecenia.
* `urls`klucz konfiguracji hosta.
* `UseUrls`metody rozszerzenia.

Wartość podana przy użyciu tych metod może być co najmniej jeden punkt końcowy HTTP i HTTPS (HTTPS, jeśli dostępny jest domyślny certyfikat). Skonfiguruj wartość jako listę oddzieloną średnikami `"Urls": "http://localhost:8000;http://localhost:8001"`(na przykład ).

Aby uzyskać więcej informacji na temat tych metod, zobacz [Adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastępowanie konfiguracji](xref:fundamentals/host/web-host#override-configuration).

Tworzony jest certyfikat dewelopera:

* Po zainstalowaniu [pliku .NET Core SDK.](/dotnet/core/sdk)
* [Narzędzie dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.

Niektóre przeglądarki wymagają udzielenia jawnego uprawnienia do ufania certyfikatowi lokalnego rozwoju.

Szablony projektów domyślnie konfigurują aplikacje do uruchamiania na https i obejmują [przekierowanie HTTPS i obsługę HSTS](xref:security/enforcing-ssl).

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody, aby skonfigurować prefiksy adresów URL i porty dla Kestrel.

`UseUrls`argument `--urls` wiersza polecenia, `urls` klucz konfiguracji hosta i zmienna `ASPNETCORE_URLS` środowiskowa również działają, ale mają ograniczenia odnotowane w dalszej części tej sekcji (domyślny certyfikat musi być dostępny dla konfiguracji punktu końcowego HTTPS).

`KestrelServerOptions`Konfiguracji:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(>)\<

Określa konfigurację `Action` uruchamianą dla każdego określonego punktu końcowego. Wywołanie `ConfigureEndpointDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>Konfigurowanie>httpsdefaults(akcja\<httpsconnectionadapteroptions)

Określa konfigurację `Action` uruchamianą dla każdego punktu końcowego HTTPS. Wywołanie `ConfigureHttpsDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.

### <a name="configureiconfiguration"></a>Konfigurowanie(Konfiguracja IConfiguration)

Tworzy moduł ładujący konfiguracji do konfigurowania <xref:Microsoft.Extensions.Configuration.IConfiguration> Kestrel, który przyjmuje jako dane wejściowe. Konfiguracja musi być w zakresie do sekcji konfiguracji dla Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Skonfiguruj Kestrel do używania protokołu HTTPS.

`ListenOptions.UseHttps`Rozszerzenia:

* `UseHttps`&ndash; Skonfiguruj Kestrel do używania protokołu HTTPS z certyfikatem domyślnym. Zgłasza wyjątek, jeśli nie skonfigurowano certyfikatu domyślnego.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`Parametry:

* `filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.
* `password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.
* `configureOptions`jest `Action` skonfigurowaniem `HttpsConnectionAdapterOptions`pliku . Zwraca `ListenOptions`wartość .
* `storeName`to magazyn certyfikatów, z którego ma być załadowany certyfikat.
* `subject`jest nazwą podmiotu certyfikatu.
* `allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.
* `location`to lokalizacja magazynu, z wyładowania certyfikatu.
* `serverCertificate`to certyfikat X.509.

W produkcji protokół HTTPS musi być jawnie skonfigurowany. Należy co najmniej dostarczyć certyfikat domyślny.

Obsługiwane konfiguracje opisane dalej:

* Brak konfiguracji
* Zastępowanie certyfikatu domyślnego z konfiguracji
* Zmienianie wartości domyślnych w kodzie

*Brak konfiguracji*

Kestrel `http://localhost:5000` nasłuchuje `https://localhost:5001` i (jeśli dostępny jest domyślny certyfikat).

<a name="configuration"></a>

*Zastępowanie certyfikatu domyślnego z konfiguracji*

`CreateDefaultBuilder`domyślnie `Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel. Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel. Skonfiguruj wiele punktów końcowych, w tym adresy URL i certyfikaty do użycia, z pliku na dysku lub z magazynu certyfikatów.

W poniższym *przykładzie appsettings.json:*

* Ustaw **AllowInvalid,** aby `true` zezwolić na używanie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).
* Każdy punkt końcowy HTTPS, który nie określa certyfikatu **(HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowanego w obszarze **Domyślne** **certyfikaty** > lub certyfikat dewelopera.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternatywą dla **używania ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów. Na przykład certyfikat**domyślny** **certyfikat certyfikatów** > można określić jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Uwagi dotyczące schematu:

* Nazwy punktów końcowych są bez uwzględniania wielkości liter. Na przykład `HTTPS` `Https` i są prawidłowe.
* Parametr `Url` jest wymagany dla każdego punktu końcowego. Format dla tego parametru jest taki `Urls` sam jak parametr konfiguracji najwyższego poziomu, z tą różnicą, że jest on ograniczony do pojedynczej wartości.
* Te punkty końcowe zastępują te `Urls` zdefiniowane w konfiguracji najwyższego poziomu, a nie do ich dodawania. Punkty końcowe zdefiniowane `Listen` w kodzie za pośrednictwem kumulują się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.
* Sekcja `Certificate` jest opcjonalna. Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach. Jeśli nie są dostępne żadne ustawienia domyślne, serwer zgłasza wyjątek i nie można go uruchomić.
* Sekcja `Certificate` obsługuje **Path**&ndash;zarówno**certyfikaty Path Password, jak** i **Subject**&ndash;**Store.**
* Dowolna liczba punktów końcowych może być zdefiniowana w ten sposób, o ile nie powodują konfliktów portów.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` metodę, `.Endpoint(string name, listenOptions => { })` która może służyć do uzupełniania ustawień skonfigurowanego punktu końcowego:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do dalszej iteracji na istniejącym ładowarce, takim jak ten dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>program .

* Sekcja konfiguracji dla każdego punktu końcowego jest `Endpoint` dostępna na opcje w metodzie, dzięki czemu ustawienia niestandardowe mogą być odczytywane.
* Wiele konfiguracji może być `options.Configure(context.Configuration.GetSection("{SECTION}"))` ładowanych przez ponowne wywołanie z inną sekcją. Używana jest tylko ostatnia `Load` konfiguracja, chyba że jest jawnie wywoływana w poprzednich wystąpieniach. Metapakiet nie jest `Load` wywoływany, więc jego domyślna sekcja konfiguracji może zostać zastąpiona.
* `KestrelConfigurationLoader`dubluje `Listen` rodzinę interfejsów `KestrelServerOptions` `Endpoint` API z jako przeciążenia, więc kod i config punktów końcowych mogą być skonfigurowane w tym samym miejscu. Te przeciążenia nie używają nazw i zużywają tylko ustawienia domyślne z konfiguracji.

*Zmienianie wartości domyślnych w kodzie*

`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany `ListenOptions` `HttpsConnectionAdapterOptions`ustawień domyślnych dla i , w tym zastąpienie domyślnego certyfikatu określonego w poprzednim scenariuszu. `ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

*Obsługa pustułki dla SNI*

[Wskazanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do obsługi wielu domen na tym samym adresie IP i porcie. Aby usługa SNI działała, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania protokołu TLS, aby serwer mógł dostarczyć poprawny certyfikat. Klient używa dostarczonego certyfikatu do szyfrowanej komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu TLS.

Kestrel obsługuje SNI `ServerCertificateSelector` za pomocą wywołania zwrotnego. Wywołanie zwrotne jest wywoływane raz na połączenie, aby umożliwić aplikacji sprawdzenie nazwy hosta i wybranie odpowiedniego certyfikatu.

Obsługa SNI wymaga:

* Uruchamianie w `netcoreapp2.1` ramach docelowej lub nowszej. Włączone `net461` lub późniejsze wywołanie zwrotne `name` jest `null`wywoływane, ale jest zawsze . Jest `name` również, `null` jeśli klient nie udostępnia parametru nazwy hosta w uzgadnianiu TLS.
* Wszystkie strony internetowe działają w tym samym wystąpieniu Kestrel. Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez odwrotnego serwera proxy.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a>Rejestrowanie połączeń

Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> emitowania dzienników poziomu debugowania dla komunikacji na poziomie bajtów w połączeniu. Rejestrowanie połączeń jest przydatne do rozwiązywania problemów w komunikacji niskiego poziomu, takich jak podczas szyfrowania TLS i za serwerami proxy. Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony przed , zaszyfrowany ruch jest rejestrowany. Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony po , odszyfrowany ruch jest rejestrowany.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Powiązanie z gniazdem TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X.509:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

W przykładzie konfiguruje protokół <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS dla punktu końcowego za pomocą pliku . Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Powiązanie z gniazdem Unixa

Słuchaj na gnieździe Unix z <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lepszej wydajności z Nginx, jak pokazano w tym przykładzie:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* W pliku nginx confiuguration `server`  >  `location`  >  `proxy_pass` ustaw `http://unix:/tmp/{KESTREL SOCKET}:/;`wpis na . `{KESTREL SOCKET}`jest nazwą gniazda dostarczonego <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (na `kestrel-test.sock` przykład w poprzednim przykładzie).
* Upewnij się, że gniazdo jest zapisywalne przez `chmod go+w /tmp/kestrel-test.sock`Nginx (na przykład). 

### <a name="port-0"></a>Port 0

Po określeniu numeru `0` portu Kestrel dynamicznie wiąże się z dostępnym portem. W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie związany w czasie wykonywania:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, do którego można uzyskać kontakt z aplikacją:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Ograniczenia

Konfigurowanie punktów końcowych przy następujących podejściach:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument wiersza polecenia
* `urls`klucz konfiguracji hosta
* `ASPNETCORE_URLS`zmienna środowiskowa

Te metody są przydatne do tworzenia kodu pracy z serwerami innymi niż Kestrel. Należy jednak pamiętać o następujących ograniczeniach:

* Protokołu HTTPS nie można używać z tymi podejściami, chyba że domyślny certyfikat jest `KestrelServerOptions` podany w konfiguracji punktu końcowego HTTPS (na przykład przy użyciu konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).
* Gdy zarówno `Listen` `UseUrls` i podejścia są używane `Listen` jednocześnie, punkty końcowe zastępują punkty `UseUrls` końcowe.

### <a name="iis-endpoint-configuration"></a>Konfiguracja punktu końcowego usług IIS

Podczas korzystania z usługi IIS powiązania adresów URL dla powiązań `Listen` zastępowania usługi IIS są ustawiane przez jedną lub `UseUrls`. Aby uzyskać więcej informacji, zobacz [ASP.NET temat modułu podstawowego.](xref:host-and-deploy/aspnet-core-module)

## <a name="transport-configuration"></a>Konfiguracja transportu

Wraz z wydaniem ASP.NET Core 2.1 domyślny transport Kestrel nie jest już oparty na Libuv, ale zamiast tego opiera się na zarządzanych gniazdach. Jest to przełomowa zmiana dla ASP.NET aplikacje Core 2.0 uaktualniania <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> do 2.1, które wywołują i zależą od jednego z następujących pakietów:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)
* [Aplikacja Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

W przypadku projektów, które wymagają użycia libuv:

* Dodaj zależność pakietu [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Zadzwoń <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a>Prefiksy adresów URL

W `UseUrls`przypadku `--urls` korzystania z `urls` argumentu wiersza `ASPNETCORE_URLS` polecenia, klucza konfiguracji hosta lub zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w dowolnym z następujących formatów.

Tylko prefiksy adresów URL HTTP są prawidłowe. Pustułka nie obsługuje protokołu HTTPS podczas `UseUrls`konfigurowania powiązań adresów URL przy użyciu pliku .

* Adres IPv4 z numerem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`jest specjalnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.

* Adres IPv6 z numerem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`jest odpowiednikiem IPv6 IPv4 `0.0.0.0`.

* Nazwa hosta z numerem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Nazwy hostów `*` `+`, i , nie są specjalne. Wszystko, co nie jest `localhost` rozpoznawane jako prawidłowy adres IP lub wiąże się ze wszystkimi adresami IPv4 i IPv6. Aby powiązać różne nazwy hostów z różnymi aplikacjami ASP.NET Core na tym samym porcie, użyj [protokołu HTTP.sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.

  > [!WARNING]
  > Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).

* Nazwa `localhost` hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Po `localhost` określeniu Kestrel próbuje powiązać interfejsy sprzężenia zwrotnego IPv4 i IPv6. Jeśli żądany port jest używany przez inną usługę w interfejsie sprzężenia zwrotnego, kestrel nie może się uruchomić. Jeśli którykolwiek z interfejsów sprzężenia zwrotnego jest niedostępny z jakiegokolwiek innego powodu (najczęściej dlatego, że IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.

## <a name="host-filtering"></a>Filtrowanie hostów

Gdy Kestrel obsługuje konfigurację opartą `http://example.com:5000`na prefiksach, takich jak Kestrel, w dużej mierze ignoruje nazwę hosta. Host `localhost` jest specjalnym przypadkiem używanym do wiązania adresów sprzężenia zwrotnego. Każdy host inny niż jawny adres IP wiąże się ze wszystkimi publicznymi adresami IP. `Host`nagłówki nie są sprawdzane.

Aby obejść ten problem, użyj oprogramowania pośredniczącego filtrowania hostów. Oprogramowanie pośredniczące filtrowania hostów jest dostarczane przez pakiet [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub 2.2). Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>jest dodawane przez , który wywołuje: <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Oprogramowanie pośredniczące filtrowania hosta jest domyślnie wyłączone. Aby włączyć oprogramowanie pośredniczące, `AllowedHosts` zdefiniuj klucz w *appsettings.json*/*appsettings.\< EnvironmentName>.json*. Wartość jest listą nazw hostów rozdzielanych średnikami bez numerów portów:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Oprogramowanie pośredniczące nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) również ma <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję. Oprogramowanie pośredniczące nagłówków przesyłanych dalej i oprogramowanie pośredniczące filtrowania hostów mają podobną funkcjonalność w różnych scenariuszach. Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego `Host` Nagłówków przesyłanych dalej jest właściwe, gdy nagłówek nie jest zachowywany podczas przekazywania żądań za pomocą serwera odwrotnego serwera proxy lub modułu równoważenia obciążenia. Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego filtrowania hostów jest właściwe, gdy `Host` Kestrel jest używany jako serwer brzegowy skierowany do publicznej wiadomości lub gdy nagłówek jest bezpośrednio przekazywaniem dalej.
>
> Aby uzyskać więcej informacji na temat <xref:host-and-deploy/proxy-load-balancer>oprogramowania pośredniczącego nagłówków przesyłanych dalej, zobacz .

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* W przypadku korzystania z gniazd UNIX w systemie Linux gniazdo nie jest automatycznie usuwane po zamknięciu aplikacji. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/aspnetcore/issues/14134).
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: Składnia i routing komunikatów (sekcja 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)
