---
title: SignalRKonfiguracja ASP.NET Core
author: bradygaster
description: Dowiedz się, jak skonfigurować SignalR aplikacje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406839"
---
# <a name="aspnet-core-signalr-configuration"></a>SignalRKonfiguracja ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Opcje serializacji JSON/MessagePack

ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` . `AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt. Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).

Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:

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
> W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Przełącz do Newtonsoft.Jsna

Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opcje serializacji MessagePack

Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania SignalR centrów:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale). Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane. Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia. W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez to centrum. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów. |
| `EnableDetailedErrors` | `false` | Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum. Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne. |
| `StreamBufferCapacity` | `10` | Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów. Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.|
| `MaximumReceiveMessageSize` | 32 KB | Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum. |

Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .

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

Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci. |
| `AuthorizationData` | Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji. Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla długiego transportu sondowania. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu obiektów WebSockets. |
| `MinimumProtocolVersion` | 0 | Określ minimalną wersję protokołu negocjowania. Służy do ograniczania liczby klientów do nowszych wersji. |

W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej. |

Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane. |
| `SubProtocolSelector` | `null` | Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową. Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript). Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody. Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety. Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.

Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet. Wywołaj `AddConsole` metodę rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna Metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika. Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

Poniższa tabela zawiera listę dostępnych poziomów dzienników. Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany. Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.

| Ciąg                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **lub** `information` | `LogLevel.Information` |
| `warn` **lub** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).

SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania. Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można je bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Skonfiguruj dozwolone transporty

Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript). Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.

W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` . Klient Java domyślnie używa transportu usługi WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania okaziciela

Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ). W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets). W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .

W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html). Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymywania aktywności

W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `HandshakeTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysyłania przy każdym żądaniu HTTP. |
| `CloseTimeout` | 5 sekund | Tylko obiekty WebSockets. Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP. Nieużywane na potrzeby połączeń protokołu WebSocket. Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie. **Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket. Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja języka JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `headers` | `null` | Słownik nagłówków wysłanych z każdym żądaniem HTTP. Wysyłanie nagłówków w przeglądarce nie działa w przypadku obiektów WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> strumienia. |
| `logMessageContent` | `null` | Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta. |
| `skipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `withCredentials` | `true` | Określa, czy poświadczenia będą wysyłane z żądaniem CORS. Azure App Service używa plików cookie dla sesji programu Sticky i wymaga, aby ta opcja działała poprawnie. Aby uzyskać więcej informacji na temat mechanizmu CORS w programie SignalR , zobacz <xref:signalr/security#cross-origin-resource-sharing> . |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |

---

W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Opcje serializacji JSON/MessagePack

ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` . `AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt. Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).

Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:

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
> W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Przełącz do Newtonsoft.Jsna

Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opcje serializacji MessagePack

Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania SignalR centrów:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale). Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane. Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia. W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez to centrum. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów. |
| `EnableDetailedErrors` | `false` | Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum. Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne. |
| `StreamBufferCapacity` | `10` | Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów. Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.|
| `MaximumReceiveMessageSize` | 32 KB | Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum. |

Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .

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

Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci. |
| `AuthorizationData` | Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji. Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla długiego transportu sondowania. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu obiektów WebSockets. |
| `MinimumProtocolVersion` | 0 | Określ minimalną wersję protokołu negocjowania. Służy do ograniczania liczby klientów do nowszych wersji. |

W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej. |

Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane. |
| `SubProtocolSelector` | `null` | Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową. Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript). Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody. Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety. Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.

Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet. Wywołaj `AddConsole` metodę rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna Metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika. Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

Poniższa tabela zawiera listę dostępnych poziomów dzienników. Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany. Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.

| Ciąg                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **lub** `information` | `LogLevel.Information` |
| `warn` **lub** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).

SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania. Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można je bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Skonfiguruj dozwolone transporty

Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript). Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.

W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` . Klient Java domyślnie używa transportu usługi WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania okaziciela

Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ). W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets). W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .

