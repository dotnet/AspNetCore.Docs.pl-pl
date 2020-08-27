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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="f6ca7-103">Komunikacja między procesami z gRPC</span><span class="sxs-lookup"><span data-stu-id="f6ca7-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="f6ca7-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f6ca7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f6ca7-105">wywołania gRPC między klientem a usługą są zwykle wysyłane za pośrednictwem gniazd TCP.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="f6ca7-106">Protokół TCP doskonale nadaje się do komunikacji w sieci, ale [komunikacja między procesami (IPC)](https://wikipedia.org/wiki/Inter-process_communication) jest bardziej wydajna, gdy klient i usługa znajdują się na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="f6ca7-107">W tym dokumencie wyjaśniono, jak używać gRPC z niestandardowymi transportami w scenariuszach IPC.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="f6ca7-108">Konfiguracja serwera</span><span class="sxs-lookup"><span data-stu-id="f6ca7-108">Server configuration</span></span>

<span data-ttu-id="f6ca7-109">Niestandardowe transporty są obsługiwane przez Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="f6ca7-110">Kestrel konfiguruje się w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f6ca7-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="f6ca7-111">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="f6ca7-111">The preceding example:</span></span>

* <span data-ttu-id="f6ca7-112">Konfiguruje punkty końcowe Kestrel w `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="f6ca7-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="f6ca7-113">Wywołania <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nasłuchiwania w [gnieździe domeny systemu UNIX](https://en.wikipedia.org/wiki/Unix_domain_socket) z określoną ścieżką.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="f6ca7-114">Kestrel ma wbudowaną obsługę punktów końcowych programu do tworzenia.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="f6ca7-115">W systemach Linux, macOS i [nowoczesnych wersje systemu Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="f6ca7-116">Konfiguracja klientów</span><span class="sxs-lookup"><span data-stu-id="f6ca7-116">Client configuration</span></span>

<span data-ttu-id="f6ca7-117">`GrpcChannel` obsługuje wykonywanie wywołań gRPC przez transporty niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="f6ca7-118">Po utworzeniu kanału można go skonfigurować przy użyciu programu z `SocketsHttpHandler` niestandardowym `ConnectionFactory` .</span><span class="sxs-lookup"><span data-stu-id="f6ca7-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="f6ca7-119">Fabryka pozwala klientowi na nawiązywanie połączeń za pośrednictwem niestandardowych transportów, a następnie wysyłanie żądań HTTP przez ten transport.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f6ca7-120">`ConnectionFactory` jest nowym interfejsem API w programie .NET 5 Release Candidate 1.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="f6ca7-121">Przykład fabryki połączeń gniazd domen systemu UNIX:</span><span class="sxs-lookup"><span data-stu-id="f6ca7-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="f6ca7-122">Tworzenie kanału przy użyciu fabryki niestandardowych połączeń:</span><span class="sxs-lookup"><span data-stu-id="f6ca7-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="f6ca7-123">Kanały utworzone za pomocą powyższego kodu wysyłają wywołania gRPC za pośrednictwem gniazd domen systemu UNIX.</span><span class="sxs-lookup"><span data-stu-id="f6ca7-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
