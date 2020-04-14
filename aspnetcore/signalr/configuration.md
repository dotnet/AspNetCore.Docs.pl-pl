---
title: konfiguracja ASP.NET SignalR Core
author: bradygaster
description: Dowiedz się, jak skonfigurować SignalR aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228143"
---
# <a name="aspnet-core-signalr-configuration"></a>Konfiguracja biblioteki SignalR platformy ASP.NET Core

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Opcje serializacji JSON/MessagePack

ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu [addjsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia. `AddJsonProtocol`można dodać po [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices`. Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt. [Właściwość PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) na tym `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiekcie jest obiektem, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [dokumentację System.Text.Json](/dotnet/api/system.text.json).

Na przykład, aby skonfigurować serializator, aby nie zmieniać wielkości liter nazw właściwości, zamiast domyślnych nazw "camelCase", użyj następującego kodu w: `Startup.ConfigureServices`

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Przełącz się na Newtonsoft.Json

Jeśli potrzebujesz `Newtonsoft.Json` funkcji, które nie są `System.Text.Json`obsługiwane w , Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opcje serializacji pakietu MessagePack

Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.

> [!NOTE]
> Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale. Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania. Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte. Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez ten koncentrator. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów. |
| `EnableDetailedErrors` | `false` | Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody. Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje. |
| `StreamBufferCapacity` | `10` | Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klienta. Jeśli ten limit zostanie osiągnięty, przetwarzanie wywołań jest blokowane, dopóki serwer przetwarza elementy strumienia.|
| `MaximumReceiveMessageSize` | 32 KB | Maksymalny rozmiar pojedynczej przychodzącej wiadomości koncentratora. |

Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer przed zastosowaniem backpressure. Zwiększenie tej wartości umożliwia serwerowi szybciej odbierać większe wiadomości bez stosowania kompresji wsteczjej, ale może zwiększyć zużycie pamięci. |
| `AuthorizationData` | Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, która buforuje serwer przed obserwowaniem wstecznego naciśnięcia. Zwiększenie tej wartości umożliwia serwerowi buforowanie większych wiadomości szybciej bez oczekiwania na ciśnienie wsteczne, ale może zwiększyć zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu długie sondowanie. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu WebSockets. |
| `MinimumProtocolVersion` | 0 | Określ minimalną wersję protokołu negocjacji. Służy do ograniczania klientów do nowszych wersji. |

Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej. |

Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone. |
| `SubProtocolSelector` | `null` | Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową. Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów). Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody. Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety. Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.

Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet. Wywołanie `AddConsole` metody rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Zamiast `LogLevel` wartości można również podać wartość `string` reprezentującą nazwę poziomu dziennika. Jest to przydatne podczas konfigurowania rejestrowania SignalR w środowiskach, `LogLevel` w których nie masz dostępu do stałych.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

W poniższej tabeli wymieniono dostępne poziomy dziennika. Wartość poda `configureLogging` się do ustawia **minimalny** poziom dziennika, który będzie rejestrowany. Wiadomości rejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.

| Ciąg                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**lub**`information` | `LogLevel.Information` |
| `warn`**lub**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).

Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania. Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można to bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Konfigurowanie dozwolonych transportów

Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript). Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji websockets klienta Java jest jedynym dostępnym transportem.

W kliencie Java transport jest `withTransport` wybierany `HttpHubConnectionBuilder`za pomocą metody na pliku . Domyślnie klient Java używa transportu WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Klient Java SignalR nie obsługuje jeszcze awaryjnego transportu.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania na okaziciela

Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ). W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets). W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .

W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymania przy życiu

Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript). Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `HandshakeTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript). Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysłania przy użyciu każdego żądania HTTP. |
| `CloseTimeout` | 5 sekund | WebSockets tylko. Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP. Nie jest używany dla połączeń WebSocket. Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie. **Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket. Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `skipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |

---

