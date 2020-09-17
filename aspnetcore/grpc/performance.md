---
title: Najlepsze rozwiązania w zakresie wydajności z gRPC
author: jamesnk
description: Poznaj najlepsze rozwiązania dotyczące tworzenia usług gRPC o wysokiej wydajności.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
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
uid: grpc/performance
ms.openlocfilehash: 4d50698b8c55f7fb3ef9a2c3102e73e046a22a9c
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722848"
---
# <a name="performance-best-practices-with-grpc"></a>Najlepsze rozwiązania w zakresie wydajności z gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

gRPC jest przeznaczony dla usług o wysokiej wydajności. W tym dokumencie wyjaśniono, jak uzyskać najlepszą wydajność z gRPC.

## <a name="reuse-grpc-channels"></a>Ponowne używanie kanałów gRPC

Podczas wykonywania wywołań gRPC należy ponownie użyć kanału gRPC. Ponowne użycie kanału umożliwia wywoływanie wywołań przez istniejące połączenie HTTP/2.

Jeśli dla każdego wywołania gRPC zostanie utworzony nowy kanał, ilość czasu, jaką zajmie ukończenie, może znacząco wzrosnąć. Każde wywołanie będzie wymagało wielu operacji sieciowych między klientem a serwerem w celu utworzenia nowego połączenia HTTP/2:

1. Otwieranie gniazda
2. Nawiązywanie połączenia TCP
3. Negocjowanie protokołu TLS
4. Uruchamianie połączenia HTTP/2
5. Wykonywanie wywołania gRPC

Kanały można bezpiecznie udostępniać i ponownie używać między gRPC wywołań:

* gRPC klienci są tworzone za pomocą kanałów. gRPC klienci są obiektami lekkimi i nie muszą być buforowane ani ponownie używane.
* Wielu klientów gRPC można utworzyć na podstawie kanału, w tym różnych typów klientów.
* Kanał i klienci utworzeni z kanału mogą być bezpiecznie używani przez wiele wątków.
* Klienci utworzeni z kanału mogą wykonywać wiele jednoczesnych wywołań.

Fabryka klientów gRPC oferuje scentralizowany sposób konfigurowania kanałów. Automatycznie ponownie używa kanałów bazowych. Aby uzyskać więcej informacji, zobacz <xref:grpc/clientfactory>.

## <a name="connection-concurrency"></a>Współbieżność połączenia

Połączenia HTTP/2 zwykle mają limit [maksymalnej liczby równoczesnych strumieni (aktywne żądania HTTP)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) w ramach połączenia. Domyślnie większość serwerów ustawia ten limit na 100 strumieni współbieżnych.

Kanał gRPC korzysta z pojedynczego połączenia HTTP/2, a współbieżne wywołania są multipleksne dla tego połączenia. Gdy liczba aktywnych wywołań osiągnie limit strumienia połączeń, dodatkowe wywołania są umieszczane w kolejce na kliencie. Wywołania w kolejce oczekują na zakończenie aktywnych wywołań przed ich wysłaniem. Aplikacje o dużym obciążeniu lub długotrwałych wywołaniach gRPC mogą zobaczyć problemy z wydajnością spowodowane przez wywołania kolejkowania z powodu tego limitu.

::: moniker range=">= aspnetcore-5.0"

W programie .NET 5 wprowadzono `SocketsHttpHandler.EnableMultipleHttp2Connections` Właściwość. W przypadku ustawienia opcji `true` dodatkowe połączenia HTTP/2 są tworzone przez kanał po osiągnięciu współbieżnego limitu strumienia. Gdy `GrpcChannel` jest tworzony, jego wewnętrzny `SocketsHttpHandler` jest automatycznie konfigurowany do tworzenia dodatkowych połączeń HTTP/2. Jeśli aplikacja skonfiguruje własną obsługę, należy rozważyć ustawienie `EnableMultipleHttp2Connections` `true` :

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

Istnieją kilka obejść dla aplikacji platformy .NET Core 3,1:

* Utwórz oddzielne kanały gRPC dla obszarów aplikacji o dużym obciążeniu. Na przykład `Logger` Usługa gRPC może mieć duże obciążenie. Aby utworzyć `LoggerClient` aplikację w aplikacji, użyj oddzielnego kanału.
* Użyj puli kanałów gRPC, na przykład utwórz listę kanałów gRPC. `Random` służy do wybierania kanału z listy za każdym razem, gdy wymagany jest kanał gRPC. Używanie `Random` losowo dystrybuowanych wywołań przez wiele połączeń.

