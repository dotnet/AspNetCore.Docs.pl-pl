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
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="5f329-105">Usługi testowe z narzędziami gRPC</span><span class="sxs-lookup"><span data-stu-id="5f329-105">Test services with gRPC tools</span></span>

<span data-ttu-id="5f329-106">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5f329-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5f329-107">Narzędzia są dostępne dla gRPC, dzięki czemu deweloperzy mogą testować usługi bez kompilowania aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="5f329-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="5f329-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) to narzędzie wiersza polecenia, które umożliwia interakcję z usługami gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="5f329-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="5f329-109">[gRPCui](https://github.com/fullstorydev/grpcui) dodaje interaktywny interfejs użytkownika sieci Web dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="5f329-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="5f329-110">W tym artykule omówiono sposób wykonywania tych zagadnień:</span><span class="sxs-lookup"><span data-stu-id="5f329-110">This article discusses how to:</span></span>

* <span data-ttu-id="5f329-111">Pobierz i zainstaluj gRPCurl oraz gRPCui.</span><span class="sxs-lookup"><span data-stu-id="5f329-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="5f329-112">Skonfiguruj odbicie gRPC za pomocą aplikacji gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f329-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="5f329-113">Odkryj i przetestuj usługi gRPC Services za pomocą programu `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="5f329-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="5f329-114">Korzystanie z usług gRPC Services za pomocą przeglądarki `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="5f329-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="5f329-115">Informacje o gRPCurl</span><span class="sxs-lookup"><span data-stu-id="5f329-115">About gRPCurl</span></span>

<span data-ttu-id="5f329-116">gRPCurl to narzędzie wiersza polecenia utworzone przez społeczność gRPC.</span><span class="sxs-lookup"><span data-stu-id="5f329-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="5f329-117">Dostępne są następujące funkcje:</span><span class="sxs-lookup"><span data-stu-id="5f329-117">Its features include:</span></span>

* <span data-ttu-id="5f329-118">Wywoływanie usług gRPC, w tym usług przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="5f329-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="5f329-119">Odnajdowanie usługi przy użyciu [odbicia gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="5f329-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="5f329-120">Lista i opisywanie usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="5f329-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="5f329-121">Współpracuje z serwerami Secure (TLS) i Unsecure (zwykłym tekstem).</span><span class="sxs-lookup"><span data-stu-id="5f329-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="5f329-122">Aby uzyskać informacje na temat pobierania i instalowania `grpcurl` , zobacz [stronę główną usługi GitHub gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="5f329-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="5f329-123">Ustawienia odbicia gRPC</span><span class="sxs-lookup"><span data-stu-id="5f329-123">Setup gRPC reflection</span></span>

<span data-ttu-id="5f329-124">`grpcurl` musi znać protobuf kontrakt usług, zanim będzie mógł je wywoływać.</span><span class="sxs-lookup"><span data-stu-id="5f329-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="5f329-125">Istnieją dwa sposoby wykonania tej czynności:</span><span class="sxs-lookup"><span data-stu-id="5f329-125">There are two ways to do this:</span></span>

* <span data-ttu-id="5f329-126">Użyj odbicia gRPC, aby odnaleźć kontrakty usługi.</span><span class="sxs-lookup"><span data-stu-id="5f329-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="5f329-127">Określ pliki *. proto* w argumentach wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="5f329-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="5f329-128">Łatwiej jest używać gRPCurl z odbiciem gRPC i odnajdywaniem usług.</span><span class="sxs-lookup"><span data-stu-id="5f329-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="5f329-129">gRPC ASP.NET Core ma wbudowaną obsługę odbicia gRPC z pakietem [gRPC. AspNetCore. Server. odbicie](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) .</span><span class="sxs-lookup"><span data-stu-id="5f329-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="5f329-130">Aby skonfigurować odbicie w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5f329-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="5f329-131">Dodaj `Grpc.AspNetCore.Server.Reflection` odwołanie do pakietu.</span><span class="sxs-lookup"><span data-stu-id="5f329-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="5f329-132">Rejestruj odbicie w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f329-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="5f329-133">`AddGrpcReflection` Aby zarejestrować usługi, które umożliwiają odbicie.</span><span class="sxs-lookup"><span data-stu-id="5f329-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="5f329-134">`MapGrpcReflectionService` Aby dodać punkt końcowy usługi odbicia.</span><span class="sxs-lookup"><span data-stu-id="5f329-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="5f329-135">Korzystanie z polecenia `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="5f329-135">Use `grpcurl`</span></span>

<span data-ttu-id="5f329-136">`-help`Argument opisuje `grpcurl` Opcje wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="5f329-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="5f329-137">Odkryj usługi</span><span class="sxs-lookup"><span data-stu-id="5f329-137">Discover services</span></span>

<span data-ttu-id="5f329-138">Użyj `describe` zlecenia, aby wyświetlić usługi zdefiniowane przez serwer:</span><span class="sxs-lookup"><span data-stu-id="5f329-138">Use the `describe` verb to view the services defined by the server:</span></span>

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

<span data-ttu-id="5f329-139">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="5f329-139">The preceding example:</span></span>

* <span data-ttu-id="5f329-140">Uruchamia `describe` zlecenie na serwerze `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="5f329-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="5f329-141">Drukuje usługi i metody zwracane przez odbicie gRPC.</span><span class="sxs-lookup"><span data-stu-id="5f329-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="5f329-142">`Greeter` jest usługą zaimplementowaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="5f329-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="5f329-143">`ServerReflection` jest usługą dodaną przez `Grpc.AspNetCore.Server.Reflection` pakiet.</span><span class="sxs-lookup"><span data-stu-id="5f329-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="5f329-144">Połącz `describe` z usługą, metodą lub nazwą komunikatu, aby wyświetlić jego szczegóły:</span><span class="sxs-lookup"><span data-stu-id="5f329-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="5f329-145">Wywoływanie usług gRPC Services</span><span class="sxs-lookup"><span data-stu-id="5f329-145">Call gRPC services</span></span>

<span data-ttu-id="5f329-146">Wywołaj usługę gRPC, określając nazwę usługi i metody wraz z argumentem JSON, który reprezentuje komunikat żądania.</span><span class="sxs-lookup"><span data-stu-id="5f329-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="5f329-147">KOD JSON jest konwertowany na protobuf i wysyłany do usługi.</span><span class="sxs-lookup"><span data-stu-id="5f329-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="5f329-148">Powyższy przykład:</span><span class="sxs-lookup"><span data-stu-id="5f329-148">The preceding example:</span></span>

* <span data-ttu-id="5f329-149">`-d` argument określa komunikat żądania o formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="5f329-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="5f329-150">Ten argument musi występować przed adresem serwera i nazwą metody.</span><span class="sxs-lookup"><span data-stu-id="5f329-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="5f329-151">Wywołuje `SayHello` metodę w `greeter.Greeter` usłudze.</span><span class="sxs-lookup"><span data-stu-id="5f329-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="5f329-152">Drukuje komunikat odpowiedzi w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="5f329-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="5f329-153">Informacje o gRPCui</span><span class="sxs-lookup"><span data-stu-id="5f329-153">About gRPCui</span></span>

<span data-ttu-id="5f329-154">gRPCui to interaktywny interfejs użytkownika sieci Web dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="5f329-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="5f329-155">Jest ona oparta na gRPCurl i oferuje graficzny interfejs użytkownika do odnajdywania i testowania usług gRPC, podobnie jak w przypadku narzędzi HTTP, takich jak Poster.</span><span class="sxs-lookup"><span data-stu-id="5f329-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="5f329-156">Aby uzyskać informacje na temat pobierania i instalowania `grpcui` , zobacz [stronę główną usługi GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="5f329-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="5f329-157">Korzystanie z akcji `grpcui`</span><span class="sxs-lookup"><span data-stu-id="5f329-157">Using `grpcui`</span></span>

<span data-ttu-id="5f329-158">Uruchom `grpcui` z adresem serwera, aby współdziałać z programem jako argumentem.</span><span class="sxs-lookup"><span data-stu-id="5f329-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="5f329-159">Narzędzie uruchomi okno przeglądarki z interaktywnym interfejsem użytkownika sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5f329-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="5f329-160">usługi gRPC są automatycznie wykrywane przy użyciu odbicia gRPC.</span><span class="sxs-lookup"><span data-stu-id="5f329-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Interfejs użytkownika sieci Web gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="5f329-162">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5f329-162">Additional resources</span></span>

* [<span data-ttu-id="5f329-163">Strona główna usługi GitHub gRPCurl</span><span class="sxs-lookup"><span data-stu-id="5f329-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="5f329-164">Strona główna usługi GitHub gRPCui</span><span class="sxs-lookup"><span data-stu-id="5f329-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="5f329-165">GRPC. AspNetCore. Server. odbicie</span><span class="sxs-lookup"><span data-stu-id="5f329-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
