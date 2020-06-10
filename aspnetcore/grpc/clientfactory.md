---
title: gRPC integrację klienta w programie .NET Core
author: jamesnk
description: Dowiedz się, jak tworzyć klientów gRPC przy użyciu fabryki klienta.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: ecf2e4ce4375f9e5292f317a15f3b8016ac7daec
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106224"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>gRPC integrację klienta w programie .NET Core

Integracja gRPC z programem `HttpClientFactory` oferuje scentralizowany sposób tworzenia klientów gRPC. Może służyć jako alternatywa do [konfigurowania autonomicznych wystąpień klienta gRPC](xref:grpc/client). Integracja z fabryką jest dostępna w pakiecie NuGet [GRPC .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

Fabryka oferuje następujące korzyści:

* Zapewnia centralną lokalizację do konfigurowania wystąpień klienta logicznego gRPC
* Zarządza okresem istnienia bazowego`HttpClientMessageHandler`
* Automatyczne propagowanie terminu ostatecznego i anulowanie w ASP.NET Core usłudze gRPC

## <a name="register-grpc-clients"></a>Rejestrowanie klientów gRPC

W celu zarejestrowania klienta gRPC `AddGrpcClient` można użyć metody rozszerzenia generycznego w programie `Startup.ConfigureServices` , określając klasę klienta z określonym gRPC i adres usługi:

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

`HttpClientFactory`tworzy `HttpClient` używany przez klienta gRPC. `HttpClientFactory`Przy użyciu metod standardowych można dodać wychodzące oprogramowanie pośredniczące lub skonfigurować podstawową `HttpClientHandler` wartość `HttpClient` :

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

klientów gRPC utworzonych przez fabrykę w usłudze gRPC można skonfigurować w `EnableCallContextPropagation()` celu automatycznego propagowania terminu ostatecznego i tokenu anulowania do wywołań podrzędnych. `EnableCallContextPropagation()`Metoda rozszerzenia jest dostępna w pakiecie NuGet [GRPC. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

Propagacja kontekstu wywołania działa, odczytując termin ostateczny i token anulowania z bieżącego kontekstu żądania gRPC i automatycznie propaguje je do wywołań wychodzących wykonywanych przez klienta gRPC. Propagacja kontekstu wywołania jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Domyślnie program `EnableCallContextPropagation` zgłasza błąd, jeśli klient jest używany poza kontekstem wywołania gRPC. Ten błąd jest przeznaczony do powiadamiania o braku kontekstu wywołania do propagacji. Jeśli klient ma być używany poza kontekstem wywołania, należy pominąć błąd, gdy klient jest skonfigurowany przy użyciu `SuppressContextNotFoundErrors` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Aby uzyskać więcej informacji na temat terminów i anulowania wywołania RPC, zobacz [cykl życia usługi RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