W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html). Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymywania aktywności

W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `HandshakeTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysyłania przy każdym żądaniu HTTP. |
| `CloseTimeout` | 5 sekund | Tylko obiekty WebSockets. Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP. Nieużywane na potrzeby połączeń protokołu WebSocket. Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie. **Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket. Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja języka JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `logMessageContent` | `null` | Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta. |
| `skipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |

---

W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`można dodać po elemencie [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w `Startup.ConfigureServices` . `AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt. Właściwość [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tego obiektu jest `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> obiektem, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zobacz [System.Text.Jsw dokumentacji](/dotnet/api/system.text.json).

Przykładowo, aby skonfigurować Serializator, aby nie zmieniać wielkości liter nazw właściwości zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:

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
> W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Przełącz do Newtonsoft.Jsna

Jeśli potrzebujesz funkcji programu `Newtonsoft.Json` , które nie są obsługiwane w programie `System.Text.Json` , zobacz [Przełącz do Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opcje serializacji MessagePack

Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania SignalR centrów:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale). Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane. Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia. W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez to centrum. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów. |
| `EnableDetailedErrors` | `false` | Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum. Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne. |
| `StreamBufferCapacity` | `10` | Maksymalna liczba elementów, które mogą być buforowane dla strumieni przekazywania klientów. Po osiągnięciu tego limitu przetwarzanie wywołań jest blokowane, dopóki serwer nie przetwarza elementów strumienia.|
| `MaximumReceiveMessageSize` | 32 KB | Maksymalny rozmiar pojedynczego przychodzącego komunikatu centrum. |

Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .

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

Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) w `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforują serwer przed zastosowaniem nadużycia. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów szybciej bez naciskania, ale może zwiększyć zużycie pamięci. |
| `AuthorizationData` | Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, które buforuje serwer przed zaobserwowaniem presji. Zwiększenie tej wartości umożliwia serwerowi przebuforowanie większych komunikatów szybciej bez czekania na obciążenie, ale może zwiększyć zużycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla długiego transportu sondowania. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu obiektów WebSockets. |

W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej. |

Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane. |
| `SubProtocolSelector` | `null` | Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową. Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript). Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody. Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety. Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.

Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet. Wywołaj `AddConsole` metodę rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna Metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Zamiast `LogLevel` wartości można także podać `string` wartość reprezentującą nazwę poziomu dziennika. Jest to przydatne podczas konfigurowania SignalR rejestrowania w środowiskach, w których nie masz dostępu do `LogLevel` stałych.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

Poniższa tabela zawiera listę dostępnych poziomów dzienników. Wartość określana przez użytkownika w celu ustawienia `configureLogging` **minimalnego** poziomu dziennika, który zostanie zarejestrowany. Komunikaty zarejestrowane na tym poziomie **lub poziomy wymienione po nim w tabeli**zostaną zarejestrowane.

| Ciąg                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **lub** `information` | `LogLevel.Information` |
| `warn` **lub** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).

SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania. Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można je bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Skonfiguruj dozwolone transporty

Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript). Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.

W kliencie Java, transport jest wybierany przy użyciu `withTransport` metody w `HttpHubConnectionBuilder` . Klient Java domyślnie używa transportu usługi WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRKlient Java nie obsługuje jeszcze rezerwy transportowej.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania okaziciela

Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ). W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets). W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .

W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html). Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymywania aktywności

W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `HandshakeTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysyłania przy każdym żądaniu HTTP. |
| `CloseTimeout` | 5 sekund | Tylko obiekty WebSockets. Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP. Nieużywane na potrzeby połączeń protokołu WebSocket. Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie. **Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket. Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja języka JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `logMessageContent` | `null` | Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta. |
| `skipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |

---