W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Opcje serializacji JSON/MessagePack

ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu [addjsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia. `AddJsonProtocol`można dodać po [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices`. Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt. [Właściwość PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) na tym `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiekcie jest obiektem, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [dokumentację System.Text.Json](/dotnet/api/system.text.json).

Na przykład, aby skonfigurować serializator, aby nie zmieniać wielkości liter nazw właściwości, zamiast domyślnych nazw "camelCase", użyj następującego kodu w: `Startup.ConfigureServices`

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Przełącz się na Newtonsoft.Json

Jeśli potrzebujesz `Newtonsoft.Json` funkcji, które nie są `System.Text.Json`obsługiwane w , Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opcje serializacji pakietu MessagePack

Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.

> [!NOTE]
> Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale. Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania. Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte. Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez ten koncentrator. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów. |
| `EnableDetailedErrors` | `false` | Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody. Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje. |
| `StreamBufferCapacity` | `10` | Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klienta. Jeśli ten limit zostanie osiągnięty, przetwarzanie wywołań jest blokowane, dopóki serwer przetwarza elementy strumienia.|
| `MaximumReceiveMessageSize` | 32 KB | Maksymalny rozmiar pojedynczej przychodzącej wiadomości koncentratora. |

Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer przed zastosowaniem backpressure. Zwiększenie tej wartości umożliwia serwerowi szybciej odbierać większe wiadomości bez stosowania kompresji wsteczjej, ale może zwiększyć zużycie pamięci. |
| `AuthorizationData` | Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, która buforuje serwer przed obserwowaniem wstecznego naciśnięcia. Zwiększenie tej wartości umożliwia serwerowi buforowanie większych wiadomości szybciej bez oczekiwania na ciśnienie wsteczne, ale może zwiększyć zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu długie sondowanie. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu WebSockets. |

Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej. |

Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone. |
| `SubProtocolSelector` | `null` | Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową. Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów). Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody. Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety. Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.

Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet. Wywołanie `AddConsole` metody rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Zamiast `LogLevel` wartości można również podać wartość `string` reprezentującą nazwę poziomu dziennika. Jest to przydatne podczas konfigurowania rejestrowania SignalR w środowiskach, `LogLevel` w których nie masz dostępu do stałych.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

W poniższej tabeli wymieniono dostępne poziomy dziennika. Wartość poda `configureLogging` się do ustawia **minimalny** poziom dziennika, który będzie rejestrowany. Wiadomości rejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.

| Ciąg                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**lub**`information` | `LogLevel.Information` |
| `warn`**lub**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).

Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania. Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można to bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Konfigurowanie dozwolonych transportów

Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript). Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji websockets klienta Java jest jedynym dostępnym transportem.

W kliencie Java transport jest `withTransport` wybierany `HttpHubConnectionBuilder`za pomocą metody na pliku . Domyślnie klient Java używa transportu WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Klient Java SignalR nie obsługuje jeszcze awaryjnego transportu.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania na okaziciela

Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ). W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets). W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .

W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymania przy życiu

Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript). Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `HandshakeTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript). Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysłania przy użyciu każdego żądania HTTP. |
| `CloseTimeout` | 5 sekund | WebSockets tylko. Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP. Nie jest używany dla połączeń WebSocket. Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie. **Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket. Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `skipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |

---

W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opcje serializacji JSON/MessagePack

ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia, które `Startup.ConfigureServices` mogą być dodawane po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w metodzie. Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt. [Właściwość PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) na tym obiekcie `JsonSerializerSettings` jest obiektem JSON.NET, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Na przykład, aby skonfigurować serializator do używania nazw właściwości "PascalCase", zamiast domyślnych nazw "camelCase", użyj następującego kodu w `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="messagepack-serialization-options"></a>Opcje serializacji pakietu MessagePack

Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.

> [!NOTE]
> Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer uzna klienta rozłączony, jeśli nie otrzymał komunikatu (w tym keep-alive) w tym przedziale. Może upłynąć dłużej niż ten przedział limitu czasu dla klienta, aby faktycznie być oznaczone rozłączony, ze względu na sposób zaimplementowania. Zalecana wartość jest `KeepAliveInterval` dwukrotnie wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte. Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez ten koncentrator. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów. |
| `EnableDetailedErrors` | `false` | Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody. Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje. |

Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta buforującego serwer. Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci. |
| `AuthorizationData` | Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysyłanych przez aplikację buforów serwera. Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych wiadomości, ale może negatywnie wpłynąć na zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu długie sondowanie. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu WebSockets. |

Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej. |

Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone. |
| `SubProtocolSelector` | `null` | Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową. Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów). Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody. Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety. Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.

Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet. Wywołanie `AddConsole` metody rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).

Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania. Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można to bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Konfigurowanie dozwolonych transportów

Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript). Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji websockets klienta Java jest jedynym dostępnym transportem.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania na okaziciela

Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ). W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets). W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .

W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymania przy życiu

Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript). Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `HandshakeTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript). Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Określa interwał, w którym klient wysyła komunikaty ping. Wysyłanie dowolnej wiadomości od klienta resetuje czasomierz do początku interwału. Jeśli klient nie wysłał wiadomości w `ClientTimeoutInterval` zestawie na serwerze, serwer uważa klienta za rozłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysłania przy użyciu każdego żądania HTTP. |
| `CloseTimeout` | 5 sekund | WebSockets tylko. Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP. Nie jest używany dla połączeń WebSocket. Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie. **Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket. Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `skipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |

---

W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opcje serializacji JSON/MessagePack

ASP.NET Core SignalR obsługuje dwa protokoły kodowania wiadomości: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozszerzenia, które `Startup.ConfigureServices` mogą być dodawane po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w metodzie. Metoda `AddJsonProtocol` przyjmuje delegata, `options` który odbiera obiekt. [Właściwość PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) na tym obiekcie `JsonSerializerSettings` jest obiektem JSON.NET, który może służyć do konfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Na przykład, aby skonfigurować serializator do używania nazw właściwości "PascalCase", zamiast domyślnych nazw "camelCase", użyj następującego kodu w `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

