---
title: Tworzenie usług i metod gRPC
author: jamesnk
description: Dowiedz się, jak tworzyć usługi i metody gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
no-loc:
- appsettings.json
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058743"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="14fed-103">Tworzenie usług i metod gRPC</span><span class="sxs-lookup"><span data-stu-id="14fed-103">Create gRPC services and methods</span></span>

<span data-ttu-id="14fed-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="14fed-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="14fed-105">W tym dokumencie opisano sposób tworzenia usług gRPC i metod w języku C#.</span><span class="sxs-lookup"><span data-stu-id="14fed-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="14fed-106">Tematy obejmują:</span><span class="sxs-lookup"><span data-stu-id="14fed-106">Topics include:</span></span>

* <span data-ttu-id="14fed-107">Jak definiować usługi i metody w plikach *. proto* .</span><span class="sxs-lookup"><span data-stu-id="14fed-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="14fed-108">Wygenerowany kod przy użyciu narzędzi języka C# gRPC.</span><span class="sxs-lookup"><span data-stu-id="14fed-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="14fed-109">Implementowanie usług i metod gRPC.</span><span class="sxs-lookup"><span data-stu-id="14fed-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="14fed-110">Tworzenie nowych usług gRPC</span><span class="sxs-lookup"><span data-stu-id="14fed-110">Create new gRPC services</span></span>

