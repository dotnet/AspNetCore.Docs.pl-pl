---
title: Najlepsze rozwiązania w zakresie wydajności
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
ms.openlocfilehash: c6f6a9e5c9aa2f01209c8457a848dc6ec1f5ed88
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88866190"
---
# <a name="performance-best-practices"></a>Najlepsze rozwiązania w zakresie wydajności

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

gRPC jest przeznaczony dla usług o wysokiej wydajności. W tym dokumencie wyjaśniono, jak uzyskać najlepszą wydajność z gRPC.

## <a name="reuse-channel"></a>Użyj ponownie kanału

Podczas wykonywania wywołań gRPC należy ponownie użyć kanału gRPC. Ponowne użycie kanału umożliwia wywoływanie wywołań przez istniejące połączenie HTTP/2.

Jeśli dla każdego wywołania gRPC zostanie utworzony nowy kanał, ilość czasu, jaką zajmie ukończenie, może znacząco wzrosnąć. Każde wywołanie będzie wymagało wielu operacji sieciowych między klientem a serwerem w celu utworzenia połączenia HTTP/2:

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

::: moniker range=">= aspnetcore-5.0"

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