> [!IMPORTANT]
> Zwiększenie maksymalnego limitu współbieżności strumienia na serwerze jest innym sposobem rozwiązania tego problemu. W Kestrel jest to konfiguracja przy użyciu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .
>
> Zwiększenie maksymalnego limitu współbieżności strumienia nie jest zalecane. Zbyt wiele strumieni na pojedynczym połączeniu HTTP/2 wprowadza nowe problemy z wydajnością:
>
> * Rywalizacja o wątki między strumieniami próbującymi wykonać zapis w połączeniu.
> * Utrata pakietów połączenia powoduje, że wszystkie wywołania są blokowane w warstwie TCP.

## <a name="load-balancing"></a>Równoważenie obciążenia

Niektóre usługi równoważenia obciążenia nie działają efektywnie z usługą gRPC. Usługi równoważenia obciążenia P4 (transport) działają na poziomie połączenia przez dystrybuowanie połączeń TCP między punktami końcowymi. Takie podejście dobrze sprawdza się w przypadku ładowania wywołań interfejsu API równoważenia przy użyciu protokołu HTTP/1.1. Współbieżne wywołania wykonane przy użyciu protokołu HTTP/1.1 są wysyłane w różnych połączeniach, co umożliwia Równoważenie obciążenia między punktami końcowymi.

Moduły równoważenia obciążenia P4 działają na poziomie połączenia, ale nie działają prawidłowo z gRPC. gRPC używa protokołu HTTP/2, który multipleksuje wiele wywołań w jednym połączeniu TCP. Wszystkie wywołania gRPC za pośrednictwem tego połączenia przejdą do jednego punktu końcowego.

Dostępne są dwie opcje efektywnego równoważenia obciążenia gRPC:

* Równoważenie obciążenia po stronie klienta
* Równoważenie obciążenia serwera proxy P7 (aplikacji)

> [!NOTE]
> Tylko wywołania gRPC można równoważyć obciążeniem między punktami końcowymi. Po nawiązaniu połączenia gRPC przesyłania strumieniowego wszystkie komunikaty wysyłane przez strumień są przenoszone do jednego punktu końcowego.

### <a name="client-side-load-balancing"></a>Równoważenie obciążenia po stronie klienta

Za pomocą równoważenia obciążenia po stronie klienta Klient zna informacje o punktach końcowych. Dla każdego wywołania gRPC wybiera inny punkt końcowy do wysłania wywołania. Równoważenie obciążenia po stronie klienta jest dobrym rozwiązaniem, gdy opóźnienie jest ważne. Między klientem a usługą nie istnieje serwer proxy, dlatego wywołanie jest wysyłane bezpośrednio do usługi. Minusem do równoważenia obciążenia po stronie klienta polega na tym, że każdy klient musi śledzić dostępne punkty końcowe, których należy użyć.

Referencyjna równoważenia obciążenia klienta jest techniką, w której stan równoważenia obciążenia jest przechowywany w centralnej lokalizacji. Klienci okresowo wysyłają zapytanie do centralnej lokalizacji, aby uzyskać informacje do użycia podczas podejmowania decyzji dotyczących równoważenia obciążenia.

`Grpc.Net.Client` obecnie nie obsługuje równoważenia obciążenia po stronie klienta. [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) to dobry wybór w przypadku, gdy w programie .NET jest wymagane Równoważenie obciążenia po stronie klienta.

### <a name="proxy-load-balancing"></a>Równoważenie obciążenia serwera proxy

Serwer proxy P7 (aplikacja) działa na wyższym poziomie niż serwer proxy P4 (transport). Serwery proxy P7 mają świadomość protokołu HTTP/2 i umożliwiają dystrybucję wywołań gRPC do serwera proxy na jednym połączeniu HTTP/2 w wielu punktach końcowych. Użycie serwera proxy jest prostsze niż Równoważenie obciążenia po stronie klienta, ale może zwiększyć dodatkowe opóźnienie dla wywołań gRPC.

Istnieje wiele dostępnych serwerów proxy P7. Dostępne są następujące opcje:

