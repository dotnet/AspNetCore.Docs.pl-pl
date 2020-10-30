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
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058743"
---
# <a name="create-grpc-services-and-methods"></a>Tworzenie usług i metod gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

W tym dokumencie opisano sposób tworzenia usług gRPC i metod w języku C#. Tematy obejmują:

* Jak definiować usługi i metody w plikach *. proto* .
* Wygenerowany kod przy użyciu narzędzi języka C# gRPC.
* Implementowanie usług i metod gRPC.

## <a name="create-new-grpc-services"></a>Tworzenie nowych usług gRPC

[usługi gRPC Services przy użyciu języka C#](xref:grpc/basics) wprowadzają najpierw podejście do programowania interfejsu API w programie gRPC. Usługi i komunikaty są zdefiniowane w plikach *. proto* . Narzędzia C# następnie generują kod z plików *. proto* . W przypadku zasobów po stronie serwera jest generowany abstrakcyjny typ podstawowy dla każdej usługi wraz z klasami wszystkich komunikatów.

Następujący plik *. proto* :

* Definiuje `Greeter` usługę.
* `Greeter`Usługa definiuje `SayHello` wywołanie.
* `SayHello` wysyła `HelloRequest` komunikat i odbiera `HelloReply` komunikat

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

Narzędzia c# generują `GreeterBase` Typ podstawowy C#:

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

Domyślnie wygenerowany element `GreeterBase` nie wykonuje żadnych czynności. Jego metoda wirtualna zwróci `SayHello` `UNIMPLEMENTED` błąd do wszystkich klientów, które go wywołują. Aby usługa była użyteczna, aplikacja musi utworzyć konkretną implementację `GreeterBase` :

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

Implementacja usługi jest zarejestrowana w aplikacji. Jeśli usługa jest hostowana przez ASP.NET Core gRPC, należy ją dodać do potoku routingu za pomocą `MapGrpcService` metody.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Aby uzyskać więcej informacji, zobacz <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>Implementuj metody gRPC

Usługa gRPC może mieć różne typy metod. Sposób, w jaki komunikaty są wysyłane i odbierane przez usługę, zależy od typu zdefiniowanej metody. Typy metod gRPC są następujące:

* Jednoargumentowy
* Przesyłanie strumieniowe serwera
* Przesyłanie strumieniowe klienta
* Dwukierunkowe przesyłanie strumieniowe

Wywołania przesyłania strumieniowego są określane za pomocą `stream` słowa kluczowego w pliku *. proto* . `stream` może być umieszczony w komunikacie żądania wywołania, komunikacie odpowiedzi lub obu.

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

Każdy typ wywołania ma inną sygnaturę metody. Zastępowanie wygenerowanych metod z abstrakcyjnego typu usługi podstawowej w konkretnej implementacji gwarantuje, że są używane poprawne argumenty i zwracanego typu.

### <a name="unary-method"></a>Metoda Jednoargumentowa

Metoda Jednoargumentowa pobiera komunikat żądania jako parametr i zwraca odpowiedź. Wywołanie jednoargumentowe zostało zakończone w momencie zwrócenia odpowiedzi.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Wywołania jednoargumentowe są najbardziej podobne do [akcji w kontrolerach interfejsu API sieci Web](xref:web-api/index). Jedną z ważnych różnic między metodami gRPC a akcjami są metody gRPC, które nie mogą powiązać części żądania z innymi argumentami metod. Metody gRPC zawsze mają jeden argument Message dla danych żądania przychodzącego. Do usługi gRPC można nadal wysyłać wiele wartości, tworząc pola w wiadomości żądania:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Metoda przesyłania strumieniowego serwera

Metoda przesyłania strumieniowego serwera pobiera komunikat żądania jako parametr. Ponieważ do obiektu wywołującego można przesyłać strumieniowo wiele komunikatów, `responseStream.WriteAsync` jest on używany do wysyłania komunikatów odpowiedzi. Wywołanie przesyłania strumieniowego serwera jest kompletne, gdy metoda zwraca.

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

Klient nie ma możliwości wysyłania dodatkowych komunikatów lub danych po rozpoczęciu metody przesyłania strumieniowego serwera. Niektóre metody przesyłania strumieniowego są przeznaczone do uruchamiania w nieskończoność. W przypadku metod ciągłego przesyłania strumieniowego klient może anulować wywołanie, gdy nie jest już potrzebne. Gdy to nastąpi, klient wysyła sygnał do serwera i zostanie zgłoszony [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) . `CancellationToken`Token powinien być używany na serwerze z metodami asynchronicznymi, aby:

* Wszystkie operacje asynchroniczne są anulowane razem z wywołaniem przesyłania strumieniowego.
* Metoda szybko kończy pracę.

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

### <a name="client-streaming-method"></a>Metoda przesyłania strumieniowego klienta

Metoda przesyłania strumieniowego klienta jest uruchamiana *bez* metody otrzymywania komunikatu. Ten `requestStream` parametr służy do odczytywania komunikatów z klienta. Wywołanie przesyłania strumieniowego klienta zostało zakończone, gdy zostanie zwrócony komunikat odpowiedzi:

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

W przypadku korzystania z języka C# 8 lub nowszego `await foreach` składnia może być używana do odczytywania wiadomości. `IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozszerzająca odczytuje wszystkie komunikaty ze strumienia żądań:

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

### <a name="bi-directional-streaming-method"></a>Dwukierunkowa Metoda przesyłania strumieniowego

Dwukierunkowa Metoda przesyłania strumieniowego jest uruchamiana *bez* metody otrzymywania komunikatu. Ten `requestStream` parametr służy do odczytywania komunikatów z klienta. Metoda może zdecydować się na wysyłanie komunikatów `responseStream.WriteAsync` . Dwukierunkowe wywołanie przesyłania strumieniowego jest kompletne, gdy metoda zwraca:

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

Powyższy kod ma następujące działanie:

* Wysyła odpowiedź dla każdego żądania.
* To podstawowe użycie dwukierunkowego przesyłania strumieniowego.

Istnieje możliwość obsługi bardziej złożonych scenariuszy, takich jak odczytywanie żądań i wysyłanie odpowiedzi jednocześnie:

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

W przypadku dwukierunkowej metody przesyłania strumieniowego klient i usługa mogą w dowolnym momencie wysyłać komunikaty do siebie. Najlepsza implementacja metody dwukierunkowej różni się w zależności od wymagań.

## <a name="access-grpc-request-headers"></a>Nagłówki żądania dostępu gRPC

Komunikat żądania nie jest jedynym sposobem, aby klient wysyłał dane do usługi gRPC. Wartości nagłówka są dostępne w usłudze przy użyciu `ServerCallContext.RequestHeaders` .

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/basics>
* <xref:grpc/client>
