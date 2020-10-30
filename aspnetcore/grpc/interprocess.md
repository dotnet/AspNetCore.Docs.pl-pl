---
title: Komunikacja między procesami przy użyciu gRPC
author: jamesnk
description: Dowiedz się, jak używać gRPC do komunikacji między procesami.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059887"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="efea1-103">Komunikacja między procesami przy użyciu gRPC</span><span class="sxs-lookup"><span data-stu-id="efea1-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="efea1-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="efea1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="efea1-105">wywołania gRPC między klientem a usługą są zwykle wysyłane za pośrednictwem gniazd TCP.</span><span class="sxs-lookup"><span data-stu-id="efea1-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="efea1-106">Protokół TCP został zaprojektowany do komunikacji przez sieć.</span><span class="sxs-lookup"><span data-stu-id="efea1-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="efea1-107">[Komunikacja między procesami (IPC)](https://wikipedia.org/wiki/Inter-process_communication) jest bardziej wydajna niż w przypadku protokołu TCP, gdy klient i usługa znajdują się na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="efea1-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="efea1-108">W tym dokumencie wyjaśniono, jak używać gRPC z niestandardowymi transportami w scenariuszach IPC.</span><span class="sxs-lookup"><span data-stu-id="efea1-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="efea1-109">Konfiguracja serwera</span><span class="sxs-lookup"><span data-stu-id="efea1-109">Server configuration</span></span>

<span data-ttu-id="efea1-110">Niestandardowe transporty są obsługiwane przez [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="efea1-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="efea1-111">Kestrel konfiguruje się w *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="efea1-111">Kestrel is configured in *Program.cs* :</span></span>

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

<span data-ttu-id="efea1-112">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="efea1-112">The preceding example:</span></span>

* <span data-ttu-id="efea1-113">Konfiguruje punkty końcowe Kestrel w `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="efea1-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="efea1-114">Wywołania <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nasłuchiwania w [gnieździe domeny systemu UNIX](https://wikipedia.org/wiki/Unix_domain_socket) z określoną ścieżką.</span><span class="sxs-lookup"><span data-stu-id="efea1-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="efea1-115">Kestrel ma wbudowaną obsługę punktów końcowych programu do tworzenia.</span><span class="sxs-lookup"><span data-stu-id="efea1-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="efea1-116">W systemach Linux, macOS i [nowoczesnych wersje systemu Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="efea1-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="efea1-117">Konfiguracja klientów</span><span class="sxs-lookup"><span data-stu-id="efea1-117">Client configuration</span></span>

<span data-ttu-id="efea1-118">`GrpcChannel` obsługuje wykonywanie wywołań gRPC przez transporty niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="efea1-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="efea1-119">Po utworzeniu kanału można go skonfigurować przy użyciu programu z `SocketsHttpHandler` niestandardowym `ConnectCallback` .</span><span class="sxs-lookup"><span data-stu-id="efea1-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="efea1-120">Wywołanie zwrotne pozwala klientowi na nawiązywanie połączeń przez transporty niestandardowe, a następnie wysyłanie żądań HTTP przez ten transport.</span><span class="sxs-lookup"><span data-stu-id="efea1-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="efea1-121">`SocketsHttpHandler.ConnectCallback` jest nowym interfejsem API w programie .NET 5 Release Candidate 2.</span><span class="sxs-lookup"><span data-stu-id="efea1-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="efea1-122">Przykład fabryki połączeń gniazd domen systemu UNIX:</span><span class="sxs-lookup"><span data-stu-id="efea1-122">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="efea1-123">Tworzenie kanału przy użyciu fabryki niestandardowych połączeń:</span><span class="sxs-lookup"><span data-stu-id="efea1-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="efea1-124">Kanały utworzone za pomocą powyższego kodu wysyłają wywołania gRPC za pośrednictwem gniazd domen systemu UNIX.</span><span class="sxs-lookup"><span data-stu-id="efea1-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="efea1-125">Obsługę innych technologii IPC można zaimplementować przy użyciu rozszerzalności w Kestrel i `SocketsHttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="efea1-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
