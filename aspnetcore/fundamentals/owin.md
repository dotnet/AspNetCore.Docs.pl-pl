---
title: Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core
author: ardalis
description: Dowiedz się, jak ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN), który umożliwia oddzielenie aplikacji sieci Web od serwerów sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666685"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core

Przez [Steve Smith](https://ardalis.com/) i Rick [Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN). OWIN umożliwia oddzielenie aplikacji internetowych od serwerów sieci Web. Definiuje standardowy sposób oprogramowania pośredniczącego, który ma być używany w potoku do obsługi żądań i skojarzonych odpowiedzi. ASP.NET Aplikacje Core i oprogramowanie pośredniczące mogą współpracować z aplikacjami, serwerami i oprogramowaniem pośredniczącym opartym na OWIN.

OWIN zapewnia warstwę oddzielenia, która umożliwia dwie struktury z różnych modeli obiektów, które mają być używane razem. Pakiet `Microsoft.AspNetCore.Owin` zawiera dwie implementacje kart:

* ASP.NET Core do OWIN 
* OWIN do ASP.NET Core

Dzięki temu ASP.NET Core mogą być hostowane na serwerze/hoście zgodnym z OWIN lub na innych komponentach zgodnych z OWIN, które mają być uruchamiane na ASP.NET Core.

> [!NOTE]
> Korzystanie z tych kart wiąże się z kosztami wydajności. Aplikacje używające tylko składników ASP.NET Core `Microsoft.AspNetCore.Owin` nie powinny używać pakietu ani kart.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Uruchamianie oprogramowania pośredniczącego OWIN w potoku ASP.NET Core

ASP.NET Core OWIN wsparcie jest wdrażany jako część `Microsoft.AspNetCore.Owin` pakietu. Można zaimportować obsługę OWIN do projektu, instalując ten pakiet.

Oprogramowanie pośredniczące OWIN jest zgodne ze `Func<IDictionary<string, object>, Task>` [specyfikacją OWIN,](https://owin.org/spec/spec/owin-1.0.0.html)która wymaga `owin.ResponseBody`interfejsu i ustawionymi określonymi klawiszami (takimi jak ). Następujące proste oprogramowanie pośredniczące OWIN wyświetla "Hello World":

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

Przykładowy podpis `Task` zwraca i `IDictionary<string, object>` akceptuje zgodnie z wymaganiami OWIN.

Poniższy kod pokazuje, `OwinHello` jak dodać oprogramowanie pośredniczące (pokazane powyżej) `UseOwin` do potoku ASP.NET Core z metodą rozszerzenia.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Można skonfigurować inne akcje, które mają być podejmowane w potoku OWIN.

> [!NOTE]
> Nagłówki odpowiedzi powinny być modyfikowane tylko przed pierwszym zapisem w strumieniu odpowiedzi.

> [!NOTE]
> Wiele wywołań `UseOwin` jest odradzane ze względu na wydajność. Składniki OWIN będą działać najlepiej, jeśli są zgrupowane razem.

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>Korzystanie z ASP.NET Core Hosting na serwerze opartym na OWIN

Serwery oparte na OWIN mogą obsługiwać aplikacje ASP.NET Core. Jednym z takich serwerów jest [Nowin](https://github.com/Bobris/Nowin), serwer www .NET OWIN. W przykładzie do tego artykułu, mam włączone projektu, który odwołuje się nowin i używa go do tworzenia `IServer` zdolnego do samodzielnego hostingu ASP.NET Core.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`jest interfejsem, `Features` który wymaga `Start` właściwości i metody.

`Start`jest odpowiedzialny za konfigurowanie i uruchamianie serwera, co w tym przypadku odbywa się za pośrednictwem serii płynnych wywołań interfejsu API, które ustawiają adresy analizowane z iServerAddressesFeature. Należy zauważyć, że płynna konfiguracja zmiennej `_builder` określa, `appFunc` że żądania będą obsługiwane przez zdefiniowane wcześniej w metodzie. Jest `Func` to wywoływane w każdym żądaniu do przetwarzania żądań przychodzących.

Dodamy również `IWebHostBuilder` rozszerzenie, aby ułatwić dodawanie i konfigurowanie serwera Nowin.

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

Mając to na miejscu, wywołaj rozszerzenie w *Program.cs,* aby uruchomić aplikację ASP.NET Core przy użyciu tego serwera niestandardowego:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

Dowiedz się więcej o [ASP.NET Serwery podstawowe](xref:fundamentals/servers/index).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Uruchom ASP.NET Core na serwerze opartym na OWIN i korzystaj z obsługi websockets

Innym przykładem tego, jak funkcje serwerów opartych na OWIN mogą być wykorzystywane przez ASP.NET Core jest dostęp do funkcji, takich jak WebSockets. Serwer sieci web owin .NET używany w poprzednim przykładzie ma obsługę wbudowanych gniazd sieci Web, które mogą być wykorzystywane przez aplikację ASP.NET Core. Poniższy przykład przedstawia prostą aplikację internetową, która obsługuje gniazda sieci Web i echa z powrotem wszystko wysyłane do serwera za pośrednictwem WebSockets.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

Ten [przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) jest skonfigurowany `NowinServer` przy użyciu tego samego, co poprzedni — jedyną `Configure` różnicą jest sposób konfigurowania aplikacji w swojej metodzie. Test przy użyciu [prostego klienta websocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) pokazuje aplikacji:

![Klient testowy gniazda sieci Web](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>Środowisko OWIN

Środowisko OWIN można skonstruować `HttpContext`za pomocą pliku .

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>Klucze OWIN

OWIN zależy `IDictionary<string,object>` od obiektu do przekazywania informacji w całej wymiany żądania/odpowiedzi HTTP. ASP.NET Core implementuje klucze wymienione poniżej. Zobacz [podstawową specyfikację, rozszerzenia](https://owin.org/#spec)i [wytyczne dotyczące kluczy OWIN i klucze wspólne](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Żądania danych (OWIN v1.0.0)

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| owin. ProśbaScheme | `String` |  |
| owin. RequestMetoda  | `String` | |    
| owin. Baza żądań  | `String` | |    
| owin. Ścieżka żądań | `String` | |     
| owin. RequestQueryString  | `String` | |    
| owin. WniosekProtocol  | `String` | |    
| owin. RequestHeaders (Żądaj y) | `IDictionary<string,string[]>`  | |
| owin. RequestBody (Ciało żądania) | `Stream`  | |

### <a name="request-data-owin-v110"></a>Żądania danych (OWIN v1.1.0)

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| owin. Requestid | `String` | Optional (Opcjonalność) |

### <a name="response-data-owin-v100"></a>Dane odpowiedzi (OWIN v1.0.0)

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| owin. Kod statususdus odpowiedzi | `int` | Optional (Opcjonalność) |
| owin. OdpowiedźReasonPhrase | `String` | Optional (Opcjonalność) |
| owin. ResponseHeaders (Głowice odpowiedzi) | `IDictionary<string,string[]>`  | |
| owin. Ciało odpowiedzi | `Stream`  | |

### <a name="other-data-owin-v100"></a>Inne dane (OWIN v1.0.0)

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| owin. CallCancelled ( CallCancelled ) | `CancellationToken` |  |
| owin. Wersja  | `String` | |   

### <a name="common-keys"></a>Wspólne klawisze

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| Ssl. Clientcertificate | `X509Certificate` |  |
| Ssl. LoadClientCertAsync  | `Func<Task>` | |    
| Serwera. RemoteIpAddress (RemoteIpAddress)  | `String` | |    
| Serwera. Usługa RemotePort | `String` | |     
| Serwera. LocalIpAddress (LocalIpAddress)  | `String` | |    
| Serwera. Port_lokalny  | `String` | |    
| Serwera. IsLocal (Lokalizacja)  | `bool` | |    
| Serwera. OnSendingHeaders (Głowice onsendingheaders)  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| Sendfile. Sendasync | Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Na żądanie |

### <a name="opaque-v030"></a>Nieprzezroczyste v0.3.0

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| Nieprzezroczyste. Wersja | `String` |  |
| Nieprzezroczyste. Uaktualnienia | `OpaqueUpgrade` | Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| Nieprzezroczyste. Strumienia | `Stream` |  |
| Nieprzezroczyste. CallCancelled ( CallCancelled ) | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Klucz               | Wartość (typ) | Opis |
| ----------------- | ------------ | ----------- |
| Websocket. Wersja | `String` |  |
| Websocket. Zaakceptować | `WebSocketAccept` | Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| Websocket. AcceptAlt (AcceptAlt) |  | Non-spec |
| Websocket. SubProtocol | `String` | Patrz [RFC6455 Sekcja 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Krok 5.5 |
| Websocket. Sendasync | `WebSocketSendAsync` | Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| Websocket. OdbiórAsync | `WebSocketReceiveAsync` | Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| Websocket. Zamknijasync | `WebSocketCloseAsync` | Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| Websocket. CallCancelled ( CallCancelled ) | `CancellationToken` |  |
| Websocket. Stan klimowy | `int` | Optional (Opcjonalność) |
| Websocket. ClientCloseDescription (Opis klienta) | `String` | Optional (Opcjonalność) |

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Serwery](xref:fundamentals/servers/index)
