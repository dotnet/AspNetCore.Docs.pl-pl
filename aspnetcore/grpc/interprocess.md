---
title: Komunikacja między procesami z gRPC
author: jamesnk
description: Dowiedz się, jak używać gRPC do komunikacji między procesami.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945765"
---
# <a name="inter-process-communication-with-grpc"></a>Komunikacja między procesami z gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

wywołania gRPC między klientem a usługą są zwykle wysyłane za pośrednictwem gniazd TCP. Protokół TCP doskonale nadaje się do komunikacji w sieci, ale [komunikacja między procesami (IPC)](https://wikipedia.org/wiki/Inter-process_communication) jest bardziej wydajna, gdy klient i usługa znajdują się na tym samym komputerze. W tym dokumencie wyjaśniono, jak używać gRPC z niestandardowymi transportami w scenariuszach IPC.

## <a name="server-configuration"></a>Konfiguracja serwera

Niestandardowe transporty są obsługiwane przez Kestrel. Kestrel konfiguruje się w *program.cs*:

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
* Wywołania <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nasłuchiwania w [gnieździe domeny systemu UNIX](https://en.wikipedia.org/wiki/Unix_domain_socket) z określoną ścieżką.

Kestrel ma wbudowaną obsługę punktów końcowych programu do tworzenia. W systemach Linux, macOS i [nowoczesnych wersje systemu Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)są obsługiwane.

## <a name="client-configuration"></a>Konfiguracja klientów

`GrpcChannel` obsługuje wykonywanie wywołań gRPC przez transporty niestandardowe. Po utworzeniu kanału można go skonfigurować przy użyciu programu z `SocketsHttpHandler` niestandardowym `ConnectionFactory` . Fabryka pozwala klientowi na nawiązywanie połączeń za pośrednictwem niestandardowych transportów, a następnie wysyłanie żądań HTTP przez ten transport.

> [!IMPORTANT]
> `ConnectionFactory` jest nowym interfejsem API w programie .NET 5 Release Candidate 1.

Przykład fabryki połączeń gniazd domen systemu UNIX:

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

Tworzenie kanału przy użyciu fabryki niestandardowych połączeń:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Kanały utworzone za pomocą powyższego kodu wysyłają wywołania gRPC za pośrednictwem gniazd domen systemu UNIX.
