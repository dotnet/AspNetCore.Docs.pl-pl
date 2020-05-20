---
<span data-ttu-id="f2f96-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f2f96-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2f96-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2f96-102">'Blazor'</span></span>
- <span data-ttu-id="f2f96-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2f96-103">'Identity'</span></span>
- <span data-ttu-id="f2f96-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2f96-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2f96-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2f96-105">'Razor'</span></span>
- <span data-ttu-id="f2f96-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f2f96-106">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="f2f96-107">Wprowadzenie do gRPC na platformie .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2f96-107">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="f2f96-108">Przez [John Luo](https://github.com/juntaoluo) i [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f2f96-108">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f2f96-109">[gRPC](https://grpc.io/docs/guides/) to język niezależny od i środowisko zdalnego wywołania procedury (RPC) o wysokiej wydajności.</span><span class="sxs-lookup"><span data-stu-id="f2f96-109">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="f2f96-110">Główne zalety gRPC są następujące:</span><span class="sxs-lookup"><span data-stu-id="f2f96-110">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="f2f96-111">Nowoczesne, wysoce wydajne i uproszczone środowisko RPC.</span><span class="sxs-lookup"><span data-stu-id="f2f96-111">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="f2f96-112">Tworzenie aplikacji interfejsu API w pierwszej kolejności, domyślnie przy użyciu buforów protokołu, dzięki czemu można wdrażać implementacje języka niezależny od.</span><span class="sxs-lookup"><span data-stu-id="f2f96-112">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="f2f96-113">Narzędzia dostępne dla wielu języków do generowania serwerów i klientów z jednoznacznie określonymi typami.</span><span class="sxs-lookup"><span data-stu-id="f2f96-113">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="f2f96-114">Obsługuje wywołania przesyłania strumieniowego klienta, serwera i dwukierunkowego.</span><span class="sxs-lookup"><span data-stu-id="f2f96-114">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="f2f96-115">Zredukowanie użycia sieci przy użyciu serializacji binarnej protobuf.</span><span class="sxs-lookup"><span data-stu-id="f2f96-115">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="f2f96-116">Te korzyści sprawiają, że gRPC doskonale nadaje się do:</span><span class="sxs-lookup"><span data-stu-id="f2f96-116">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="f2f96-117">Lekkie mikrousługi, w których wydajność jest krytyczna.</span><span class="sxs-lookup"><span data-stu-id="f2f96-117">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="f2f96-118">Systemy Polyglot, w których wiele języków jest wymaganych do programowania.</span><span class="sxs-lookup"><span data-stu-id="f2f96-118">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="f2f96-119">Usługi w czasie rzeczywistym, które muszą obsługiwać żądania przesyłania strumieniowego lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f2f96-119">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="f2f96-120">Obsługa narzędzi C# dla plików. proto</span><span class="sxs-lookup"><span data-stu-id="f2f96-120">C# Tooling support for .proto files</span></span>

<span data-ttu-id="f2f96-121">gRPC używa podejścia pierwszego kontraktu do programowania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="f2f96-121">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="f2f96-122">Usługi i komunikaty są zdefiniowane w plikach \* \* . proto\* :</span><span class="sxs-lookup"><span data-stu-id="f2f96-122">Services and messages are defined in *\*.proto* files:</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="f2f96-123">Typy .NET dla usług, klientów i komunikatów są generowane automatycznie przez dołączenie plików \* \* proto\* w projekcie:</span><span class="sxs-lookup"><span data-stu-id="f2f96-123">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="f2f96-124">Dodaj odwołanie do pakietu do pakietu [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="f2f96-124">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="f2f96-125">Dodaj pliki \* \* . proto\* do `<Protobuf>` grupy elementów.</span><span class="sxs-lookup"><span data-stu-id="f2f96-125">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="f2f96-126">Więcej informacji o obsłudze narzędzi gRPC można znaleźć w temacie <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="f2f96-126">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="f2f96-127">usługi gRPC Services na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2f96-127">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="f2f96-128">usługi gRPC Services mogą być hostowane na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2f96-128">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="f2f96-129">Usługi mają pełną integrację z popularnymi funkcjami ASP.NET Core, takimi jak rejestrowanie, iniekcja zależności (DI), uwierzytelnianie i autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="f2f96-129">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="f2f96-130">Szablon projektu usługi gRPC to usługa początkowa:</span><span class="sxs-lookup"><span data-stu-id="f2f96-130">The gRPC service project template provides a starter service:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

<span data-ttu-id="f2f96-131">`GreeterService`dziedziczy z `GreeterBase` typu, który jest generowany na podstawie `Greeter` usługi w pliku \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="f2f96-131">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="f2f96-132">Usługa jest dostępna dla klientów w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2f96-132">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="f2f96-133">Aby dowiedzieć się więcej na temat usług gRPC Services na ASP.NET Core, zobacz <xref:grpc/aspnetcore> .</span><span class="sxs-lookup"><span data-stu-id="f2f96-133">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="f2f96-134">Wywoływanie usług gRPC za pomocą klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="f2f96-134">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="f2f96-135">gRPC klienci są konkretnymi typami klientów, które są [generowane z plików \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="f2f96-135">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="f2f96-136">Konkretny klient gRPC ma metody, które są tłumaczone na usługę gRPC w pliku \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="f2f96-136">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="f2f96-137">Klient gRPC jest tworzony przy użyciu kanału, który reprezentuje długotrwałe połączenie z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="f2f96-137">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="f2f96-138">Kanał można utworzyć przy użyciu `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="f2f96-138">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="f2f96-139">Aby uzyskać więcej informacji na temat tworzenia klientów i wywoływania różnych metod usługi, zobacz <xref:grpc/client> .</span><span class="sxs-lookup"><span data-stu-id="f2f96-139">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2f96-140">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f2f96-140">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
