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
# <a name="grpc-services-with-c"></a><span data-ttu-id="449b0-103">Usługi gRPC z C\#</span><span class="sxs-lookup"><span data-stu-id="449b0-103">gRPC services with C\#</span></span>

<span data-ttu-id="449b0-104">W tym dokumencie przedstawiono pojęcia potrzebne do pisania aplikacji [gRPC](https://grpc.io/docs/guides/) w języku C#.</span><span class="sxs-lookup"><span data-stu-id="449b0-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="449b0-105">Tematy omówione w tym miejscu dotyczą zarówno opartych na [rdzeniu C,](https://grpc.io/blog/grpc-stacks)jak i ASP.NET aplikacji gRPC opartych na rdzeniu.</span><span class="sxs-lookup"><span data-stu-id="449b0-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="449b0-106">plik proto</span><span class="sxs-lookup"><span data-stu-id="449b0-106">proto file</span></span>

<span data-ttu-id="449b0-107">gRPC stosuje podejście oparte na umowie do tworzenia api.</span><span class="sxs-lookup"><span data-stu-id="449b0-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="449b0-108">Bufory protokołów (protobuf) są domyślnie używane jako język projektowania interfejsu (IDL).</span><span class="sxs-lookup"><span data-stu-id="449b0-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="449b0-109">Plik \* \*.proto\* zawiera:</span><span class="sxs-lookup"><span data-stu-id="449b0-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="449b0-110">Definicja usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="449b0-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="449b0-111">Wiadomości wysyłane między klientami i serwerami.</span><span class="sxs-lookup"><span data-stu-id="449b0-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="449b0-112">Aby uzyskać więcej informacji na temat składni plików protobuf, zobacz [oficjalną dokumentację (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="449b0-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="449b0-113">Rozważmy na przykład plik *greet.proto* używany w [usłudze Wprowadzenie do usługi gRPC:](xref:tutorials/grpc/grpc-start)</span><span class="sxs-lookup"><span data-stu-id="449b0-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="449b0-114">Definiuje `Greeter` usługę.</span><span class="sxs-lookup"><span data-stu-id="449b0-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="449b0-115">Usługa `Greeter` definiuje wywołanie. `SayHello`</span><span class="sxs-lookup"><span data-stu-id="449b0-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="449b0-116">`SayHello`wysyła `HelloRequest` wiadomość i `HelloReply` odbiera wiadomość:</span><span class="sxs-lookup"><span data-stu-id="449b0-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="449b0-117">Dodawanie pliku proto do\# aplikacji C</span><span class="sxs-lookup"><span data-stu-id="449b0-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="449b0-118">Plik \* \*.proto\* jest uwzględniony w projekcie, `<Protobuf>` dodając go do grupy elementów:</span><span class="sxs-lookup"><span data-stu-id="449b0-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="449b0-119">Obsługa narzędzi języka C# dla plików .proto</span><span class="sxs-lookup"><span data-stu-id="449b0-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="449b0-120">Pakiet narzędzi [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) jest wymagany do generowania zasobów języka C# z \* \*plików .proto.\*</span><span class="sxs-lookup"><span data-stu-id="449b0-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="449b0-121">Wygenerowane zasoby (pliki):</span><span class="sxs-lookup"><span data-stu-id="449b0-121">The generated assets (files):</span></span>

* <span data-ttu-id="449b0-122">Są generowane zgodnie z potrzebami za każdym razem, gdy projekt jest budowany.</span><span class="sxs-lookup"><span data-stu-id="449b0-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="449b0-123">Nie są dodawane do projektu lub zaewidencjonowane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="449b0-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="449b0-124">Są artefakt kompilacji zawarte w katalogu *obj.*</span><span class="sxs-lookup"><span data-stu-id="449b0-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="449b0-125">Ten pakiet jest wymagany zarówno przez serwer, jak i projekty klientów.</span><span class="sxs-lookup"><span data-stu-id="449b0-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="449b0-126">Metapakiet `Grpc.AspNetCore` zawiera odniesienie `Grpc.Tools`do .</span><span class="sxs-lookup"><span data-stu-id="449b0-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="449b0-127">Projekty serwera `Grpc.AspNetCore` można dodawać za pomocą Menedżera `<PackageReference>` pakietów w programie Visual Studio lub przez dodanie do pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="449b0-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="449b0-128">Projekty klienckie `Grpc.Tools` powinny bezpośrednio odwoływać się wraz z innymi pakietami wymaganymi do korzystania z klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="449b0-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="449b0-129">Pakiet narzędzi nie jest wymagany w czasie wykonywania, więc zależność jest `PrivateAssets="All"`oznaczona:</span><span class="sxs-lookup"><span data-stu-id="449b0-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="449b0-130">Wygenerowane zasoby języka C#</span><span class="sxs-lookup"><span data-stu-id="449b0-130">Generated C# assets</span></span>

<span data-ttu-id="449b0-131">Pakiet narzędzi generuje typy języka C# reprezentujące wiadomości \* \*\* zdefiniowane w dołączonych plikach .proto.</span><span class="sxs-lookup"><span data-stu-id="449b0-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="449b0-132">W przypadku zasobów po stronie serwera generowany jest abstrakcyjny typ podstawowy usługi.</span><span class="sxs-lookup"><span data-stu-id="449b0-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="449b0-133">Typ podstawowy zawiera definicje wszystkich wywołań gRPC zawartych w pliku *.proto.*</span><span class="sxs-lookup"><span data-stu-id="449b0-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="449b0-134">Utwórz implementację usługi konkretnej, która wywodzi się z tego typu podstawowego i implementuje logikę wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="449b0-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="449b0-135">Dla `greet.proto`, przykład opisany wcześniej, typ `GreeterBase` abstrakcyjny, `SayHello` który zawiera metodę wirtualną jest generowany.</span><span class="sxs-lookup"><span data-stu-id="449b0-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="449b0-136">Konkretna `GreeterService` implementacja zastępuje metodę i implementuje logikę obsługi wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="449b0-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="449b0-137">W przypadku zasobów po stronie klienta generowany jest typ klienta betonowego.</span><span class="sxs-lookup"><span data-stu-id="449b0-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="449b0-138">Wywołania gRPC w pliku *.proto* są tłumaczone na metody na typie betonu, który można wywołać.</span><span class="sxs-lookup"><span data-stu-id="449b0-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="449b0-139">Dla `greet.proto`, przykład opisany wcześniej, generowany `GreeterClient` jest typ betonu.</span><span class="sxs-lookup"><span data-stu-id="449b0-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="449b0-140">Wywołanie `GreeterClient.SayHelloAsync` zainicjowania wywołania gRPC do serwera.</span><span class="sxs-lookup"><span data-stu-id="449b0-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="449b0-141">Domyślnie zasoby serwera i klienta są generowane dla każdego `<Protobuf>` \* \*\* pliku proto zawartego w grupie elementów.</span><span class="sxs-lookup"><span data-stu-id="449b0-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="449b0-142">Aby upewnić się, że tylko zasoby serwera `GrpcServices` są generowane `Server`w projekcie serwera, atrybut jest ustawiony na .</span><span class="sxs-lookup"><span data-stu-id="449b0-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="449b0-143">Podobnie atrybut jest ustawiony w `Client` projektach klientów.</span><span class="sxs-lookup"><span data-stu-id="449b0-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="449b0-144">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="449b0-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
