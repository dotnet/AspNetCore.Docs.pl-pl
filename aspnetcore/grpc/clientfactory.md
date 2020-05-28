---
<span data-ttu-id="73c13-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="73c13-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="73c13-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="73c13-102">'Blazor'</span></span>
- <span data-ttu-id="73c13-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="73c13-103">'Identity'</span></span>
- <span data-ttu-id="73c13-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="73c13-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="73c13-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="73c13-105">'Razor'</span></span>
- <span data-ttu-id="73c13-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="73c13-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="73c13-107">gRPC integrację klienta w programie .NET Core</span><span class="sxs-lookup"><span data-stu-id="73c13-107">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="73c13-108">Integracja gRPC z programem `HttpClientFactory` oferuje scentralizowany sposób tworzenia klientów gRPC.</span><span class="sxs-lookup"><span data-stu-id="73c13-108">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="73c13-109">Może służyć jako alternatywa do [konfigurowania autonomicznych wystąpień klienta gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="73c13-109">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="73c13-110">Integracja z fabryką jest dostępna w pakiecie NuGet [GRPC .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="73c13-110">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="73c13-111">Fabryka oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="73c13-111">The factory offers the following benefits:</span></span>

* <span data-ttu-id="73c13-112">Zapewnia centralną lokalizację do konfigurowania wystąpień klienta logicznego gRPC</span><span class="sxs-lookup"><span data-stu-id="73c13-112">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="73c13-113">Zarządza okresem istnienia bazowego`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="73c13-113">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="73c13-114">Automatyczne propagowanie terminu ostatecznego i anulowanie w ASP.NET Core usłudze gRPC</span><span class="sxs-lookup"><span data-stu-id="73c13-114">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="73c13-115">Rejestrowanie klientów gRPC</span><span class="sxs-lookup"><span data-stu-id="73c13-115">Register gRPC clients</span></span>

<span data-ttu-id="73c13-116">W celu zarejestrowania klienta gRPC `AddGrpcClient` można użyć metody rozszerzenia generycznego w programie `Startup.ConfigureServices` , określając klasę klienta z określonym gRPC i adres usługi:</span><span class="sxs-lookup"><span data-stu-id="73c13-116">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="73c13-117">Typ klienta gRPC jest rejestrowany jako przejściowy z iniekcją zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="73c13-117">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="73c13-118">Klient może teraz zostać dodany i wykorzystany bezpośrednio w typach utworzonych przez DI.</span><span class="sxs-lookup"><span data-stu-id="73c13-118">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="73c13-119">ASP.NET Core kontrolery MVC, SignalR centra i usługi gRPC są umieszczane w miejscach, w których można automatycznie dodawać klientów gRPC:</span><span class="sxs-lookup"><span data-stu-id="73c13-119">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="73c13-120">Konfigurowanie HttpClient</span><span class="sxs-lookup"><span data-stu-id="73c13-120">Configure HttpClient</span></span>

<span data-ttu-id="73c13-121">`HttpClientFactory`tworzy `HttpClient` używany przez klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="73c13-121">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="73c13-122">`HttpClientFactory`Przy użyciu metod standardowych można dodać wychodzące oprogramowanie pośredniczące lub skonfigurować podstawową `HttpClientHandler` wartość `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="73c13-122">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="73c13-123">Aby uzyskać więcej informacji, zobacz [Tworzenie żądań HTTP przy użyciu IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="73c13-123">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="73c13-124">Konfigurowanie kanałów i przechwyceń</span><span class="sxs-lookup"><span data-stu-id="73c13-124">Configure Channel and Interceptors</span></span>

<span data-ttu-id="73c13-125">metody specyficzne dla gRPC są dostępne dla:</span><span class="sxs-lookup"><span data-stu-id="73c13-125">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="73c13-126">Skonfiguruj kanał bazowy klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="73c13-126">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="73c13-127">Dodaj `Interceptor` wystąpienia, które będą używane przez klienta podczas wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="73c13-127">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="73c13-128">Termin ostateczny i Propagacja anulowania</span><span class="sxs-lookup"><span data-stu-id="73c13-128">Deadline and cancellation propagation</span></span>

<span data-ttu-id="73c13-129">klientów gRPC utworzonych przez fabrykę w usłudze gRPC można skonfigurować w `EnableCallContextPropagation()` celu automatycznego propagowania terminu ostatecznego i tokenu anulowania do wywołań podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="73c13-129">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="73c13-130">`EnableCallContextPropagation()`Metoda rozszerzenia jest dostępna w pakiecie NuGet [GRPC. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="73c13-130">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="73c13-131">Propagacja kontekstu wywołania działa, odczytując termin ostateczny i token anulowania z bieżącego kontekstu żądania gRPC i automatycznie propaguje je do wywołań wychodzących wykonywanych przez klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="73c13-131">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="73c13-132">Propagacja kontekstu wywołania jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.</span><span class="sxs-lookup"><span data-stu-id="73c13-132">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="73c13-133">Domyślnie program `EnableCallContextPropagation` zgłasza błąd, jeśli klient jest używany poza kontekstem wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="73c13-133">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="73c13-134">Ten błąd jest przeznaczony do powiadamiania o braku kontekstu wywołania do propagacji.</span><span class="sxs-lookup"><span data-stu-id="73c13-134">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="73c13-135">Jeśli klient ma być używany poza kontekstem wywołania, należy pominąć błąd, gdy klient jest skonfigurowany przy użyciu `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="73c13-135">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="73c13-136">Aby uzyskać więcej informacji na temat terminów i anulowania wywołania RPC, zobacz [cykl życia usługi RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="73c13-136">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73c13-137">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="73c13-137">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
