---
title: Usługi gRPC w środowisku C#
author: juntaoluo
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi pisania usług gRPC Services przy użyciu języka C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774759"
---
# <a name="grpc-services-with-c"></a>usługi gRPC w języku C\#

W tym dokumencie przedstawiono koncepcje niezbędne do pisania aplikacji [gRPC](https://grpc.io/docs/guides/) w języku C#. Tematy omówione tutaj dotyczą zarówno aplikacji gRPC opartych na procesorze [C](https://grpc.io/blog/grpc-stacks), jak i na ASP.NET Core.

## <a name="proto-file"></a>plik PROTO

gRPC używa podejścia pierwszego kontraktu do programowania interfejsu API. Bufory protokołu (protobuf) są domyślnie używane jako język projektowania interfejsu (IDL). Plik * \*. proto* zawiera:

* Definicja usługi gRPC.
* Komunikaty wysyłane między klientami a serwerami.

Aby uzyskać więcej informacji na temat składni plików protobuf, zapoznaj się z [oficjalną dokumentacją (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Rozważmy na przykład plik *Greeting. proto* używany w temacie [Rozpoczynanie pracy z usługą gRPC](xref:tutorials/grpc/grpc-start):

* Definiuje `Greeter` usługę.
* `Greeter` Usługa definiuje `SayHello` wywołanie.
* `SayHello`wysyła `HelloRequest` komunikat i odbiera `HelloReply` komunikat:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Dodawanie pliku. proto do aplikacji C\#

Plik PROTO jest dołączany do projektu przez dodanie go do grupy `<Protobuf>` Items: * \**

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Obsługa narzędzi C# dla plików. proto

Pakiet narzędzi [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) jest wymagany do generowania zasobów C# z * \*plików. proto* . Wygenerowane zasoby (pliki):

* Są generowane w przypadku, gdy jest to wymagane, za każdym razem, gdy projekt jest skompilowany.
* Nie są dodawane do projektu ani zaewidencjonowane do kontroli źródła.
* Czy artefakt kompilacji znajduje się w katalogu *obj* .

Ten pakiet jest wymagany przez projekty serwera i klienta. `Grpc.AspNetCore` Pakietbinding zawiera odwołanie do `Grpc.Tools`. Projekty serwera mogą `<PackageReference>` być `Grpc.AspNetCore` dodawane przy użyciu Menedżera pakietów w programie Visual Studio lub poprzez dodanie do pliku projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Projekty klienta należy bezpośrednio odwoływać `Grpc.Tools` się do innych pakietów wymaganych do korzystania z klienta gRPC. Pakiet narzędzi nie jest wymagany w czasie wykonywania, dlatego zależność jest oznaczona przy użyciu `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Wygenerowane elementy zawartości C#

Pakiet narzędzi generuje typy C# reprezentujące komunikaty zdefiniowane w zawartych * \*plikach. proto* .

W przypadku zasobów po stronie serwera jest generowany abstrakcyjny typ podstawowy usługi. Typ podstawowy zawiera definicje wszystkich wywołań gRPC znajdujących się w pliku *. proto* . Utwórz konkretną implementację usługi, która pochodzi z tego typu podstawowego i implementuje logikę dla wywołań gRPC. W przypadku `greet.proto`, przykładu opisanego wcześniej, generowany jest `GreeterBase` typ abstrakcyjny, który `SayHello` zawiera metodę wirtualną. Konkretna implementacja `GreeterService` przesłania metodę i implementuje logikę obsługi wywołania gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Dla zasobów po stronie klienta jest generowany konkretny typ klienta. Wywołania gRPC w pliku *. proto* są tłumaczone na metody w konkretnym typie, który można wywołać. W przypadku `greet.proto`, przykładu opisanego wcześniej, generowany jest `GreeterClient` konkretny typ. Wywołaj `GreeterClient.SayHelloAsync` , aby zainicjować wywołanie gRPC na serwerze.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Domyślnie zasoby serwera i klienta są generowane dla każdego `<Protobuf>` * \*pliku. proto* , który znajduje się w grupie elementów. Aby upewnić się, że tylko zasoby serwera są generowane w projekcie serwera `GrpcServices` , atrybut jest ustawiany `Server`na.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Podobnie atrybut jest ustawiany na `Client` w projektach klientów.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
