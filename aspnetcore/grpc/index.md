---
title: Wprowadzenie do gRPC na platformie .NET Core
author: juntaoluo
description: Dowiedz się więcej o usługach gRPC Services z serwerem Kestrel i stosem ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/index
ms.openlocfilehash: d21d808181d44b4c7e800df61ec1f3accdb58539
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016105"
---
# <a name="introduction-to-grpc-on-net-core"></a>Wprowadzenie do gRPC na platformie .NET Core

Przez [John Luo](https://github.com/juntaoluo) i [Kuba Kowalski-króla](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) to język niezależny od i środowisko zdalnego wywołania procedury (RPC) o wysokiej wydajności.

Główne zalety struktury gRPC są następujące:
* Nowoczesna, wysoce wydajna i uproszczona struktura RPC.
* Tworzenie interfejsu API za pomocą podejścia „najpierw kontrakt” z domyślnym użyciem buforów protokołu, co umożliwia implementacje niezależne od języka.
* Dostępne dla wielu języków narzędzia do generowania silnie typizowanych serwerów i klientów.
* Obsługa wywołania przesyłania strumieniowego klienta, serwera i dwukierunkowego.
* Zredukowane użycie sieci dzięki serializacji binarnej Protobuf.

Te korzyści sprawiają, że gRPC doskonale nadaje się do:
* Lekkie mikrousługi, w których wydajność jest krytyczna.
* Systemy Polyglot, w których wiele języków jest wymaganych do programowania.
* Usługi w czasie rzeczywistym, które muszą obsługiwać żądania przesyłania strumieniowego lub odpowiedzi.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>Obsługa narzędzi C# dla plików. proto

gRPC używa podejścia pierwszego kontraktu do programowania interfejsu API. Usługi i komunikaty są zdefiniowane w plikach * \* . proto* :

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

Typy .NET dla usług, klientów i komunikatów są generowane automatycznie przez dołączenie plików * \* proto* w projekcie:

* Dodaj odwołanie do pakietu do pakietu [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
* Dodaj pliki * \* . proto* do `<Protobuf>` grupy elementów.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Więcej informacji o obsłudze narzędzi gRPC można znaleźć w temacie <xref:grpc/basics> .

## <a name="grpc-services-on-aspnet-core"></a>usługi gRPC Services na ASP.NET Core

usługi gRPC Services mogą być hostowane na ASP.NET Core. Usługi mają pełną integrację z popularnymi funkcjami ASP.NET Core, takimi jak rejestrowanie, iniekcja zależności (DI), uwierzytelnianie i autoryzacja.

Szablon projektu usługi gRPC to usługa początkowa:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService`dziedziczy z `GreeterBase` typu, który jest generowany na podstawie `Greeter` usługi w pliku * \* . proto* . Usługa jest dostępna dla klientów w *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Aby dowiedzieć się więcej na temat usług gRPC Services na ASP.NET Core, zobacz <xref:grpc/aspnetcore> .

## <a name="call-grpc-services-with-a-net-client"></a>Wywoływanie usług gRPC za pomocą klienta platformy .NET

gRPC klienci są konkretnymi typami klientów, które są [generowane z plików * \* . proto* ](xref:grpc/basics#generated-c-assets). Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku * \* . proto* .

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Klient gRPC jest tworzony przy użyciu kanału, który reprezentuje długotrwałe połączenie z usługą gRPC. Kanał można utworzyć przy użyciu `GrpcChannel.ForAddress` .

Aby uzyskać więcej informacji na temat tworzenia klientów i wywoływania różnych metod usługi, zobacz <xref:grpc/client> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
