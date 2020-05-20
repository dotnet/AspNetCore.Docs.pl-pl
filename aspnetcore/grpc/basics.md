---
<span data-ttu-id="d8ceb-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8ceb-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8ceb-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8ceb-102">'Blazor'</span></span>
- <span data-ttu-id="d8ceb-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8ceb-103">'Identity'</span></span>
- <span data-ttu-id="d8ceb-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8ceb-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8ceb-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8ceb-105">'Razor'</span></span>
- <span data-ttu-id="d8ceb-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d8ceb-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-services-with-c"></a><span data-ttu-id="d8ceb-107">usługi gRPC w języku C\#</span><span class="sxs-lookup"><span data-stu-id="d8ceb-107">gRPC services with C\#</span></span>

<span data-ttu-id="d8ceb-108">W tym dokumencie przedstawiono koncepcje niezbędne do pisania aplikacji [gRPC](https://grpc.io/docs/guides/) w języku C#.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-108">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="d8ceb-109">Tematy omówione tutaj dotyczą zarówno aplikacji gRPC opartych na procesorze [C](https://grpc.io/blog/grpc-stacks), jak i na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-109">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="d8ceb-110">plik PROTO</span><span class="sxs-lookup"><span data-stu-id="d8ceb-110">proto file</span></span>

<span data-ttu-id="d8ceb-111">gRPC używa podejścia pierwszego kontraktu do programowania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-111">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="d8ceb-112">Bufory protokołu (protobuf) są domyślnie używane jako język projektowania interfejsu (IDL).</span><span class="sxs-lookup"><span data-stu-id="d8ceb-112">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="d8ceb-113">Plik \* \* . proto\* zawiera:</span><span class="sxs-lookup"><span data-stu-id="d8ceb-113">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="d8ceb-114">Definicja usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-114">The definition of the gRPC service.</span></span>
* <span data-ttu-id="d8ceb-115">Komunikaty wysyłane między klientami a serwerami.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-115">The messages sent between clients and servers.</span></span>

<span data-ttu-id="d8ceb-116">Aby uzyskać więcej informacji na temat składni plików protobuf, zapoznaj się z [oficjalną dokumentacją (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="d8ceb-116">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="d8ceb-117">Rozważmy na przykład plik *Greeting. proto* używany w temacie [Rozpoczynanie pracy z usługą gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="d8ceb-117">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="d8ceb-118">Definiuje `Greeter` usługę.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-118">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="d8ceb-119">`Greeter`Usługa definiuje `SayHello` wywołanie.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-119">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="d8ceb-120">`SayHello`wysyła `HelloRequest` komunikat i odbiera `HelloReply` komunikat:</span><span class="sxs-lookup"><span data-stu-id="d8ceb-120">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="d8ceb-121">Dodawanie pliku. proto do \# aplikacji C</span><span class="sxs-lookup"><span data-stu-id="d8ceb-121">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="d8ceb-122">Plik \* \* proto\* jest dołączany do projektu przez dodanie go do `<Protobuf>` grupy Items:</span><span class="sxs-lookup"><span data-stu-id="d8ceb-122">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="d8ceb-123">Obsługa narzędzi C# dla plików. proto</span><span class="sxs-lookup"><span data-stu-id="d8ceb-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="d8ceb-124">Pakiet narzędzi [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) jest wymagany do generowania zasobów C# z plików \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="d8ceb-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="d8ceb-125">Wygenerowane zasoby (pliki):</span><span class="sxs-lookup"><span data-stu-id="d8ceb-125">The generated assets (files):</span></span>

* <span data-ttu-id="d8ceb-126">Są generowane w przypadku, gdy jest to wymagane, za każdym razem, gdy projekt jest skompilowany.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="d8ceb-127">Nie są dodawane do projektu ani zaewidencjonowane do kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="d8ceb-128">Czy artefakt kompilacji znajduje się w katalogu *obj* .</span><span class="sxs-lookup"><span data-stu-id="d8ceb-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="d8ceb-129">Ten pakiet jest wymagany przez projekty serwera i klienta.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="d8ceb-130">`Grpc.AspNetCore`Pakietbinding zawiera odwołanie do `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="d8ceb-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="d8ceb-131">Projekty serwera mogą `Grpc.AspNetCore` być dodawane przy użyciu Menedżera pakietów w programie Visual Studio lub poprzez dodanie `<PackageReference>` do pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="d8ceb-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="d8ceb-132">Projekty klienta należy bezpośrednio odwoływać `Grpc.Tools` się do innych pakietów wymaganych do korzystania z klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="d8ceb-133">Pakiet narzędzi nie jest wymagany w czasie wykonywania, dlatego zależność jest oznaczona przy użyciu `PrivateAssets="All"` :</span><span class="sxs-lookup"><span data-stu-id="d8ceb-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="d8ceb-134">Wygenerowane elementy zawartości C#</span><span class="sxs-lookup"><span data-stu-id="d8ceb-134">Generated C# assets</span></span>

<span data-ttu-id="d8ceb-135">Pakiet narzędzi generuje typy C# reprezentujące komunikaty zdefiniowane w zawartych plikach \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="d8ceb-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="d8ceb-136">W przypadku zasobów po stronie serwera jest generowany abstrakcyjny typ podstawowy usługi.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="d8ceb-137">Typ podstawowy zawiera definicje wszystkich wywołań gRPC znajdujących się w pliku *. proto* .</span><span class="sxs-lookup"><span data-stu-id="d8ceb-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="d8ceb-138">Utwórz konkretną implementację usługi, która pochodzi z tego typu podstawowego i implementuje logikę dla wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="d8ceb-139">W przypadku `greet.proto` , przykładu opisanego wcześniej, `GreeterBase` generowany jest typ abstrakcyjny, który zawiera `SayHello` metodę wirtualną.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="d8ceb-140">Konkretna implementacja `GreeterService` przesłania metodę i implementuje logikę obsługi wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="d8ceb-141">Dla zasobów po stronie klienta jest generowany konkretny typ klienta.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="d8ceb-142">Wywołania gRPC w pliku *. proto* są tłumaczone na metody w konkretnym typie, który można wywołać.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="d8ceb-143">W przypadku `greet.proto` , przykładu opisanego wcześniej, `GreeterClient` generowany jest konkretny typ.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="d8ceb-144">Wywołaj, `GreeterClient.SayHelloAsync` Aby zainicjować wywołanie gRPC na serwerze.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="d8ceb-145">Domyślnie zasoby serwera i klienta są generowane dla każdego pliku \* \* . proto\* , który znajduje się w `<Protobuf>` grupie elementów.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="d8ceb-146">Aby upewnić się, że tylko zasoby serwera są generowane w projekcie serwera, `GrpcServices` atrybut jest ustawiany na `Server` .</span><span class="sxs-lookup"><span data-stu-id="d8ceb-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="d8ceb-147">Podobnie atrybut jest ustawiany na `Client` w projektach klientów.</span><span class="sxs-lookup"><span data-stu-id="d8ceb-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8ceb-148">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d8ceb-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
