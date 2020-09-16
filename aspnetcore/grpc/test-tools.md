---
title: Usługi testowe z narzędziami gRPC
author: jamesnk
description: Dowiedz się, jak testować usługi za pomocą narzędzi gRPC. gRPCurl narzędzie wiersza polecenia do współpracy z usługami gRPC Services. gRPCui to interaktywny interfejs użytkownika sieci Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594432"
---
# <a name="test-services-with-grpc-tools"></a>Usługi testowe z narzędziami gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Narzędzia są dostępne dla gRPC, dzięki czemu deweloperzy mogą testować usługi bez kompilowania aplikacji klienckich. [gRPCurl](https://github.com/fullstorydev/grpcurl) to narzędzie wiersza polecenia, które umożliwia interakcję z usługami gRPC Services. [gRPCui](https://github.com/fullstorydev/grpcui) dodaje interaktywny interfejs użytkownika sieci Web dla gRPC.

W tym artykule omówiono sposób wykonywania tych zagadnień:

* Pobierz i zainstaluj gRPCurl oraz gRPCui.
* Skonfiguruj odbicie gRPC za pomocą aplikacji gRPC ASP.NET Core.
* Odkryj i przetestuj usługi gRPC Services za pomocą programu `grpcurl` .
* Korzystanie z usług gRPC Services za pomocą przeglądarki `grpcui` .

## <a name="about-grpcurl"></a>Informacje o gRPCurl

gRPCurl to narzędzie wiersza polecenia utworzone przez społeczność gRPC. Dostępne są następujące funkcje:

* Wywoływanie usług gRPC, w tym usług przesyłania strumieniowego.
* Odnajdowanie usługi przy użyciu [odbicia gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Lista i opisywanie usług gRPC Services.
* Współpracuje z serwerami Secure (TLS) i Unsecure (zwykłym tekstem).

Aby uzyskać informacje na temat pobierania i instalowania `grpcurl` , zobacz [stronę główną usługi GitHub gRPCurl](https://github.com/fullstorydev/grpcurl#installation).

## <a name="setup-grpc-reflection"></a>Ustawienia odbicia gRPC

`grpcurl` musi znać protobuf kontrakt usług, zanim będzie mógł je wywoływać. Istnieją dwa sposoby wykonania tej czynności:

* Użyj odbicia gRPC, aby odnaleźć kontrakty usługi.
* Określ pliki *. proto* w argumentach wiersza polecenia.

Łatwiej jest używać gRPCurl z odbiciem gRPC i odnajdywaniem usług. gRPC ASP.NET Core ma wbudowaną obsługę odbicia gRPC z pakietem [gRPC. AspNetCore. Server. odbicie](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) . Aby skonfigurować odbicie w aplikacji:

* Dodaj `Grpc.AspNetCore.Server.Reflection` odwołanie do pakietu.
* Rejestruj odbicie w *Startup.cs*:
  * `AddGrpcReflection` Aby zarejestrować usługi, które umożliwiają odbicie.
  * `MapGrpcReflectionService` Aby dodać punkt końcowy usługi odbicia.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>Korzystanie z polecenia `grpcurl`

`-help`Argument opisuje `grpcurl` Opcje wiersza polecenia:

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Odkryj usługi

Użyj `describe` zlecenia, aby wyświetlić usługi zdefiniowane przez serwer:

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

Powyższy przykład:

* Uruchamia `describe` zlecenie na serwerze `localhost:5001` .
* Drukuje usługi i metody zwracane przez odbicie gRPC.
  * `Greeter` jest usługą zaimplementowaną przez aplikację.
  * `ServerReflection` jest usługą dodaną przez `Grpc.AspNetCore.Server.Reflection` pakiet.

Połącz `describe` z usługą, metodą lub nazwą komunikatu, aby wyświetlić jego szczegóły:

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Wywoływanie usług gRPC Services

Wywołaj usługę gRPC, określając nazwę usługi i metody wraz z argumentem JSON, który reprezentuje komunikat żądania. KOD JSON jest konwertowany na protobuf i wysyłany do usługi.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Powyższy przykład:

* `-d` argument określa komunikat żądania o formacie JSON. Ten argument musi występować przed adresem serwera i nazwą metody.
* Wywołuje `SayHello` metodę w `greeter.Greeter` usłudze.
* Drukuje komunikat odpowiedzi w formacie JSON.

## <a name="about-grpcui"></a>Informacje o gRPCui

gRPCui to interaktywny interfejs użytkownika sieci Web dla gRPC. Jest ona oparta na gRPCurl i oferuje graficzny interfejs użytkownika do odnajdywania i testowania usług gRPC, podobnie jak w przypadku narzędzi HTTP, takich jak Poster.

Aby uzyskać informacje na temat pobierania i instalowania `grpcui` , zobacz [stronę główną usługi GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Korzystanie z akcji `grpcui`

Uruchom `grpcui` z adresem serwera, aby współdziałać z programem jako argumentem.

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Narzędzie uruchomi okno przeglądarki z interaktywnym interfejsem użytkownika sieci Web. usługi gRPC są automatycznie wykrywane przy użyciu odbicia gRPC.

![Interfejs użytkownika sieci Web gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Strona główna usługi GitHub gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Strona główna usługi GitHub gRPCui](https://github.com/fullstorydev/grpcui)
* [GRPC. AspNetCore. Server. odbicie](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