W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może zostać dodana po metodzie [addsygnalizująca](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w ramach `Startup.ConfigureServices` metody. `AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt. Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:

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
> W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="messagepack-serialization-options"></a>Opcje serializacji MessagePack

Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania SignalR centrów:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Serwer Rozważ odłączenie klienta, jeśli nie odebrano komunikatu (w tym w tym interwale). Może upłynąć dłużej niż ten interwał limitu czasu, aby klient mógł zostać oznaczony jako odłączony, ze względu na to, jak jest to zaimplementowane. Zalecaną wartością jest podwójna `KeepAliveInterval` wartość.|
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia. W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez to centrum. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów. |
| `EnableDetailedErrors` | `false` | Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum. Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne. |

Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .

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

Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. |
| `AuthorizationData` | Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer. Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla długiego transportu sondowania. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu obiektów WebSockets. |

W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej. |

Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane. |
| `SubProtocolSelector` | `null` | Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową. Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript). Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody. Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety. Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.

Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet. Wywołaj `AddConsole` metodę rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna Metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).

SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania. Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można je bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Skonfiguruj dozwolone transporty

Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript). Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

W tej wersji elementu WebSockets klienta Java jest jedynym dostępnym transportem.

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania okaziciela

Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ). W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets). W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .

W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html). Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymywania aktywności

W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `HandshakeTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 MS) | Określa interwał wysyłania komunikatów ping przez klienta. Wysłanie wszelkich komunikatów z klienta powoduje zresetowanie czasomierza do początku interwału. Jeśli klient nie wysłał komunikatu w `ClientTimeoutInterval` zestawie na serwerze, serwer traktuje klienta jako odłączony. |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysyłania przy każdym żądaniu HTTP. |
| `CloseTimeout` | 5 sekund | Tylko obiekty WebSockets. Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP. Nieużywane na potrzeby połączeń protokołu WebSocket. Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie. **Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket. Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja języka JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `logMessageContent` | `null` | Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta. |
| `skipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |

---

W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

ASP.NET Core SignalR obsługuje dwa protokoły dla komunikatów kodowania: [JSON](https://www.json.org/) i [MessagePack](https://msgpack.org/index.html). Każdy protokół ma opcje konfiguracji serializacji.

Serializacji JSON można skonfigurować na serwerze przy użyciu metody rozszerzenia [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , która może zostać dodana po metodzie [addsygnalizująca](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) w ramach `Startup.ConfigureServices` metody. `AddJsonProtocol`Metoda przyjmuje delegata, który odbiera `options` obiekt. Właściwość [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tego obiektu jest `JsonSerializerSettings` obiektem JSON.NET, którego można użyć do skonfigurowania serializacji argumentów i zwracanych wartości. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Przykładowo, aby skonfigurować serializator do używania nazw właściwości "PascalCase" zamiast domyślnych nazw "camelCase", należy użyć następującego kodu w `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