W kliencie platformy .NET ta sama `AddJsonProtocol` metoda rozszerzenia istnieje w programie [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obszar `Microsoft.Extensions.DependencyInjection` nazw musi zostać zaimportowany w celu rozwiązania metody rozszerzenia:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Obecnie nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="messagepack-serialization-options"></a>Opcje serializacji pakietu MessagePack

Serializacja MessagePack można skonfigurować, udostępniając pełnomocnika do [wywołania AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Zobacz [MessagePack w SignalR,](xref:signalr/messagepackhubprotocol) aby uzyskać więcej informacji.

> [!NOTE]
> Obecnie nie można skonfigurować serializacji messagepack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania koncentratorów SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, wiadomość ping jest wysyłana automatycznie, aby utrzymać połączenie otwarte. Podczas `KeepAliveInterval`zmiany zmień `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecana `ServerTimeout` / `serverTimeoutInMilliseconds` wartość jest `KeepAliveInterval` dwukrotnie wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez ten koncentrator. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych koncentratorów. |
| `EnableDetailedErrors` | `false` | Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w hub metody. Wartością `false`domyślną jest , ponieważ te komunikaty wyjątków mogą zawierać poufne informacje. |

Opcje można skonfigurować dla wszystkich koncentratorów, `AddSignalR` udostępniając `Startup.ConfigureServices`opcje delegowania do połączenia w .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Opcje dla pojedynczego koncentratora zastępują `AddSignalR` opcje globalne podane <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>w i można je skonfigurować za pomocą:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania zaawansowanych ustawień związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie pełnomocnika do `Startup.Configure` [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania zaawansowanych opcji HTTP ASP.NET Core SignalR:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta buforującego serwer. Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci. |
| `AuthorizationData` | Dane automatycznie zbierane z `Authorize` atrybutów stosowanych do klasy Hub. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) używanych do określenia, czy klient jest autoryzowany do łączenia się z koncentratorem. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysyłanych przez aplikację buforów serwera. Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych wiadomości, ale może negatywnie wpłynąć na zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Bit flagi wyliczenia `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może używać do łączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu długie sondowanie. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu WebSockets. |

Transport długiego sondowania ma dodatkowe opcje, `LongPolling` które można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas oczekiwania serwera na wysłanie wiadomości do klienta przed zakończeniem pojedynczego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient do wydawania nowych żądań ankiety częściej. |

Transport WebSocket ma dodatkowe opcje, które `WebSockets` można skonfigurować za pomocą właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po zamknięciu serwera, jeśli klient nie może zamknąć w tym przedziale czasu, połączenie zostanie zakończone. |
| `SubProtocolSelector` | `null` | Pełnomocnik, który może służyć `Sec-WebSocket-Protocol` do ustawiania nagłówka na wartość niestandardową. Pełnomocnik odbiera wartości wymagane przez klienta jako dane wejściowe i oczekuje się, że zwróci żądaną wartość. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować `HubConnectionBuilder` na typ (dostępne w .NET i JavaScript klientów). Jest również dostępny w kliencie Java, ale podklasa `HttpHubConnectionBuilder` jest tym, co `HubConnection` zawiera opcje konfiguracji konstruktora, a także na samym.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest skonfigurowane w kliencie `ConfigureLogging` platformy .NET przy użyciu metody. Dostawcy rejestrowania i filtry mogą być rejestrowane w taki sam sposób, jak są one na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację [logowania ASP.NET Core.](xref:fundamentals/logging/index)

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować niezbędne pakiety. Zobacz [wbudowane dostawców rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) sekcji dokumentów dla pełnej listy.

Na przykład, aby włączyć rejestrowanie `Microsoft.Extensions.Logging.Console` konsoli, zainstaluj pakiet NuGet. Wywołanie `AddConsole` metody rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wyprodukowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, należy określić `signalR.LogLevel.None` w metodzie. `configureLogging`

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [dokumentację diagnostyki signalr](xref:signalr/diagnostics).

Klient Java SignalR używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnej implementacji określonego rejestrowania przez wprowadzenie określonej zależności rejestrowania. Poniższy fragment kodu pokazuje, jak `java.util.logging` używać z klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny rejestrator bez operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można to bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Konfigurowanie dozwolonych transportów

Transporty używane przez SignalR można skonfigurować w `WithUrl` wywołaniu (w`withUrl` języku JavaScript). Bitowe-OR wartości `HttpTransportType` może służyć do ograniczenia klienta do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na websockets i długie połączenia sondowania:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` `withUrl`pola obiektu opcji pod warunkiem:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania na okaziciela

Aby zapewnić dane uwierzytelniania wraz z `AccessTokenProvider` żądaniami SignalR, użyj opcji (w`accessTokenFactory` języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET ten token dostępu jest przekazywany jako token `Authorization` HTTP "Uwierzytelnianie `Bearer`nadźwigowe" (przy użyciu nagłówka z typem ). W kliencie JavaScript token dostępu jest używany jako token na nośniku, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość stosowania nagłówków (w szczególności w zdarzeniach wysłanych przez serwer i żądaniach WebSockets). W takich przypadkach token dostępu jest dostarczany `access_token`jako wartość ciągu zapytania .

W kliencie .NET `AccessTokenProvider` można określić opcję za `WithUrl`pomocą opcji delegata w :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez `accessTokenFactory` ustawienie `withUrl`pola na obiekcie opcji w :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W kliencie Java SignalR można skonfigurować token nośny do użycia do uwierzytelniania, udostępniając fabrykę tokenów dostępu do [programu HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [zAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) aby zapewnić [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). Za pomocą wywołania [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), można napisać logikę do tworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymania przy życiu

Dodatkowe opcje konfigurowania limitu czasu i zachowania keep-alive są dostępne w samym `HubConnection` obiekcie:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `Closed` uwagę`onclose` serwer rozłączony i wyzwala zdarzenie (w języku JavaScript). Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `HandshakeTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `Closed` anuluje uzgadnianie i wyzwala zdarzenie (w`onclose` języku JavaScript). Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |

W kliencie .NET wartości limitu `TimeSpan` czasu są określane jako wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onclose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Limit czasu dla aktywności serwera. Jeśli serwer nie wysłał wiadomości w tym przedziale czasu, klient bierze pod `onClose` uwagę serwer rozłączony i wyzwala zdarzenie. Ta wartość musi być wystarczająco duża, aby wiadomość ping została wysłana z serwera **i** odebrana przez klienta w przedziale limitu czasu. Zalecana wartość to liczba co najmniej `KeepAliveInterval` dwukrotnie większa od wartości serwera, aby dać czas na przybycie pingów. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu dla uzgadniania serwera początkowego. Jeśli serwer nie wysyła odpowiedzi uzgadniania w tym przedziale czasu, klient `onClose` anuluje uzgadnianie i wyzwala zdarzenie. Jest to ustawienie zaawansowane, które powinno być modyfikowane tylko wtedy, gdy występują błędy limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej informacji na temat procesu uzgadniania, zobacz [Specyfikację protokołu SignalR Hub .](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md) |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować `WithUrl` `withUrl` w metodzie (w języku JavaScript) na `HubConnectionBuilder` `HttpHubConnectionBuilder` lub na różnych interfejsach API konfiguracji na kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania do uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie HTTP do wysłania przy każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysłania przy użyciu każdego żądania HTTP. |
| `CloseTimeout` | 5 sekund | WebSockets tylko. Maksymalny czas oczekiwania klienta po zamknięciu serwera na potwierdzenie żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient rozłącza się. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Pełnomocnik, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanego do wysyłania żądań HTTP. Nie jest używany dla połączeń WebSocket. Ten pełnomocnik musi zwrócić wartość nie null i odbiera wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej `HttpMessageHandler` i zwróć ją lub zwróć nowe wystąpienie. **Podczas zastępowania programu obsługi upewnij się, aby skopiować ustawienia, które mają być zachowane z podanego programu obsługi, w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw ten wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Pełnomocnik, który może służyć do konfigurowania dodatkowych opcji WebSocket. Odbiera wystąpienie [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) które mogą służyć do konfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `skipNegotiation` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania elementu nośnego w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw to, aby `true` pominąć krok negocjacji. **Obsługiwane tylko wtedy, gdy transport WebSockets jest jedynym włączonym transportem.** Tego ustawienia nie można włączyć podczas korzystania z usługi Azure SignalR. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania przy każdym żądaniu HTTP. |

---

W kliencie .NET opcje te mogą być modyfikowane `WithUrl`przez pełnomocnika opcji podanych do:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript opcje te mogą być dostępne `withUrl`w obiekcie JavaScript, który jest dostępny w celu:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java opcje te można skonfigurować `HttpHubConnectionBuilder` za pomocą metod zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
