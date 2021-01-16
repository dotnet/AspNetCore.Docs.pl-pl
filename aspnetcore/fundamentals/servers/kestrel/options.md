---
title: Skonfiguruj opcje ASP.NET Core serwera sieci Web Kestrel
author: rick-anderson
description: Dowiedz się więcej na temat konfigurowania opcji Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253982"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>Skonfiguruj opcje ASP.NET Core serwera sieci Web Kestrel

Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.

Aby zapewnić dodatkową konfigurację po wywołaniu `ConfigureWebHostDefaults` , użyj `ConfigureKestrel` :

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

Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy. `Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.

W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

W przykładach przedstawionych w dalszej części tego artykułu opcje Kestrel są konfigurowane w kodzie C#. Opcje Kestrel można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index). Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację Kestrel z *appsettings.json* lub *appSettings. { Environment} plik JSON* :

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfigurację punktu końcowego](xref:fundamentals/servers/kestrel/endpoints) można konfigurować z poziomu dostawców konfiguracji. Pozostała konfiguracja Kestrel musi być skonfigurowana w kodzie C#.

Skorzystaj z **jednej** z następujących metod:

* Skonfiguruj Kestrel w `Startup.ConfigureServices` :

  1. Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy. W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.
  2. W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:

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

* Skonfiguruj Kestrel podczas kompilowania hosta:

  W programie *program.cs* Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:

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

Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).

## <a name="general-limits"></a>Ogólne limity

### <a name="keep-alive-timeout"></a>Limit czasu utrzymywania aktywności

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5). Wartość domyślna to 2 minuty.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Maksymalna liczba połączeń klienta

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets). Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maksymalna liczba połączeń jest domyślnie nieograniczona (null).

### <a name="maximum-request-body-size"></a>Maksymalny rozmiar treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.

Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania. Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.

Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony. Program IIS już ustawia limit.

### <a name="minimum-request-body-data-rate"></a>Minimalna stawka danych treści żądania

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę. Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, jaki Kestrel pozwala klientowi zwiększyć jego częstotliwość wysyłania do minimum. Ta częstotliwość nie jest sprawdzana w tym czasie. Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością z powodu spowolnienia TCP.

Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.

Minimalna stawka dotyczy także odpowiedzi. Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.

Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odwołania w poprzednim przykładzie nie są obecne w `HttpContext.Features` przypadku żądań HTTP/2. Modyfikowanie limitów szybkości dla poszczególnych żądań jest zwykle nieobsługiwane w przypadku protokołu HTTP/2 z powodu obsługi protokołu żądania multipleksowania. Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> nadal występuje `HttpContext.Features` dla żądań HTTP/2, ponieważ limit liczby odczytów nadal można *wyłączyć całkowicie* dla każdego żądania przez ustawienie `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` nawet dla żądania HTTP/2. Próba odczytania `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby ustawienia jej na wartość inną niż `null` spowoduje `NotSupportedException` zgłoszenie żądania HTTP/2.

Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.

### <a name="request-headers-timeout"></a>Limit czasu nagłówków żądań

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania. Wartość domyślna to 30 sekund.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>Limity protokołu HTTP/2

### <a name="maximum-streams-per-connection"></a>Maksymalna liczba strumieni na połączenie

`Http2.MaxStreamsPerConnection` ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2. Odrzucone strumienie są nadmiarowe.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Wartość domyślna to 100.

### <a name="header-table-size"></a>Rozmiar tabeli nagłówka

Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2. `Http2.HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK. Wartość jest podawana w oktetach i musi być większa od zera (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Wartość domyślna to 4096.

### <a name="maximum-frame-size"></a>Maksymalny rozmiar ramki

`Http2.MaxFrameSize` wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer. Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Wartość domyślna to 2 ^ 14 (16 384).

### <a name="maximum-request-header-size"></a>Maksymalny rozmiar nagłówka żądania

`Http2.MaxRequestHeaderFieldSize` wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania. Ten limit dotyczy zarówno nazwy, jak i wartości w skompresowanych i nieskompresowanych reprezentacji. Wartość musi być większa od zera (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Wartość domyślna to 8 192.

### <a name="initial-connection-window-size"></a>Początkowy rozmiar okna połączenia

`Http2.InitialConnectionWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie. Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` . Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Wartość domyślna to 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Rozmiar początkowego okna strumienia

`Http2.InitialStreamWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream). Żądania są również ograniczone przez `Http2.InitialConnectionWindowSize` . Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Wartość domyślna to 96 KB (98 304).

### <a name="http2-keep-alive-ping-configuration"></a>Konfiguracja polecenia ping Keep Alive protokołu HTTP/2

Kestrel można skonfigurować do wysyłania poleceń ping protokołu HTTP/2 do podłączonych klientów. Polecenia ping protokołu HTTP/2 współpracują z wieloma celami:

* Utrzymywanie bezczynnych połączeń. Niektórzy klienci i serwery proxy zamykają połączenia, które są w stanie bezczynności. Polecenia ping protokołu HTTP/2 są uznawane za działania w ramach połączenia i uniemożliwiają zamknięcie połączenia w trybie bezczynności.
* Zamknij połączenia w złej kondycji. Połączenia, w których klient nie odpowiada na polecenie "Keep Alive" w skonfigurowanym czasie, są zamykane przez serwer.

Dostępne są dwie opcje konfiguracji związane z poleceniami "Keep Alive" protokołu HTTP/2:

* `Http2.KeepAlivePingInterval``TimeSpan`umożliwia skonfigurowanie interwału ping. Serwer wysyła polecenie ping do klienta, jeśli nie otrzyma żadnej ramki przez ten okres. Polecenia ping utrzymywania aktywności są wyłączone, gdy ta opcja jest ustawiona na `TimeSpan.MaxValue` . Wartość domyślna to `TimeSpan.MaxValue`.
* `Http2.KeepAlivePingTimeout``TimeSpan`umożliwia skonfigurowanie limitu czasu polecenia ping. Jeśli serwer nie odbiera żadnych ramek, takich jak odpowiedzi ping, podczas tego limitu czasu połączenie jest zamknięte. Limit czasu utrzymywania aktywności jest wyłączony, gdy ta opcja jest ustawiona na `TimeSpan.MaxValue` . Wartość domyślna to 20 sekund.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>Inne opcje

### <a name="synchronous-io"></a>Synchroniczne operacje we/wy

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi. Wartość domyślna to `false`.

> [!WARNING]
> Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada. Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.

Poniższy przykład umożliwia synchroniczne we/wy:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
