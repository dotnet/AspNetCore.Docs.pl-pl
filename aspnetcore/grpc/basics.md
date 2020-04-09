---
title: Usługi gRPC w środowisku C#
author: juntaoluo
description: Poznaj podstawowe pojęcia podczas pisania usług gRPC w języku C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664207"
---
# <a name="grpc-services-with-c"></a>Usługi gRPC z C\#

W tym dokumencie przedstawiono pojęcia potrzebne do pisania aplikacji [gRPC](https://grpc.io/docs/guides/) w języku C#. Tematy omówione w tym miejscu dotyczą zarówno opartych na [rdzeniu C,](https://grpc.io/blog/grpc-stacks)jak i ASP.NET aplikacji gRPC opartych na rdzeniu.

## <a name="proto-file"></a>plik proto

gRPC stosuje podejście oparte na umowie do tworzenia api. Bufory protokołów (protobuf) są domyślnie używane jako język projektowania interfejsu (IDL). Plik * \*.proto* zawiera:

* Definicja usługi gRPC.
* Wiadomości wysyłane między klientami i serwerami.

Aby uzyskać więcej informacji na temat składni plików protobuf, zobacz [oficjalną dokumentację (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Rozważmy na przykład plik *greet.proto* używany w [usłudze Wprowadzenie do usługi gRPC:](xref:tutorials/grpc/grpc-start)

* Definiuje `Greeter` usługę.
* Usługa `Greeter` definiuje wywołanie. `SayHello`
* `SayHello`wysyła `HelloRequest` wiadomość i `HelloReply` odbiera wiadomość:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Dodawanie pliku proto do\# aplikacji C

Plik * \*.proto* jest uwzględniony w projekcie, `<Protobuf>` dodając go do grupy elementów:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Obsługa narzędzi języka C# dla plików .proto

Pakiet narzędzi [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) jest wymagany do generowania zasobów języka C# z * \*plików .proto.* Wygenerowane zasoby (pliki):

* Są generowane zgodnie z potrzebami za każdym razem, gdy projekt jest budowany.
* Nie są dodawane do projektu lub zaewidencjonowane w kontroli źródła.
* Są artefakt kompilacji zawarte w katalogu *obj.*

Ten pakiet jest wymagany zarówno przez serwer, jak i projekty klientów. Metapakiet `Grpc.AspNetCore` zawiera odniesienie `Grpc.Tools`do . Projekty serwera `Grpc.AspNetCore` można dodawać za pomocą Menedżera `<PackageReference>` pakietów w programie Visual Studio lub przez dodanie do pliku projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Projekty klienckie `Grpc.Tools` powinny bezpośrednio odwoływać się wraz z innymi pakietami wymaganymi do korzystania z klienta gRPC. Pakiet narzędzi nie jest wymagany w czasie wykonywania, więc zależność jest `PrivateAssets="All"`oznaczona:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Wygenerowane zasoby języka C#

Pakiet narzędzi generuje typy języka C# reprezentujące wiadomości * \** zdefiniowane w dołączonych plikach .proto.

W przypadku zasobów po stronie serwera generowany jest abstrakcyjny typ podstawowy usługi. Typ podstawowy zawiera definicje wszystkich wywołań gRPC zawartych w pliku *.proto.* Utwórz implementację usługi konkretnej, która wywodzi się z tego typu podstawowego i implementuje logikę wywołań gRPC. Dla `greet.proto`, przykład opisany wcześniej, typ `GreeterBase` abstrakcyjny, `SayHello` który zawiera metodę wirtualną jest generowany. Konkretna `GreeterService` implementacja zastępuje metodę i implementuje logikę obsługi wywołania gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

W przypadku zasobów po stronie klienta generowany jest typ klienta betonowego. Wywołania gRPC w pliku *.proto* są tłumaczone na metody na typie betonu, który można wywołać. Dla `greet.proto`, przykład opisany wcześniej, generowany `GreeterClient` jest typ betonu. Wywołanie `GreeterClient.SayHelloAsync` zainicjowania wywołania gRPC do serwera.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Domyślnie zasoby serwera i klienta są generowane dla każdego `<Protobuf>` * \** pliku proto zawartego w grupie elementów. Aby upewnić się, że tylko zasoby serwera `GrpcServices` są generowane `Server`w projekcie serwera, atrybut jest ustawiony na .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Podobnie atrybut jest ustawiony w `Client` projektach klientów.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