* [Wysłannika](https://www.envoyproxy.io/) — popularny serwer proxy typu open source.
* [Łącząca](https://linkerd.io/) się z siatką usługi dla Kubernetes.
* [YARP: odwrotny serwer](https://microsoft.github.io/reverse-proxy/) proxy — Podgląd typu open source zapisany w środowisku .NET.

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>Komunikacja między procesami

wywołania gRPC między klientem a usługą są zwykle wysyłane za pośrednictwem gniazd TCP. Protokół TCP doskonale nadaje się do komunikacji w sieci, ale [komunikacja między procesami (IPC)](https://wikipedia.org/wiki/Inter-process_communication) jest bardziej wydajna, gdy klient i usługa znajdują się na tym samym komputerze.

Rozważ użycie transportu, takiego jak gniazda domeny systemu UNIX lub nazwane potoki, dla wywołań gRPC między procesami na tym samym komputerze. Aby uzyskać więcej informacji, zobacz <xref:grpc/interprocess>.

## <a name="keep-alive-pings"></a>Utrzymywanie aktywności poleceń ping

Polecenia ping utrzymywania aktywności mogą służyć do utrzymywania połączeń HTTP/2 w okresach braku aktywności. Istniejące połączenie HTTP/2 jest gotowe, gdy aplikacja wznawia działanie, umożliwia szybkie wywoływanie gRPC, bez opóźnień spowodowanych przez ponowne nawiązanie połączenia.

Monitoruj polecenia ping dla <xref:System.Net.Http.SocketsHttpHandler> :

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

Poprzedni kod konfiguruje kanał, który wysyła polecenie ping Keep Alive do serwera co 60 sekund w okresach braku aktywności. Polecenie ping gwarantuje, że serwer i wszystkie używane serwery proxy nie będą zamykać połączenia z powodu braku aktywności.

::: moniker-end

## <a name="streaming"></a>Przesyłanie strumieniowe

gRPC dwukierunkowe przesyłanie strumieniowe może służyć do zastępowania jednoargumentowych wywołań gRPC w scenariuszach o wysokiej wydajności. Po rozpoczęciu strumienia dwukierunkowego komunikaty przesyłające strumieniowo i z powrotem są szybsze niż wysyłanie komunikatów z wieloma jednoargumentowymi wywołaniami gRPC. Komunikaty przesyłane strumieniowo są wysyłane jako dane na istniejącym żądaniu HTTP/2 i eliminuje obciążenie związane z tworzeniem nowego żądania HTTP/2 dla każdego wywołania jednoargumentowego.

Przykładowa usługa:

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

Przykładowy klient:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

Zamiana wywołań jednoargumentowych z dwukierunkowym przesyłaniem strumieniowym ze względu na wydajność jest zaawansowaną techniką i nie jest odpowiednia w wielu sytuacjach

Używanie wywołań przesyłania strumieniowego jest dobrym rozwiązaniem w przypadku:

1. Wymagana jest Wysoka przepływność lub małe opóźnienia.
2. gRPC i HTTP/2 są identyfikowane jako wąskie gardła wydajności.
3. Proces roboczy w kliencie wysyła lub otrzymuje zwykłe wiadomości za pomocą usługi gRPC.

Należy pamiętać o dodatkowej złożoności i ograniczeniach użycia wywołań przesyłania strumieniowego zamiast jednoargumentowego:

1. Strumień może zostać przerwany przez usługę lub błąd połączenia. Jeśli wystąpi błąd, logika jest wymagana do ponownego uruchomienia strumienia.
2. `RequestStream.WriteAsync` nie jest bezpieczna w przypadku wielowątkowości. W danym momencie może być zapisany tylko jeden komunikat w strumieniu. Wysyłanie komunikatów z wielu wątków za pośrednictwem pojedynczego strumienia wymaga kolejki producent/odbiorca, która jest taka sama jak <xref:System.Threading.Channels.Channel%601> w przypadku komunikatów.
3. Metoda przesyłania strumieniowego gRPC jest ograniczona do odebrania jednego typu komunikatu i wysłania jednego typu wiadomości. Na przykład `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` odbiera `RequestMessage` i wysyła `ResponseMessage` . Protobuf obsługę komunikatów nieznanych lub warunkowych przy użyciu `Any` i może obejść `oneof` to ograniczenie.
