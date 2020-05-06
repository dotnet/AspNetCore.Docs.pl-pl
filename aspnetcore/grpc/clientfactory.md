---
title: gRPC integrację klienta w programie .NET Core
author: jamesnk
description: Dowiedz się, jak tworzyć klientów gRPC przy użyciu fabryki klienta.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 42b786b9a4d9b422ccf92d7a329979894a35b275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774720"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>gRPC integrację klienta w programie .NET Core

Integracja gRPC z `HttpClientFactory` programem oferuje scentralizowany sposób tworzenia klientów gRPC. Może służyć jako alternatywa do [konfigurowania autonomicznych wystąpień klienta gRPC](xref:grpc/client). Integracja z fabryką jest dostępna w pakiecie NuGet [GRPC .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

Fabryka oferuje następujące korzyści:

* Zapewnia centralną lokalizację do konfigurowania wystąpień klienta logicznego gRPC
* Zarządza okresem istnienia bazowego`HttpClientMessageHandler`
* Automatyczne propagowanie terminu ostatecznego i anulowanie w ASP.NET Core usłudze gRPC

## <a name="register-grpc-clients"></a>Rejestrowanie klientów gRPC

W celu zarejestrowania klienta gRPC można użyć `AddGrpcClient` metody rozszerzenia generycznego w programie `Startup.ConfigureServices`, określając klasę klienta z określonym gRPC i adres usługi:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Typ klienta gRPC jest rejestrowany jako przejściowy z iniekcją zależności (DI). Klient może teraz zostać dodany i wykorzystany bezpośrednio w typach utworzonych przez DI. ASP.NET Core kontrolery MVC, SignalR centra i usługi gRPC są umieszczane w miejscach, w których można automatycznie dodawać klientów gRPC:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Konfigurowanie HttpClient

`HttpClientFactory`tworzy `HttpClient` używany przez klienta gRPC. Przy `HttpClientFactory` użyciu metod standardowych można dodać wychodzące oprogramowanie pośredniczące lub skonfigurować podstawową `HttpClientHandler` wartość: `HttpClient`

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Aby uzyskać więcej informacji, zobacz [Tworzenie żądań HTTP przy użyciu IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Konfigurowanie kanałów i przechwyceń

metody specyficzne dla gRPC są dostępne dla:

* Skonfiguruj kanał bazowy klienta gRPC.
* Dodaj `Interceptor` wystąpienia, które będą używane przez klienta podczas wykonywania wywołań gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Termin ostateczny i Propagacja anulowania

klientów gRPC utworzonych przez fabrykę w usłudze gRPC można skonfigurować w `EnableCallContextPropagation()` celu automatycznego propagowania terminu ostatecznego i tokenu anulowania do wywołań podrzędnych. Metoda `EnableCallContextPropagation()` rozszerzenia jest dostępna w pakiecie NuGet [GRPC. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

Propagacja kontekstu wywołania działa, odczytując termin ostateczny i token anulowania z bieżącego kontekstu żądania gRPC i automatycznie propaguje je do wywołań wychodzących wykonywanych przez klienta gRPC. Propagacja kontekstu wywołania jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Aby uzyskać więcej informacji na temat terminów i anulowania wywołania RPC, zobacz [cykl życia usługi RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