<span data-ttu-id="14fed-111">[usługi gRPC Services przy użyciu języka C#](xref:grpc/basics) wprowadzają najpierw podejście do programowania interfejsu API w programie gRPC.</span><span class="sxs-lookup"><span data-stu-id="14fed-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="14fed-112">Usługi i komunikaty są zdefiniowane w plikach *. proto* .</span><span class="sxs-lookup"><span data-stu-id="14fed-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="14fed-113">Narzędzia C# następnie generują kod z plików *. proto* .</span><span class="sxs-lookup"><span data-stu-id="14fed-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="14fed-114">W przypadku zasobów po stronie serwera jest generowany abstrakcyjny typ podstawowy dla każdej usługi wraz z klasami wszystkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="14fed-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="14fed-115">Następujący plik *. proto* :</span><span class="sxs-lookup"><span data-stu-id="14fed-115">The following *.proto* file:</span></span>

* <span data-ttu-id="14fed-116">Definiuje `Greeter` usługę.</span><span class="sxs-lookup"><span data-stu-id="14fed-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="14fed-117">`Greeter`Usługa definiuje `SayHello` wywołanie.</span><span class="sxs-lookup"><span data-stu-id="14fed-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="14fed-118">`SayHello` wysyła `HelloRequest` komunikat i odbiera `HelloReply` komunikat</span><span class="sxs-lookup"><span data-stu-id="14fed-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="14fed-119">Narzędzia c# generują `GreeterBase` Typ podstawowy C#:</span><span class="sxs-lookup"><span data-stu-id="14fed-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="14fed-120">Domyślnie wygenerowany element `GreeterBase` nie wykonuje żadnych czynności.</span><span class="sxs-lookup"><span data-stu-id="14fed-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="14fed-121">Jego metoda wirtualna zwróci `SayHello` `UNIMPLEMENTED` błąd do wszystkich klientów, które go wywołują.</span><span class="sxs-lookup"><span data-stu-id="14fed-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="14fed-122">Aby usługa była użyteczna, aplikacja musi utworzyć konkretną implementację `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="14fed-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="14fed-123">Implementacja usługi jest zarejestrowana w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14fed-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="14fed-124">Jeśli usługa jest hostowana przez ASP.NET Core gRPC, należy ją dodać do potoku routingu za pomocą `MapGrpcService` metody.</span><span class="sxs-lookup"><span data-stu-id="14fed-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="14fed-125">Aby uzyskać więcej informacji, zobacz <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="14fed-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="14fed-126">Implementuj metody gRPC</span><span class="sxs-lookup"><span data-stu-id="14fed-126">Implement gRPC methods</span></span>

<span data-ttu-id="14fed-127">Usługa gRPC może mieć różne typy metod.</span><span class="sxs-lookup"><span data-stu-id="14fed-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="14fed-128">Sposób, w jaki komunikaty są wysyłane i odbierane przez usługę, zależy od typu zdefiniowanej metody.</span><span class="sxs-lookup"><span data-stu-id="14fed-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="14fed-129">Typy metod gRPC są następujące:</span><span class="sxs-lookup"><span data-stu-id="14fed-129">The gRPC method types are:</span></span>

* <span data-ttu-id="14fed-130">Jednoargumentowy</span><span class="sxs-lookup"><span data-stu-id="14fed-130">Unary</span></span>
* <span data-ttu-id="14fed-131">Przesyłanie strumieniowe serwera</span><span class="sxs-lookup"><span data-stu-id="14fed-131">Server streaming</span></span>
* <span data-ttu-id="14fed-132">Przesyłanie strumieniowe klienta</span><span class="sxs-lookup"><span data-stu-id="14fed-132">Client streaming</span></span>
* <span data-ttu-id="14fed-133">Dwukierunkowe przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="14fed-133">Bi-directional streaming</span></span>

<span data-ttu-id="14fed-134">Wywołania przesyłania strumieniowego są określane za pomocą `stream` słowa kluczowego w pliku *. proto* .</span><span class="sxs-lookup"><span data-stu-id="14fed-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="14fed-135">`stream` może być umieszczony w komunikacie żądania wywołania, komunikacie odpowiedzi lub obu.</span><span class="sxs-lookup"><span data-stu-id="14fed-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="14fed-136">Każdy typ wywołania ma inną sygnaturę metody.</span><span class="sxs-lookup"><span data-stu-id="14fed-136">Each call type has a different method signature.</span></span> <span data-ttu-id="14fed-137">Zastępowanie wygenerowanych metod z abstrakcyjnego typu usługi podstawowej w konkretnej implementacji gwarantuje, że są używane poprawne argumenty i zwracanego typu.</span><span class="sxs-lookup"><span data-stu-id="14fed-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="14fed-138">Metoda Jednoargumentowa</span><span class="sxs-lookup"><span data-stu-id="14fed-138">Unary method</span></span>

<span data-ttu-id="14fed-139">Metoda Jednoargumentowa pobiera komunikat żądania jako parametr i zwraca odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="14fed-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="14fed-140">Wywołanie jednoargumentowe zostało zakończone w momencie zwrócenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="14fed-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="14fed-141">Wywołania jednoargumentowe są najbardziej podobne do [akcji w kontrolerach interfejsu API sieci Web](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="14fed-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="14fed-142">Jedną z ważnych różnic między metodami gRPC a akcjami są metody gRPC, które nie mogą powiązać części żądania z innymi argumentami metod.</span><span class="sxs-lookup"><span data-stu-id="14fed-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="14fed-143">Metody gRPC zawsze mają jeden argument Message dla danych żądania przychodzącego.</span><span class="sxs-lookup"><span data-stu-id="14fed-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="14fed-144">Do usługi gRPC można nadal wysyłać wiele wartości, tworząc pola w wiadomości żądania:</span><span class="sxs-lookup"><span data-stu-id="14fed-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="14fed-145">Metoda przesyłania strumieniowego serwera</span><span class="sxs-lookup"><span data-stu-id="14fed-145">Server streaming method</span></span>

<span data-ttu-id="14fed-146">Metoda przesyłania strumieniowego serwera pobiera komunikat żądania jako parametr.</span><span class="sxs-lookup"><span data-stu-id="14fed-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="14fed-147">Ponieważ do obiektu wywołującego można przesyłać strumieniowo wiele komunikatów, `responseStream.WriteAsync` jest on używany do wysyłania komunikatów odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="14fed-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="14fed-148">Wywołanie przesyłania strumieniowego serwera jest kompletne, gdy metoda zwraca.</span><span class="sxs-lookup"><span data-stu-id="14fed-148">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="14fed-149">Klient nie ma możliwości wysyłania dodatkowych komunikatów lub danych po rozpoczęciu metody przesyłania strumieniowego serwera.</span><span class="sxs-lookup"><span data-stu-id="14fed-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="14fed-150">Niektóre metody przesyłania strumieniowego są przeznaczone do uruchamiania w nieskończoność.</span><span class="sxs-lookup"><span data-stu-id="14fed-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="14fed-151">W przypadku metod ciągłego przesyłania strumieniowego klient może anulować wywołanie, gdy nie jest już potrzebne.</span><span class="sxs-lookup"><span data-stu-id="14fed-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="14fed-152">Gdy to nastąpi, klient wysyła sygnał do serwera i zostanie zgłoszony [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) .</span><span class="sxs-lookup"><span data-stu-id="14fed-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="14fed-153">`CancellationToken`Token powinien być używany na serwerze z metodami asynchronicznymi, aby:</span><span class="sxs-lookup"><span data-stu-id="14fed-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="14fed-154">Wszystkie operacje asynchroniczne są anulowane razem z wywołaniem przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="14fed-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="14fed-155">Metoda szybko kończy pracę.</span><span class="sxs-lookup"><span data-stu-id="14fed-155">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="14fed-156">Metoda przesyłania strumieniowego klienta</span><span class="sxs-lookup"><span data-stu-id="14fed-156">Client streaming method</span></span>

<span data-ttu-id="14fed-157">Metoda przesyłania strumieniowego klienta jest uruchamiana *bez* metody otrzymywania komunikatu.</span><span class="sxs-lookup"><span data-stu-id="14fed-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="14fed-158">Ten `requestStream` parametr służy do odczytywania komunikatów z klienta.</span><span class="sxs-lookup"><span data-stu-id="14fed-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="14fed-159">Wywołanie przesyłania strumieniowego klienta zostało zakończone, gdy zostanie zwrócony komunikat odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="14fed-159">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="14fed-160">W przypadku korzystania z języka C# 8 lub nowszego `await foreach` składnia może być używana do odczytywania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="14fed-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="14fed-161">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozszerzająca odczytuje wszystkie komunikaty ze strumienia żądań:</span><span class="sxs-lookup"><span data-stu-id="14fed-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="14fed-162">Dwukierunkowa Metoda przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="14fed-162">Bi-directional streaming method</span></span>

<span data-ttu-id="14fed-163">Dwukierunkowa Metoda przesyłania strumieniowego jest uruchamiana *bez* metody otrzymywania komunikatu.</span><span class="sxs-lookup"><span data-stu-id="14fed-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="14fed-164">Ten `requestStream` parametr służy do odczytywania komunikatów z klienta.</span><span class="sxs-lookup"><span data-stu-id="14fed-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="14fed-165">Metoda może zdecydować się na wysyłanie komunikatów `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="14fed-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="14fed-166">Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy metoda zwraca:</span><span class="sxs-lookup"><span data-stu-id="14fed-166">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="14fed-167">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="14fed-167">The preceding code:</span></span>

* <span data-ttu-id="14fed-168">Wysyła odpowiedź dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="14fed-168">Sends a response for each request.</span></span>
* <span data-ttu-id="14fed-169">To podstawowe użycie dwukierunkowego przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="14fed-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="14fed-170">Istnieje możliwość obsługi bardziej złożonych scenariuszy, takich jak odczytywanie żądań i wysyłanie odpowiedzi jednocześnie:</span><span class="sxs-lookup"><span data-stu-id="14fed-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="14fed-171">W przypadku dwukierunkowej metody przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie.</span><span class="sxs-lookup"><span data-stu-id="14fed-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="14fed-172">Najlepsza implementacja metody dwukierunkowej różni się w zależności od wymagań.</span><span class="sxs-lookup"><span data-stu-id="14fed-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="14fed-173">Nagłówki żądania dostępu gRPC</span><span class="sxs-lookup"><span data-stu-id="14fed-173">Access gRPC request headers</span></span>

<span data-ttu-id="14fed-174">Komunikat żądania nie jest jedynym sposobem, aby klient wysyłał dane do usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="14fed-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="14fed-175">Wartości nagłówka są dostępne w usłudze przy użyciu `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="14fed-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="14fed-176">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="14fed-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
