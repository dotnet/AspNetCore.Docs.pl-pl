---
title: Integracja fabryki klienta gRPC w .NET Core
author: jamesnk
description: Dowiedz się, jak tworzyć klientów gRPC przy użyciu fabryki klienta.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667168"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>Integracja fabryki klienta gRPC w .NET Core

Integracja gRPC z `HttpClientFactory` oferuje scentralizowany sposób tworzenia klientów gRPC. Może być stosowany jako alternatywa dla [konfigurowania autonomicznych wystąpień klienta gRPC](xref:grpc/client). Integracja fabryczna jest dostępna w pakiecie [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.

Fabryka oferuje następujące korzyści:

* Zapewnia centralną lokalizację do konfigurowania logicznych wystąpień klienta gRPC
* Zarządza okresem istnienia instrumentu bazowego`HttpClientMessageHandler`
* Automatyczne propagowanie terminu i anulowanie w ASP.NET usługi GRPC Core

## <a name="register-grpc-clients"></a>Zarejestruj klientów gRPC

Aby zarejestrować klienta gRPC, można użyć `AddGrpcClient` `Startup.ConfigureServices`ogólnej metody rozszerzenia w ramach , określając klasę klienta wpisaną gRPC i adres usługi:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Typ klienta gRPC jest zarejestrowany jako przejściowy z iniekcji zależności (DI). Klient może być teraz wstrzykiwany i zużywany bezpośrednio w typach utworzonych przez DI. ASP.NET Core MVC, koncentratory SignalR i usługi gRPC to miejsca, w których klienci gRPC mogą być automatycznie wstrzykiwani:

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

## <a name="configure-httpclient"></a>Konfigurowanie protokołu HttpClient

`HttpClientFactory`tworzy `HttpClient` używane przez klienta gRPC. Standardowe `HttpClientFactory` metody mogą być używane do dodawania oprogramowania `HttpClientHandler` pośredniczącego żądań wychodzących lub do konfigurowania `HttpClient`podstawy:

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

Aby uzyskać więcej informacji, zobacz [Tworzenie żądań HTTP przy użyciu programu IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Konfigurowanie kanałów i przechwytujących

Dostępne są metody specyficzne dla gRPC dla:

* Skonfiguruj podstawowy kanał klienta gRPC.
* Dodaj `Interceptor` wystąpienia, które klient będzie używać podczas wykonywania wywołań gRPC.

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

## <a name="deadline-and-cancellation-propagation"></a>Termin i rozmnażanie anulowania

Klienci gRPC utworzone przez fabrykę w usłudze gRPC można `EnableCallContextPropagation()` skonfigurować do automatycznego propagowania tokenu ostatecznego i anulowania do wywołań podrzędnych. Metoda `EnableCallContextPropagation()` rozszerzenia jest dostępna w pakiecie [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.

Propagacja kontekstu wywołania działa, odczytując token terminu i anulowania z bieżącego kontekstu żądania gRPC i automatycznie propagując je do połączeń wychodzących wykonanych przez klienta gRPC. Propagacja kontekstu wywołania jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Aby uzyskać więcej informacji na temat terminów i anulowania RPC, zobacz [cykl życia RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
