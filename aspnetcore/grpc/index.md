---
title: Wprowadzenie do gRPC w .NET Core
author: juntaoluo
description: Dowiedz się więcej o usługach gRPC z serwerem Kestrel i ASP.NET stosem Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667315"
---
# <a name="introduction-to-grpc-on-net-core"></a>Wprowadzenie do gRPC w .NET Core

Autorstwa [Johna Luo](https://github.com/juntaoluo) i [Jamesa Newtona-Kinga](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) jest niezależną od języka, wysokiej wydajności zdalnego wywołania procedury (RPC) framework.

Główne zalety gRPC to:
* Nowoczesna, wysokowydajna, lekka rama RPC.
* Programowanie interfejsu API po kroku, domyślnie przy użyciu buforów protokołu, co pozwala na implementacje niezależnego języka.
* Narzędzia dostępne dla wielu języków do generowania silnie typizowanych serwerów i klientów.
* Obsługuje połączenia klienckie, serwerowe i dwukierunkowe.
* Mniejsze zużycie sieci dzięki serializacji binarnej Protobuf.

Korzyści te sprawiają, że gRPC idealnie nadaje się do:
* Lekkie mikrousługi, w których wydajność ma kluczowe znaczenie.
* Systemy poliglota, w których do rozwoju wymagane jest wiele języków.
* Usługi w czasie rzeczywistym typu "punkt-punkt", które muszą obsługiwać żądania przesyłania strumieniowego lub odpowiedzi.

## <a name="c-tooling-support-for-proto-files"></a>Obsługa narzędzi języka C# dla plików .proto

gRPC stosuje podejście oparte na umowie do tworzenia api. Usługi i wiadomości * \** są definiowane w plikach .proto:

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

Typy .NET dla usług, klientów i * \** wiadomości są generowane automatycznie przez włączenie plików .proto do projektu:

* Dodaj odwołanie do pakietu [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
* Dodaj * \** pliki .proto `<Protobuf>` do grupy elementów.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Aby uzyskać więcej informacji na temat <xref:grpc/basics>obsługi narzędzi gRPC, zobacz .

## <a name="grpc-services-on-aspnet-core"></a>Usługi gRPC na ASP.NET Core

Usługi gRPC mogą być hostowane w ASP.NET Core. Usługi mają pełną integrację z popularnych funkcji ASP.NET Core, takich jak rejestrowanie, iniekcja zależności (DI), uwierzytelnianie i autoryzacja.

Szablon projektu usługi gRPC zapewnia usługę początkową:

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

`GreeterService`dziedziczy od `GreeterBase` typu, który jest `Greeter` generowany z usługi w pliku * \*.proto.* Usługa jest dostępna dla klientów w *Startup.cs:*

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Aby dowiedzieć się więcej o usługach gRPC <xref:grpc/aspnetcore>na ASP.NET Core, zobacz .

## <a name="call-grpc-services-with-a-net-client"></a>Dzwoń z usług gRPC z klientem .NET

Klienci gRPC to konkretne typy klientów [generowane z * \*plików .proto.* ](xref:grpc/basics#generated-c-assets) Konkretny klient gRPC ma metody, które przekładają się na usługę gRPC w pliku * \*.proto.*

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Klient gRPC jest tworzony przy użyciu kanału, który reprezentuje długotrwałe połączenie z usługą gRPC. Kanał można utworzyć `GrpcChannel.ForAddress`za pomocą programu .

Aby uzyskać więcej informacji na temat tworzenia klientów <xref:grpc/client>i wywoływania różnych metod usług, zobacz .

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
