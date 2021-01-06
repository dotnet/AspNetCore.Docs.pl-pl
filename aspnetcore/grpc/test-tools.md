---
title: Testowanie usług gRPC za pomocą gRPCurl w ASP.NET Core
author: jamesnk
description: Dowiedz się, jak testować usługi za pomocą narzędzi gRPC. gRPCurl narzędzie wiersza polecenia do współpracy z usługami gRPC Services. gRPCui to interaktywny interfejs użytkownika sieci Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058795"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Testowanie usług gRPC za pomocą gRPCurl w ASP.NET Core

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Dostępne są narzędzia dla programu gRPC, które umożliwiają deweloperom Testowanie usług bez kompilowania aplikacji klienckich:

* [gRPCurl](https://github.com/fullstorydev/grpcurl) to narzędzie wiersza polecenia, które umożliwia interakcję z usługami gRPC Services.
* [gRPCui](https://github.com/fullstorydev/grpcui) kompiluje się w oparciu o gRPCurl i dodaje interaktywny interfejs użytkownika sieci Web dla gRPC, podobny do narzędzi, takich jak interfejs użytkownika programu Poster i programu Swagger.

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

![wiersz polecenia gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Konfigurowanie odbicia gRPC

`grpcurl` musi znać protobuf kontrakt usług, zanim będzie mógł je wywoływać. Istnieją dwa sposoby wykonania tej czynności:

* Skonfiguruj [odbicie gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) na serwerze. gRPCurl automatycznie odnajduje kontrakty usługi.
* Określ `.proto` pliki w argumentach wiersza polecenia do gRPCurl.

Łatwiej jest używać gRPCurl z odbiciem gRPC. odbicie gRPC dodaje nową usługę gRPC do aplikacji, którą klienci mogą wywoływać w celu odnajdywania usług.

gRPC ASP.NET Core ma wbudowaną obsługę odbicia gRPC z [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) pakietem. Aby skonfigurować odbicie w aplikacji:

* Dodaj `Grpc.AspNetCore.Server.Reflection` odwołanie do pakietu.
* Rejestruj odbicie w `Startup.cs` :
  * `AddGrpcReflection` Aby zarejestrować usługi, które umożliwiają odbicie.
  * `MapGrpcReflectionService` Aby dodać punkt końcowy usługi odbicia.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Po skonfigurowaniu odbicia gRPC:

* Usługa odbicia gRPC zostanie dodana do aplikacji serwera.
* Aplikacje klienckie, które obsługują odbicie gRPC, mogą wywołać usługę odbicia w celu odnalezienia usług hostowanych przez serwer.
* usługi gRPC są nadal wywoływane z klienta programu. Odbicie włącza tylko odnajdowanie usług i nie pomija zabezpieczeń po stronie serwera. Punkty końcowe chronione [uwierzytelnianiem i autoryzacją](xref:grpc/authn-and-authz) wymagają, aby obiekt wywołujący przeszedł poświadczenia dla punktu końcowego, który ma zostać wywołany pomyślnie.

## <a name="use-grpcurl"></a>Korzystanie z polecenia `grpcurl`

`-help`Argument opisuje `grpcurl` Opcje wiersza polecenia:

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Odkryj usługi

Użyj `describe` zlecenia, aby wyświetlić usługi zdefiniowane przez serwer:

```console
$ grpcurl localhost:5001 describe
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
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Wywoływanie usług gRPC Services

Wywołaj usługę gRPC, określając nazwę usługi i metody wraz z argumentem JSON, który reprezentuje komunikat żądania. KOD JSON jest konwertowany na protobuf i wysyłany do usługi.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

W powyższym przykładzie:

* `-d`Argument określa komunikat żądania o formacie JSON. Ten argument musi występować przed adresem serwera i nazwą metody.
* Wywołuje `SayHello` metodę w `greeter.Greeter` usłudze.
* Drukuje komunikat odpowiedzi w formacie JSON.

## <a name="about-grpcui"></a>Informacje o gRPCui

gRPCui to interaktywny interfejs użytkownika sieci Web dla gRPC. Jest ona oparta na gRPCurl i oferuje graficzny interfejs użytkownika do odnajdywania i testowania usług gRPC, podobnie jak w przypadku narzędzi HTTP, takich jak Poster lub Swagger.

Aby uzyskać informacje na temat pobierania i instalowania `grpcui` , zobacz [stronę główną usługi GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Korzystanie z akcji `grpcui`

Uruchom `grpcui` z adresem serwera, aby korzystać z programu jako argumentu:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Narzędzie uruchamia okno przeglądarki z interaktywnym interfejsem użytkownika sieci Web. usługi gRPC są automatycznie wykrywane przy użyciu odbicia gRPC.

![Interfejs użytkownika sieci Web gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Strona główna usługi GitHub gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Strona główna usługi GitHub gRPCui](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