W kliencie .NET ta sama `AddJsonProtocol` Metoda rozszerzająca istnieje w [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Przestrzeń nazw musi zostać zaimportowana, aby można było rozpoznać metodę rozszerzenia:

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
> W tej chwili nie można skonfigurować serializacji JSON w kliencie JavaScript.

### <a name="messagepack-serialization-options"></a>Opcje serializacji MessagePack

Serializacji MessagePack można skonfigurować, dostarczając delegata do wywołania [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Aby uzyskać więcej informacji, zobacz [MessagePack SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> W tej chwili nie można skonfigurować serializacji MessagePack w kliencie JavaScript.

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

W poniższej tabeli opisano opcje konfigurowania SignalR centrów:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 sekund | Jeśli klient nie wyśle początkowego komunikatu uzgadniania w tym przedziale czasu, połączenie zostanie zamknięte. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Jeśli serwer nie wysłał komunikatu w tym interwale, zostanie automatycznie wysłana wiadomość ping w celu pozostawienia otwartego połączenia. W przypadku zmiany `KeepAliveInterval` należy zmienić `ServerTimeout` / `serverTimeoutInMilliseconds` ustawienie na kliencie. Zalecaną `ServerTimeout` / `serverTimeoutInMilliseconds` wartością jest podwójna `KeepAliveInterval` wartość.  |
| `SupportedProtocols` | Wszystkie zainstalowane protokoły | Protokoły obsługiwane przez to centrum. Domyślnie wszystkie protokoły zarejestrowane na serwerze są dozwolone, ale protokoły można usunąć z tej listy, aby wyłączyć określone protokoły dla poszczególnych centrów. |
| `EnableDetailedErrors` | `false` | Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie centrum. Wartość domyślna to `false` , ponieważ te komunikaty o wyjątkach mogą zawierać informacje poufne. |

Opcje można skonfigurować dla wszystkich centrów, dostarczając opcje delegata do `AddSignalR` wywołania w `Startup.ConfigureServices` .

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

Opcje jednego centrum przesłaniają opcje globalne podane w `AddSignalR` i można je skonfigurować przy użyciu <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Zaawansowane opcje konfiguracji HTTP

Służy `HttpConnectionDispatcherOptions` do konfigurowania ustawień zaawansowanych związanych z transportami i zarządzaniem buforem pamięci. Te opcje są konfigurowane przez przekazanie delegata [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) w `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

W poniższej tabeli opisano opcje konfigurowania SignalR zaawansowanych opcji HTTP ASP.NET Core:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maksymalna liczba bajtów odebranych od klienta, które buforuje serwer. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. |
| `AuthorizationData` | Dane zbierane automatycznie z `Authorize` atrybutów zastosowanych do klasy centrum. | Lista obiektów [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) służąca do określenia, czy klient jest autoryzowany do łączenia się z centrum. |
| `TransportMaxBufferSize` | 32 KB | Maksymalna liczba bajtów wysłanych przez aplikację, które buforują serwer. Zwiększenie tej wartości umożliwia serwerowi wysyłanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. |
| `Transports` | Wszystkie transporty są włączone. | Wyliczenie flag bitowych `HttpTransportType` wartości, które mogą ograniczyć transporty, których klient może użyć do nawiązania połączenia. |
| `LongPolling` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla długiego transportu sondowania. |
| `WebSockets` | Sprawdź poniżej. | Dodatkowe opcje specyficzne dla transportu obiektów WebSockets. |

W przypadku długiego transportu sondowania dostępne są dodatkowe opcje, które można skonfigurować przy użyciu `LongPolling` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maksymalny czas, przez jaki serwer czeka na wysłanie wiadomości do klienta przed zakończeniem jednego żądania sondowania. Zmniejszenie tej wartości powoduje, że klient wystawia nowe żądania sondy częściej. |

Transport WebSocket ma dodatkowe opcje, które można skonfigurować przy użyciu `WebSockets` Właściwości:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Gdy serwer zostanie zamknięty, jeśli klient nie zostanie zamknięty w tym okresie, połączenie zostanie przerwane. |
| `SubProtocolSelector` | `null` | Delegat, którego można użyć do ustawienia `Sec-WebSocket-Protocol` nagłówka na wartość niestandardową. Delegat otrzymuje wartości żądane przez klienta jako dane wejściowe i oczekuje na zwrócenie żądanej wartości. |

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Opcje klienta można skonfigurować dla `HubConnectionBuilder` typu (dostępnego w klientach .NET i JavaScript). Jest ona również dostępna w kliencie Java, ale `HttpHubConnectionBuilder` podklasa zawiera opcje konfiguracji konstruktora, a także `HubConnection` samego siebie.

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

Rejestrowanie jest konfigurowane w kliencie .NET przy użyciu `ConfigureLogging` metody. Dostawcy i filtry rejestrowania mogą być rejestrowane w taki sam sposób, w jaki znajdują się na serwerze. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [rejestrowania w ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby zarejestrować dostawców rejestrowania, należy zainstalować wymagane pakiety. Aby zapoznać się z pełną listą, zobacz sekcję [wbudowane dostawcy rejestrowania](xref:fundamentals/logging/index#built-in-logging-providers) w witrynie docs.

Na przykład aby włączyć rejestrowanie konsoli, zainstaluj `Microsoft.Extensions.Logging.Console` pakiet NuGet. Wywołaj `AddConsole` metodę rozszerzenia:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

W kliencie JavaScript `configureLogging` istnieje podobna Metoda. Podaj `LogLevel` wartość wskazującą minimalny poziom komunikatów dziennika do wygenerowania. Dzienniki są zapisywane w oknie konsoli przeglądarki.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.

Aby uzyskać więcej informacji na temat rejestrowania, zobacz [ SignalR dokumentację diagnostyki](xref:signalr/diagnostics).

SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania. Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można je bezpiecznie zignorować.

### <a name="configure-allowed-transports"></a>Skonfiguruj dozwolone transporty

Transporty używane przez program SignalR można skonfigurować w `WithUrl` wywołaniu ( `withUrl` w języku JavaScript). Wartości bitowe i `HttpTransportType` można użyć, aby ograniczyć klient do używania tylko określonych transportów. Wszystkie transporty są domyślnie włączone.

Na przykład, aby wyłączyć transport zdarzeń wysłanych przez serwer, ale zezwolić na połączenia z usługą WebSockets i długie sondowanie:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

W kliencie JavaScript transporty są konfigurowane przez ustawienie `transport` pola w obiekcie Options dostarczonego do `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Konfigurowanie uwierzytelniania okaziciela

Aby zapewnić dane uwierzytelniania wraz z SignalR żądaniami, użyj `AccessTokenProvider` opcji ( `accessTokenFactory` w języku JavaScript), aby określić funkcję, która zwraca żądany token dostępu. W kliencie .NET token dostępu jest przenoszona jako token "uwierzytelnianie okaziciela" protokołu HTTP (przy użyciu `Authorization` nagłówka z typem `Bearer` ). W kliencie JavaScript token dostępu jest używany jako token okaziciela, **z wyjątkiem** kilku przypadków, w których interfejsy API przeglądarki ograniczają możliwość zastosowania nagłówków (w konkretnym przypadku zdarzeń wysyłanych przez serwer i żądań WebSockets). W takich przypadkach token dostępu jest podawany jako wartość ciągu zapytania `access_token` .

W kliencie .NET `AccessTokenProvider` opcji można określić za pomocą delegata opcji w `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

W kliencie JavaScript token dostępu jest konfigurowany przez ustawienie `accessTokenFactory` pola w obiekcie Options w `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając fabrykę tokenów dostępu do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html). Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurowanie opcji limitu czasu i utrzymywania aktywności

W samym obiekcie są dostępne dodatkowe opcje konfigurowania trybu przekroczenia limitu czasu i zachowania utrzymywania aktywności `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |
| `HandshakeTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `Closed` zdarzenie ( `onclose` w języku JavaScript). Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

W kliencie .NET wartości limitu czasu są określone jako `TimeSpan` wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onclose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną wartością serwera, `KeepAliveInterval` Aby umożliwić czas na nadejście poleceń ping. |

# <a name="java"></a>[Java](#tab/java)

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 MS) | Limit czasu dla działania serwera. Jeśli serwer nie wysłał komunikatu w tym interwale, Klient uważa, że serwer odłączył się i wyzwala `onClose` zdarzenie. Ta wartość musi być wystarczająca do wysłania wiadomości ping z serwera **i** odebrana przez klienta w ramach przedziału czasu. Zalecana wartość to liczba z co najmniej podwójną `KeepAliveInterval` wartością serwera, aby umożliwić czas na nadejście poleceń ping. |
| `withHandshakeResponseTimeout` | 15 sekund | Limit czasu początkowej uzgadniania z serwerem. Jeśli serwer nie wyśle odpowiedzi uzgadniania w tym interwale, klient anuluje uzgadnianie i wyzwala `onClose` zdarzenie. Jest to ustawienie zaawansowane, które należy zmodyfikować tylko w przypadku wystąpienia błędów limitu czasu uzgadniania z powodu poważnego opóźnienia sieci. Aby uzyskać więcej szczegółów na temat procesu uzgadniania, zobacz [ SignalR specyfikację protokołu centrum](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Konfigurowanie opcji dodatkowych

Dodatkowe opcje można skonfigurować w `WithUrl` `withUrl` metodzie (w języku JavaScript) dla `HubConnectionBuilder` lub w różnych interfejsach API konfiguracji w `HttpHubConnectionBuilder` kliencie Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opcja .NET |  Wartość domyślna | Opis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `SkipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `ClientCertificates` | Pusty | Kolekcja certyfikatów TLS do wysłania w celu uwierzytelniania żądań. |
| `Cookies` | Pusty | Kolekcja plików cookie protokołu HTTP do wysłania w każdym żądaniu HTTP. |
| `Credentials` | Pusty | Poświadczenia do wysyłania przy każdym żądaniu HTTP. |
| `CloseTimeout` | 5 sekund | Tylko obiekty WebSockets. Maksymalny czas oczekiwania klienta po zamknięciu serwera w celu potwierdzenia żądania zamknięcia. Jeśli serwer nie potwierdzi zamknięcia w tym czasie, klient zostanie rozłączony. |
| `Headers` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegat, który może służyć do konfigurowania lub zastępowania `HttpMessageHandler` używanych do wysyłania żądań HTTP. Nieużywane na potrzeby połączeń protokołu WebSocket. Ten delegat musi zwracać wartość różną od null i otrzymuje wartość domyślną jako parametr. Zmodyfikuj ustawienia tej wartości domyślnej i zwróć ją lub Zwróć nowe `HttpMessageHandler` wystąpienie. **Podczas zastępowania programu obsługi należy skopiować ustawienia, które mają być zachowane z podanej procedury obsługi. w przeciwnym razie skonfigurowane opcje (takie jak pliki cookie i nagłówki) nie będą miały zastosowania do nowego programu obsługi.** |
| `Proxy` | `null` | Serwer proxy HTTP, który ma być używany podczas wysyłania żądań HTTP. |
| `UseDefaultCredentials` | `false` | Ustaw tę wartość logiczną, aby wysyłać domyślne poświadczenia dla żądań HTTP i WebSockets. Umożliwia to korzystanie z uwierzytelniania systemu Windows. |
| `WebSocketConfiguration` | `null` | Delegat, który może służyć do konfigurowania dodatkowych opcji protokołu WebSocket. Odbiera wystąpienie elementu [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , którego można użyć do skonfigurowania opcji. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opcja języka JavaScript | Wartość domyślna | Opis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `logMessageContent` | `null` | Ustaw na `true` , aby rejestrować bajty/znaki komunikatów wysyłanych i odbieranych przez klienta. |
| `skipNegotiation` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |

# <a name="java"></a>[Java](#tab/java)

| Opcja Java | Wartość domyślna | Opis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkcja zwracająca ciąg, który jest dostarczany jako token uwierzytelniania okaziciela w żądaniach HTTP. |
| `shouldSkipNegotiate` | `false` | Ustaw tę wartość na `true` , aby pominąć krok negocjowania. **Obsługiwane tylko wtedy, gdy transport Sockets jest jedynym włączonym transportem**. Nie można włączyć tego ustawienia w przypadku korzystania z SignalR usługi platformy Azure. |
| `withHeader` `withHeaders` | Pusty | Mapa dodatkowych nagłówków HTTP do wysłania w każdym żądaniu HTTP. |

---

W kliencie platformy .NET te opcje można modyfikować za pomocą delegata opcji udostępnianego dla `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

W kliencie JavaScript te opcje można podać w obiekcie JavaScript udostępnionym `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

W kliencie Java Opcje te można skonfigurować przy użyciu metod `HttpHubConnectionBuilder` zwróconych z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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
