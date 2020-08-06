---
title: SignalRHubContext
author: bradygaster
description: Dowiedz się, jak używać SignalR usługi ASP.NET Core HubContext do wysyłania powiadomień do klientów spoza centrum.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubcontext
ms.openlocfilehash: d38cb5b3e818879d3ec40a927acfdd69dc85377a
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819253"
---
# <a name="send-messages-from-outside-a-hub"></a>Wysyłanie komunikatów spoza centrum

Autor [Mikael Mengistu](https://twitter.com/MikaelM_12)

SignalRCentrum to podstawowe streszczenie służące do wysyłania komunikatów do klientów podłączonych do SignalR serwera programu. Możliwe jest również wysyłanie komunikatów z innych miejsc w aplikacji przy użyciu `IHubContext` usługi. W tym artykule wyjaśniono, jak uzyskać dostęp do programu w SignalR `IHubContext` celu wysyłania powiadomień do klientów spoza centrum.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Pobierz wystąpienie elementu IHubContext

W ASP.NET Core SignalR można uzyskać dostęp do wystąpienia `IHubContext` przez iniekcję zależności. Można wstrzyknąć wystąpienie `IHubContext` do kontrolera, oprogramowania pośredniczącego lub innej usługi. Użyj wystąpienia, aby wysyłać komunikaty do klientów.

> [!NOTE]
> Różni się to od ASP.NET 4. x SignalR , które były używane GlobalHost w celu zapewnienia dostępu do programu `IHubContext` . ASP.NET Core ma strukturę iniekcji zależności, która eliminuje potrzebę tego globalnej pojedynczej.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Wstrzyknąć wystąpienie elementu IHubContext w kontrolerze

Można wstrzyknąć wystąpienie `IHubContext` do kontrolera przez dodanie go do konstruktora:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Teraz, mając dostęp do wystąpienia `IHubContext` , można wywołać metody centrum, tak jakby znajdowały się one w centrum.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Pobierz wystąpienie elementu IHubContext w oprogramowaniu pośredniczącym

Uzyskaj dostęp do tego `IHubContext` potoku w ramach oprogramowania pośredniczącego, takiego jak:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Gdy metody piasty są wywoływane spoza `Hub` klasy, nie istnieje obiekt wywołujący skojarzony z wywołaniem. W związku z tym nie ma dostępu do `ConnectionId` `Caller` właściwości, i `Others` .

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>Pobierz wystąpienie elementu IHubContext z IHost

Dostęp do programu `IHubContext` z hosta sieci Web jest przydatny do integracji z obszarami poza ASP.NET Core, na przykład przy użyciu platform wtrysku zależności innych firm:

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Wstrzyknąć HubContext o jednoznacznie określonym typie

Aby wstrzyknąć HubContext o jednoznacznie określonym typie, upewnij się, że koncentrator dziedziczy z `Hub<T>` . Wstrzyknąć go przy użyciu `IHubContext<THub, T>` interfejsu zamiast `IHubContext<THub>` .

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie](xref:tutorials/signalr)
* [Koncentratory](xref:signalr/hubs)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
