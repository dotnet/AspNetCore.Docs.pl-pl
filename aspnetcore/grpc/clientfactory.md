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
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="28608-103">Integracja fabryki klienta gRPC w .NET Core</span><span class="sxs-lookup"><span data-stu-id="28608-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="28608-104">Integracja gRPC z `HttpClientFactory` oferuje scentralizowany sposób tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="28608-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="28608-105">Może być stosowany jako alternatywa dla [konfigurowania autonomicznych wystąpień klienta gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="28608-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="28608-106">Integracja fabryczna jest dostępna w pakiecie [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="28608-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="28608-107">Fabryka oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="28608-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="28608-108">Zapewnia centralną lokalizację do konfigurowania logicznych wystąpień klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="28608-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="28608-109">Zarządza okresem istnienia instrumentu bazowego`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="28608-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="28608-110">Automatyczne propagowanie terminu i anulowanie w ASP.NET usługi GRPC Core</span><span class="sxs-lookup"><span data-stu-id="28608-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="28608-111">Zarejestruj klientów gRPC</span><span class="sxs-lookup"><span data-stu-id="28608-111">Register gRPC clients</span></span>

<span data-ttu-id="28608-112">Aby zarejestrować klienta gRPC, można użyć `AddGrpcClient` `Startup.ConfigureServices`ogólnej metody rozszerzenia w ramach , określając klasę klienta wpisaną gRPC i adres usługi:</span><span class="sxs-lookup"><span data-stu-id="28608-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="28608-113">Typ klienta gRPC jest zarejestrowany jako przejściowy z iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="28608-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="28608-114">Klient może być teraz wstrzykiwany i zużywany bezpośrednio w typach utworzonych przez DI.</span><span class="sxs-lookup"><span data-stu-id="28608-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="28608-115">ASP.NET Core MVC, koncentratory SignalR i usługi gRPC to miejsca, w których klienci gRPC mogą być automatycznie wstrzykiwani:</span><span class="sxs-lookup"><span data-stu-id="28608-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="28608-116">Konfigurowanie protokołu HttpClient</span><span class="sxs-lookup"><span data-stu-id="28608-116">Configure HttpClient</span></span>

<span data-ttu-id="28608-117">`HttpClientFactory`tworzy `HttpClient` używane przez klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="28608-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="28608-118">Standardowe `HttpClientFactory` metody mogą być używane do dodawania oprogramowania `HttpClientHandler` pośredniczącego żądań wychodzących lub do konfigurowania `HttpClient`podstawy:</span><span class="sxs-lookup"><span data-stu-id="28608-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="28608-119">Aby uzyskać więcej informacji, zobacz [Tworzenie żądań HTTP przy użyciu programu IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="28608-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="28608-120">Konfigurowanie kanałów i przechwytujących</span><span class="sxs-lookup"><span data-stu-id="28608-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="28608-121">Dostępne są metody specyficzne dla gRPC dla:</span><span class="sxs-lookup"><span data-stu-id="28608-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="28608-122">Skonfiguruj podstawowy kanał klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="28608-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="28608-123">Dodaj `Interceptor` wystąpienia, które klient będzie używać podczas wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="28608-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="28608-124">Termin i rozmnażanie anulowania</span><span class="sxs-lookup"><span data-stu-id="28608-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="28608-125">Klienci gRPC utworzone przez fabrykę w usłudze gRPC można `EnableCallContextPropagation()` skonfigurować do automatycznego propagowania tokenu ostatecznego i anulowania do wywołań podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="28608-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="28608-126">Metoda `EnableCallContextPropagation()` rozszerzenia jest dostępna w pakiecie [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="28608-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="28608-127">Propagacja kontekstu wywołania działa, odczytując token terminu i anulowania z bieżącego kontekstu żądania gRPC i automatycznie propagując je do połączeń wychodzących wykonanych przez klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="28608-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="28608-128">Propagacja kontekstu wywołania jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.</span><span class="sxs-lookup"><span data-stu-id="28608-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="28608-129">Aby uzyskać więcej informacji na temat terminów i anulowania RPC, zobacz [cykl życia RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="28608-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28608-130">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="28608-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
