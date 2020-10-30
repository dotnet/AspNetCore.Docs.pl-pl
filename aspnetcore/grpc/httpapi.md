---
title: Tworzenie internetowych interfejsów API JSON z usługi gRPC
author: jamesnk
description: Dowiedz się, jak tworzyć interfejsy API HTTP JSON dla usług gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 45e2a1a5e6a9f00294147db769454b78c5b866e5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059939"
---
# <a name="create-json-web-apis-from-grpc"></a>Tworzenie internetowych interfejsów API JSON z usługi gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API to eksperymentalny projekt, a nie zatwierdzony produkt. Chcemy:
>
> * Przetestuj, że nasze podejście do tworzenia interfejsów API sieci Web JSON dla usług gRPC Services działa.
> * Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET.
>
> Prosimy o [opinię](https://github.com/grpc/grpc-dotnet/issues/167) , aby upewnić się, że kompilujemy coś, co deweloperzy są podobne i wydajne.

gRPC to nowoczesny sposób komunikacji między aplikacjami. gRPC używa protokołu HTTP/2, przesyłania strumieniowego, protobuf i komunikatów z wiadomościami, aby tworzyć wysokiej wydajności usługi w czasie rzeczywistym.

Jedno ograniczenie z gRPC nie dotyczy każdej platformy. Przeglądarki nie obsługują w pełni protokołu HTTP/2, co umożliwia tworzenie i wprowadzanie danych w notacji JSON do aplikacji przeglądarki. Nawet w przypadku korzyści, które gRPC, REST i JSON mają ważne miejsce w nowoczesnych aplikacjach. Kompilowanie interfejsów API sieci Web gRPC * **i** _ JSON pozwala uzyskać niepożądane koszty związane z programowaniem aplikacji.

W tym dokumencie omówiono sposób tworzenia interfejsów API sieci Web JSON przy użyciu usług gRPC Services.

## <a name="grpc-http-api"></a>Interfejs API HTTP gRPC

gRPC HTTP API to eksperymentalne rozszerzenie dla ASP.NET Core, które tworzy RESTful interfejsu API JSON dla usług gRPC Services. Po skonfigurowaniu interfejs API protokołu HTTP gRPC umożliwia aplikacjom wywoływanie usług gRPC Services przy użyciu znanych pojęć związanych z protokołem HTTP:

_ Czasowniki HTTP
* Powiązanie parametru adresu URL
* Żądania/odpowiedzi JSON

gRPC nadal mogą być używane do wywoływania usług.

### <a name="usage"></a>Użycie

1. Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. GRPC. pliku Httpapi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).
1. Zarejestruj usługi w *Startup.cs* z `AddGrpcHttpApi` .
1. Dodaj pliki [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) oraz [Google/API/Annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) do projektu.
1. Adnotuj metody gRPC w plikach *proto* z powiązaniami i trasami http:

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

`SayHello`Metodę gRPC można teraz wywołać jako gRPC + protobuf i jako interfejs API protokołu http:

* Żądając `HTTP/1.1 GET /v1/greeter/world`
* Reakcji `{ "message": "Hello world" }`

Dzienniki serwera pokazują, że wywołanie HTTP jest wykonywane przez usługę gRPC. Interfejs API protokołu HTTP gRPC mapuje przychodzące żądanie HTTP do komunikatu gRPC, a następnie konwertuje komunikat odpowiedzi na format JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Jest to przykład podstawowy. Zobacz [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) , aby uzyskać więcej opcji dostosowywania.

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API vs gRPC-Web

Zarówno gRPC HTTP API, jak i gRPC-Web zezwalają na wywoływanie usług gRPC Services z przeglądarki. Jednak każdy z nich działa inaczej:

* gRPC-Web umożliwia aplikacjom przeglądarki wywoływanie usług gRPC z przeglądarki za pomocą klienta gRPC-Web i protobuf. gRPC — sieć Web wymaga, aby aplikacja przeglądarki generowała klienta gRPC i korzystała z wysyłania małych i szybkich komunikatów protobuf.
* Interfejs API protokołu HTTP gRPC umożliwia aplikacjom przeglądarki wywoływanie usług gRPC, tak jakby były RESTful interfejsy API z notacją JSON. Aplikacja przeglądarki nie musi generować klienta gRPC ani wiedzieć o gRPC.

Nie utworzono żadnego wygenerowanego klienta dla interfejsu API HTTP gRPC. Poprzednią `Greeter` usługę można wywołać przy użyciu interfejsów API JavaScript przeglądarki:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Stan eksperymentalny

Interfejs API HTTP gRPC to eksperyment. Nie jest ona kompletna i nie jest obsługiwana. Jesteśmy zainteresowani tą technologią oraz możliwość zapewniania deweloperom aplikacji szybkiego tworzenia usług gRPC i JSON w tym samym czasie. Nie istnieje zobowiązanie do ukończenia interfejsu API protokołu HTTP gRPC.

Chcemy mierzyć zainteresowanie deweloperem w interfejsie API HTTP gRPC. Jeśli gRPC interfejs API HTTP jest interesujący, [Przekaż opinię](https://github.com/grpc/grpc-dotnet/issues/167).

## <a name="grpc-gateway"></a>GRPC — Brama

[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) jest kolejną technologią do tworzenia interfejsów API JSON RESTful z usług GRPC Services. Używa tych samych adnotacji *. proto* , aby mapować koncepcje http do usług gRPC Services.

Największą różnicą między GRPC-Gateway i gRPC interfejsu API protokołu HTTP jest GRPC-Gateway używa generowania kodu w celu utworzenia serwera zwrotnego serwera proxy. Zwrotny serwer proxy tłumaczy wywołania RESTful na gRPC, a następnie wysyła je do usługi gRPC.

Aby zainstalować i użyć usługi GRPC-Gateway, zapoznaj się z [dokumentacją GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dokumentacja usługi Google. API. HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
