---
title: Tworzenie internetowych interfejsów API JSON z gRPC
author: jamesnk
description: Dowiedz się, jak tworzyć interfejsy API HTTP JSON dla usług gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102892"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="b0f5e-103">Tworzenie internetowych interfejsów API JSON z gRPC</span><span class="sxs-lookup"><span data-stu-id="b0f5e-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="b0f5e-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b0f5e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b0f5e-105">gRPC HTTP API to eksperymentalny projekt, a nie zatwierdzony produkt.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="b0f5e-106">Chcemy:</span><span class="sxs-lookup"><span data-stu-id="b0f5e-106">We want to:</span></span>
>
> * <span data-ttu-id="b0f5e-107">Przetestuj, że nasze podejście do tworzenia interfejsów API sieci Web JSON dla usług gRPC Services działa.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="b0f5e-108">Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="b0f5e-109">Prosimy o [opinię](https://github.com/grpc/grpc-dotnet/issues/167) , aby upewnić się, że kompilujemy coś, co deweloperzy są podobne i wydajne.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="b0f5e-110">gRPC to nowoczesny sposób komunikacji między aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="b0f5e-111">gRPC używa protokołu HTTP/2, przesyłania strumieniowego, protobuf i komunikatów z wiadomościami, aby tworzyć wysokiej wydajności usługi w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="b0f5e-112">Jedno ograniczenie z gRPC nie dotyczy każdej platformy.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="b0f5e-113">Przeglądarki nie obsługują w pełni protokołu HTTP/2, co umożliwia tworzenie i wprowadzanie danych w notacji JSON do aplikacji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="b0f5e-114">Nawet w przypadku korzyści, które gRPC, REST i JSON mają ważne miejsce w nowoczesnych aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="b0f5e-115">Tworzenie interfejsów API sieci Web gRPC ***i*** JSON pozwala uzyskać niepożądane koszty tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="b0f5e-116">W tym dokumencie omówiono sposób tworzenia interfejsów API sieci Web JSON przy użyciu usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="b0f5e-117">Interfejs API HTTP gRPC</span><span class="sxs-lookup"><span data-stu-id="b0f5e-117">gRPC HTTP API</span></span>

<span data-ttu-id="b0f5e-118">gRPC HTTP API to eksperymentalne rozszerzenie dla ASP.NET Core, które tworzy RESTful interfejsu API JSON dla usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="b0f5e-119">Po skonfigurowaniu interfejs API protokołu HTTP gRPC umożliwia aplikacjom wywoływanie usług gRPC Services przy użyciu znanych pojęć związanych z protokołem HTTP:</span><span class="sxs-lookup"><span data-stu-id="b0f5e-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="b0f5e-120">Zlecenia HTTP</span><span class="sxs-lookup"><span data-stu-id="b0f5e-120">HTTP verbs</span></span>
* <span data-ttu-id="b0f5e-121">Powiązanie parametru adresu URL</span><span class="sxs-lookup"><span data-stu-id="b0f5e-121">URL parameter binding</span></span>
* <span data-ttu-id="b0f5e-122">Żądania/odpowiedzi JSON</span><span class="sxs-lookup"><span data-stu-id="b0f5e-122">JSON requests/responses</span></span>

<span data-ttu-id="b0f5e-123">gRPC nadal mogą być używane do wywoływania usług.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="b0f5e-124">Użycie</span><span class="sxs-lookup"><span data-stu-id="b0f5e-124">Usage</span></span>

1. <span data-ttu-id="b0f5e-125">Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. GRPC. pliku Httpapi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="b0f5e-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="b0f5e-126">Zarejestruj usługi w *Startup.cs* z `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="b0f5e-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="b0f5e-127">Dodaj pliki [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) oraz [Google/API/Annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) do projektu.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="b0f5e-128">Adnotuj metody gRPC w plikach *proto* z powiązaniami i trasami http:</span><span class="sxs-lookup"><span data-stu-id="b0f5e-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
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

<span data-ttu-id="b0f5e-129">`SayHello`Metodę gRPC można teraz wywołać jako gRPC + protobuf i jako interfejs API protokołu http:</span><span class="sxs-lookup"><span data-stu-id="b0f5e-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="b0f5e-130">Żądając `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="b0f5e-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="b0f5e-131">Reakcji `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="b0f5e-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="b0f5e-132">Dzienniki serwera pokazują, że wywołanie HTTP jest wykonywane przez usługę gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="b0f5e-133">Interfejs API protokołu HTTP gRPC mapuje przychodzące żądanie HTTP do komunikatu gRPC, a następnie konwertuje komunikat odpowiedzi na format JSON.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="b0f5e-134">Jest to przykład podstawowy.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-134">This is a basic example.</span></span> <span data-ttu-id="b0f5e-135">Zobacz [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) , aby uzyskać więcej opcji dostosowywania.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="b0f5e-136">gRPC HTTP API vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="b0f5e-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="b0f5e-137">Zarówno gRPC HTTP API, jak i gRPC-Web zezwalają na wywoływanie usług gRPC Services z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="b0f5e-138">Jednak każdy z nich działa inaczej:</span><span class="sxs-lookup"><span data-stu-id="b0f5e-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="b0f5e-139">gRPC-Web umożliwia aplikacjom przeglądarki wywoływanie usług gRPC z przeglądarki za pomocą klienta gRPC-Web i protobuf.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="b0f5e-140">gRPC — sieć Web wymaga, aby aplikacja przeglądarki generowała klienta gRPC i korzystała z wysyłania małych i szybkich komunikatów protobuf.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="b0f5e-141">Interfejs API protokołu HTTP gRPC umożliwia aplikacjom przeglądarki wywoływanie usług gRPC, tak jakby były RESTful interfejsy API z notacją JSON.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="b0f5e-142">Aplikacja przeglądarki nie musi generować klienta gRPC ani wiedzieć o gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="b0f5e-143">Nie utworzono żadnego wygenerowanego klienta dla interfejsu API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="b0f5e-144">Poprzednią `Greeter` usługę można wywołać przy użyciu interfejsów API JavaScript przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="b0f5e-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="b0f5e-145">Stan eksperymentalny</span><span class="sxs-lookup"><span data-stu-id="b0f5e-145">Experimental status</span></span>

<span data-ttu-id="b0f5e-146">Interfejs API HTTP gRPC to eksperyment.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="b0f5e-147">Nie jest ona kompletna i nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="b0f5e-148">Jesteśmy zainteresowani tą technologią oraz możliwość zapewniania deweloperom aplikacji szybkiego tworzenia usług gRPC i JSON w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="b0f5e-149">Nie istnieje zobowiązanie do ukończenia interfejsu API protokołu HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="b0f5e-150">Chcemy mierzyć zainteresowanie deweloperem w interfejsie API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="b0f5e-151">Jeśli gRPC interfejs API HTTP jest interesujący, [Przekaż opinię](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="b0f5e-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="b0f5e-152">GRPC — Brama</span><span class="sxs-lookup"><span data-stu-id="b0f5e-152">grpc-gateway</span></span>

<span data-ttu-id="b0f5e-153">[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) jest kolejną technologią do tworzenia interfejsów API JSON RESTful z usług GRPC Services.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="b0f5e-154">Używa tych samych adnotacji *. proto* , aby mapować koncepcje http do usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="b0f5e-155">Największą różnicą między GRPC-Gateway i gRPC interfejsu API protokołu HTTP jest GRPC-Gateway używa generowania kodu w celu utworzenia serwera zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="b0f5e-156">Zwrotny serwer proxy tłumaczy wywołania RESTful na gRPC, a następnie wysyła je do usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0f5e-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="b0f5e-157">Aby zainstalować i użyć usługi GRPC-Gateway, zapoznaj się z [dokumentacją GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span><span class="sxs-lookup"><span data-stu-id="b0f5e-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0f5e-158">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b0f5e-158">Additional resources</span></span>

* [<span data-ttu-id="b0f5e-159">Dokumentacja usługi Google. API. HttpRule</span><span class="sxs-lookup"><span data-stu-id="b0f5e-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
