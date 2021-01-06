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
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="b4173-105">Testowanie usług gRPC za pomocą gRPCurl w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4173-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="b4173-106">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b4173-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b4173-107">Dostępne są narzędzia dla programu gRPC, które umożliwiają deweloperom Testowanie usług bez kompilowania aplikacji klienckich:</span><span class="sxs-lookup"><span data-stu-id="b4173-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="b4173-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) to narzędzie wiersza polecenia, które umożliwia interakcję z usługami gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="b4173-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="b4173-109">[gRPCui](https://github.com/fullstorydev/grpcui) kompiluje się w oparciu o gRPCurl i dodaje interaktywny interfejs użytkownika sieci Web dla gRPC, podobny do narzędzi, takich jak interfejs użytkownika programu Poster i programu Swagger.</span><span class="sxs-lookup"><span data-stu-id="b4173-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="b4173-110">W tym artykule omówiono sposób wykonywania tych zagadnień:</span><span class="sxs-lookup"><span data-stu-id="b4173-110">This article discusses how to:</span></span>

* <span data-ttu-id="b4173-111">Pobierz i zainstaluj gRPCurl oraz gRPCui.</span><span class="sxs-lookup"><span data-stu-id="b4173-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="b4173-112">Skonfiguruj odbicie gRPC za pomocą aplikacji gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4173-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="b4173-113">Odkryj i przetestuj usługi gRPC Services za pomocą programu `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="b4173-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="b4173-114">Korzystanie z usług gRPC Services za pomocą przeglądarki `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="b4173-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="b4173-115">Informacje o gRPCurl</span><span class="sxs-lookup"><span data-stu-id="b4173-115">About gRPCurl</span></span>

<span data-ttu-id="b4173-116">gRPCurl to narzędzie wiersza polecenia utworzone przez społeczność gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4173-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="b4173-117">Dostępne są następujące funkcje:</span><span class="sxs-lookup"><span data-stu-id="b4173-117">Its features include:</span></span>

* <span data-ttu-id="b4173-118">Wywoływanie usług gRPC, w tym usług przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="b4173-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="b4173-119">Odnajdowanie usługi przy użyciu [odbicia gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="b4173-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="b4173-120">Lista i opisywanie usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="b4173-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="b4173-121">Współpracuje z serwerami Secure (TLS) i Unsecure (zwykłym tekstem).</span><span class="sxs-lookup"><span data-stu-id="b4173-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="b4173-122">Aby uzyskać informacje na temat pobierania i instalowania `grpcurl` , zobacz [stronę główną usługi GitHub gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="b4173-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![wiersz polecenia gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="b4173-124">Konfigurowanie odbicia gRPC</span><span class="sxs-lookup"><span data-stu-id="b4173-124">Set up gRPC reflection</span></span>

<span data-ttu-id="b4173-125">`grpcurl` musi znać protobuf kontrakt usług, zanim będzie mógł je wywoływać.</span><span class="sxs-lookup"><span data-stu-id="b4173-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="b4173-126">Istnieją dwa sposoby wykonania tej czynności:</span><span class="sxs-lookup"><span data-stu-id="b4173-126">There are two ways to do this:</span></span>

* <span data-ttu-id="b4173-127">Skonfiguruj [odbicie gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b4173-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="b4173-128">gRPCurl automatycznie odnajduje kontrakty usługi.</span><span class="sxs-lookup"><span data-stu-id="b4173-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="b4173-129">Określ `.proto` pliki w argumentach wiersza polecenia do gRPCurl.</span><span class="sxs-lookup"><span data-stu-id="b4173-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="b4173-130">Łatwiej jest używać gRPCurl z odbiciem gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4173-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="b4173-131">odbicie gRPC dodaje nową usługę gRPC do aplikacji, którą klienci mogą wywoływać w celu odnajdywania usług.</span><span class="sxs-lookup"><span data-stu-id="b4173-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="b4173-132">gRPC ASP.NET Core ma wbudowaną obsługę odbicia gRPC z [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) pakietem.</span><span class="sxs-lookup"><span data-stu-id="b4173-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="b4173-133">Aby skonfigurować odbicie w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b4173-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="b4173-134">Dodaj `Grpc.AspNetCore.Server.Reflection` odwołanie do pakietu.</span><span class="sxs-lookup"><span data-stu-id="b4173-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="b4173-135">Rejestruj odbicie w `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="b4173-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="b4173-136">`AddGrpcReflection` Aby zarejestrować usługi, które umożliwiają odbicie.</span><span class="sxs-lookup"><span data-stu-id="b4173-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="b4173-137">`MapGrpcReflectionService` Aby dodać punkt końcowy usługi odbicia.</span><span class="sxs-lookup"><span data-stu-id="b4173-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="b4173-138">Po skonfigurowaniu odbicia gRPC:</span><span class="sxs-lookup"><span data-stu-id="b4173-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="b4173-139">Usługa odbicia gRPC zostanie dodana do aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="b4173-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="b4173-140">Aplikacje klienckie, które obsługują odbicie gRPC, mogą wywołać usługę odbicia w celu odnalezienia usług hostowanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="b4173-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="b4173-141">usługi gRPC są nadal wywoływane z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="b4173-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="b4173-142">Odbicie włącza tylko odnajdowanie usług i nie pomija zabezpieczeń po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="b4173-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="b4173-143">Punkty końcowe chronione [uwierzytelnianiem i autoryzacją](xref:grpc/authn-and-authz) wymagają, aby obiekt wywołujący przeszedł poświadczenia dla punktu końcowego, który ma zostać wywołany pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="b4173-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="b4173-144">Korzystanie z polecenia `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="b4173-144">Use `grpcurl`</span></span>

<span data-ttu-id="b4173-145">`-help`Argument opisuje `grpcurl` Opcje wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="b4173-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="b4173-146">Odkryj usługi</span><span class="sxs-lookup"><span data-stu-id="b4173-146">Discover services</span></span>

<span data-ttu-id="b4173-147">Użyj `describe` zlecenia, aby wyświetlić usługi zdefiniowane przez serwer:</span><span class="sxs-lookup"><span data-stu-id="b4173-147">Use the `describe` verb to view the services defined by the server:</span></span>

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

<span data-ttu-id="b4173-148">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="b4173-148">The preceding example:</span></span>

* <span data-ttu-id="b4173-149">Uruchamia `describe` zlecenie na serwerze `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="b4173-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="b4173-150">Drukuje usługi i metody zwracane przez odbicie gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4173-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="b4173-151">`Greeter` jest usługą zaimplementowaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="b4173-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="b4173-152">`ServerReflection` jest usługą dodaną przez `Grpc.AspNetCore.Server.Reflection` pakiet.</span><span class="sxs-lookup"><span data-stu-id="b4173-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="b4173-153">Połącz `describe` z usługą, metodą lub nazwą komunikatu, aby wyświetlić jego szczegóły:</span><span class="sxs-lookup"><span data-stu-id="b4173-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="b4173-154">Wywoływanie usług gRPC Services</span><span class="sxs-lookup"><span data-stu-id="b4173-154">Call gRPC services</span></span>

<span data-ttu-id="b4173-155">Wywołaj usługę gRPC, określając nazwę usługi i metody wraz z argumentem JSON, który reprezentuje komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="b4173-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="b4173-156">KOD JSON jest konwertowany na protobuf i wysyłany do usługi.</span><span class="sxs-lookup"><span data-stu-id="b4173-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="b4173-157">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b4173-157">In the preceding example:</span></span>

* <span data-ttu-id="b4173-158">`-d`Argument określa komunikat żądania o formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="b4173-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="b4173-159">Ten argument musi występować przed adresem serwera i nazwą metody.</span><span class="sxs-lookup"><span data-stu-id="b4173-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="b4173-160">Wywołuje `SayHello` metodę w `greeter.Greeter` usłudze.</span><span class="sxs-lookup"><span data-stu-id="b4173-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="b4173-161">Drukuje komunikat odpowiedzi w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="b4173-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="b4173-162">Informacje o gRPCui</span><span class="sxs-lookup"><span data-stu-id="b4173-162">About gRPCui</span></span>

<span data-ttu-id="b4173-163">gRPCui to interaktywny interfejs użytkownika sieci Web dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4173-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="b4173-164">Jest ona oparta na gRPCurl i oferuje graficzny interfejs użytkownika do odnajdywania i testowania usług gRPC, podobnie jak w przypadku narzędzi HTTP, takich jak Poster lub Swagger.</span><span class="sxs-lookup"><span data-stu-id="b4173-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="b4173-165">Aby uzyskać informacje na temat pobierania i instalowania `grpcui` , zobacz [stronę główną usługi GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="b4173-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="b4173-166">Korzystanie z akcji `grpcui`</span><span class="sxs-lookup"><span data-stu-id="b4173-166">Using `grpcui`</span></span>

<span data-ttu-id="b4173-167">Uruchom `grpcui` z adresem serwera, aby korzystać z programu jako argumentu:</span><span class="sxs-lookup"><span data-stu-id="b4173-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="b4173-168">Narzędzie uruchamia okno przeglądarki z interaktywnym interfejsem użytkownika sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b4173-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="b4173-169">usługi gRPC są automatycznie wykrywane przy użyciu odbicia gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4173-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Interfejs użytkownika sieci Web gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="b4173-171">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b4173-171">Additional resources</span></span>

* [<span data-ttu-id="b4173-172">Strona główna usługi GitHub gRPCurl</span><span class="sxs-lookup"><span data-stu-id="b4173-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="b4173-173">Strona główna usługi GitHub gRPCui</span><span class="sxs-lookup"><span data-stu-id="b4173-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
