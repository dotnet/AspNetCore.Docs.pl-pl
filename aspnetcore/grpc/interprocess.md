---
title: Komunikacja między procesami przy użyciu gRPC
author: jamesnk
description: Dowiedz się, jak używać gRPC do komunikacji między procesami.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: 7278ebd001aea4ba52c1134b3bac696c01950a27
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90723004"
---
# <a name="inter-process-communication-with-grpc"></a>Komunikacja między procesami przy użyciu gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

wywołania gRPC między klientem a usługą są zwykle wysyłane za pośrednictwem gniazd TCP. Protokół TCP został zaprojektowany do komunikacji przez sieć. [Komunikacja między procesami (IPC)](https://wikipedia.org/wiki/Inter-process_communication) jest bardziej wydajna niż w przypadku protokołu TCP, gdy klient i usługa znajdują się na tym samym komputerze. W tym dokumencie wyjaśniono, jak używać gRPC z niestandardowymi transportami w scenariuszach IPC.

## <a name="server-configuration"></a>Konfiguracja serwera

Niestandardowe transporty są obsługiwane przez [Kestrel](xref:fundamentals/servers/kestrel). Kestrel konfiguruje się w *program.cs*:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

Powyższy przykład:

* Konfiguruje punkty końcowe Kestrel w `ConfigureKestrel` .
* Wywołania <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nasłuchiwania w [gnieździe domeny systemu UNIX](https://wikipedia.org/wiki/Unix_domain_socket) z określoną ścieżką.

Kestrel ma wbudowaną obsługę punktów końcowych programu do tworzenia. W systemach Linux, macOS i [nowoczesnych wersje systemu Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)są obsługiwane.

## <a name="client-configuration"></a>Konfiguracja klientów

`GrpcChannel` obsługuje wykonywanie wywołań gRPC przez transporty niestandardowe. Po utworzeniu kanału można go skonfigurować przy użyciu programu z `SocketsHttpHandler` niestandardowym `ConnectCallback` . Wywołanie zwrotne pozwala klientowi na nawiązywanie połączeń przez transporty niestandardowe, a następnie wysyłanie żądań HTTP przez ten transport.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` jest nowym interfejsem API w programie .NET 5 Release Candidate 2.

Przykład fabryki połączeń gniazd domen systemu UNIX:

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

Tworzenie kanału przy użyciu fabryki niestandardowych połączeń:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Kanały utworzone za pomocą powyższego kodu wysyłają wywołania gRPC za pośrednictwem gniazd domen systemu UNIX. Obsługę innych technologii IPC można zaimplementować przy użyciu rozszerzalności w Kestrel i `SocketsHttpHandler` .
