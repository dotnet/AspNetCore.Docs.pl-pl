---
title: Usługi gRPC na platformie ASP.NET Core
author: juntaoluo
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi pisania usług gRPC Services przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/aspnetcore
ms.openlocfilehash: b120aa4ab6922445f2c53f3b1cb3bd5c159d8a84
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057833"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="ab702-103">Usługi gRPC na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab702-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="ab702-104">W tym dokumencie przedstawiono sposób rozpoczynania pracy z usługami gRPC Services przy użyciu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab702-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="ab702-105">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ab702-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab702-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab702-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ab702-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab702-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab702-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ab702-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="ab702-109">Wprowadzenie do usługi gRPC na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab702-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="ab702-110">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ab702-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab702-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab702-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ab702-112">Szczegółowe instrukcje dotyczące tworzenia projektu gRPC można znaleźć w temacie Wprowadzenie do [usług gRPC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="ab702-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ab702-113">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ab702-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ab702-114">Uruchom `dotnet new grpc -o GrpcGreeter` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="ab702-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="ab702-115">Dodawanie usług gRPC do aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab702-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="ab702-116">gRPC wymaga pakietu [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="ab702-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="ab702-117">Konfigurowanie gRPC</span><span class="sxs-lookup"><span data-stu-id="ab702-117">Configure gRPC</span></span>

<span data-ttu-id="ab702-118">W *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="ab702-118">In *Startup.cs* :</span></span>

* <span data-ttu-id="ab702-119">gRPC jest włączona z `AddGrpc` metodą.</span><span class="sxs-lookup"><span data-stu-id="ab702-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="ab702-120">Każda usługa gRPC jest dodawana do potoku routingu za pomocą `MapGrpcService` metody.</span><span class="sxs-lookup"><span data-stu-id="ab702-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ab702-121">ASP.NET Core middlewares i funkcje korzystają z potoku routingu, w związku z czym aplikacja może być skonfigurowana w taki sposób, aby obsługiwała dodatkowe procedury obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="ab702-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="ab702-122">Dodatkowe programy obsługi żądań, takie jak kontrolery MVC, pracują równolegle ze skonfigurowanymi usługami gRPC.</span><span class="sxs-lookup"><span data-stu-id="ab702-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="ab702-123">Konfigurowanie Kestrel</span><span class="sxs-lookup"><span data-stu-id="ab702-123">Configure Kestrel</span></span>

<span data-ttu-id="ab702-124">Punkty końcowe Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="ab702-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="ab702-125">Wymagaj protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab702-125">Require HTTP/2.</span></span>
* <span data-ttu-id="ab702-126">Powinien być zabezpieczony przy użyciu [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="ab702-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="ab702-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ab702-127">HTTP/2</span></span>

<span data-ttu-id="ab702-128">gRPC wymaga protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab702-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="ab702-129">gRPC dla ASP.NET Core sprawdza poprawność elementu [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="ab702-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="ab702-130">Kestrel [obsługuje protokół HTTP/2](xref:fundamentals/servers/kestrel#http2-support) w większości nowoczesnych systemów operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="ab702-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="ab702-131">Punkty końcowe Kestrel są domyślnie skonfigurowane do obsługi połączeń HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab702-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="ab702-132">TLS</span><span class="sxs-lookup"><span data-stu-id="ab702-132">TLS</span></span>

<span data-ttu-id="ab702-133">Punkty końcowe Kestrel używane dla gRPC powinny być zabezpieczone przy użyciu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="ab702-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="ab702-134">W trakcie opracowywania, punkt końcowy zabezpieczony przy użyciu protokołu TLS jest automatycznie tworzony na `https://localhost:5001` czas, gdy obecny jest ASP.NET Core certyfikat deweloperski.</span><span class="sxs-lookup"><span data-stu-id="ab702-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="ab702-135">Nie jest wymagana żadna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="ab702-135">No configuration is required.</span></span> <span data-ttu-id="ab702-136">`https`Prefiks sprawdza, czy punkt końcowy Kestrel korzysta z protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="ab702-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="ab702-137">W środowisku produkcyjnym należy jawnie skonfigurować protokół TLS.</span><span class="sxs-lookup"><span data-stu-id="ab702-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="ab702-138">W poniższym *:::no-loc(appsettings.json):::* przykładzie jest dostępny punkt końcowy HTTP/2 zabezpieczony przy użyciu protokołu TLS:</span><span class="sxs-lookup"><span data-stu-id="ab702-138">In the following *:::no-loc(appsettings.json):::* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/:::no-loc(appsettings.json):::?highlight=4)]

<span data-ttu-id="ab702-139">Alternatywnie można skonfigurować punkty końcowe Kestrel w *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="ab702-139">Alternatively, Kestrel endpoints can be configured in *Program.cs* :</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="ab702-140">Negocjowanie protokołu</span><span class="sxs-lookup"><span data-stu-id="ab702-140">Protocol negotiation</span></span>

<span data-ttu-id="ab702-141">Protokół TLS jest używany przez więcej niż Zabezpieczanie komunikacji.</span><span class="sxs-lookup"><span data-stu-id="ab702-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="ab702-142">Uzgadnianie [protokołu warstwy aplikacji TLS (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) służy do negocjowania protokołu połączenia między klientem a serwerem, gdy punkt końcowy obsługuje wiele protokołów.</span><span class="sxs-lookup"><span data-stu-id="ab702-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="ab702-143">Ta negocjacja określa, czy połączenie używa protokołu HTTP/1.1 czy HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab702-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="ab702-144">Jeśli punkt końcowy protokołu HTTP/2 jest skonfigurowany bez protokołu TLS, [ListenOptions. protokoły](xref:fundamentals/servers/kestrel#listenoptionsprotocols) punktu końcowego muszą mieć ustawioną wartość `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="ab702-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="ab702-145">Punkt końcowy z wieloma protokołami (na przykład `HttpProtocols.Http1AndHttp2` ) nie może być używany bez protokołu TLS, ponieważ nie ma negocjacji.</span><span class="sxs-lookup"><span data-stu-id="ab702-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="ab702-146">Wszystkie połączenia z niezabezpieczonym punktem końcowym domyślne do protokołu HTTP/1.1 i wywołania gRPC kończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="ab702-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="ab702-147">Aby uzyskać więcej informacji na temat włączania protokołów HTTP/2 i TLS przy użyciu usługi Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab702-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="ab702-148">Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="ab702-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ab702-149">Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="ab702-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ab702-150">Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="ab702-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="ab702-151">Integracja z interfejsami API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab702-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="ab702-152">usługi gRPC mają pełny dostęp do funkcji ASP.NET Core, takich jak [iniekcja zależności](xref:fundamentals/dependency-injection) (di) i [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="ab702-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ab702-153">Na przykład implementacja usługi może rozpoznać usługę rejestratora z kontenera DI za pośrednictwem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="ab702-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="ab702-154">Domyślnie implementacja usługi gRPC może rozwiązywać inne usługi DI z dowolnym okresem istnienia (pojedynczym, zakresowym lub przejściowym).</span><span class="sxs-lookup"><span data-stu-id="ab702-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="ab702-155">Rozwiąż element HttpContext w metodach gRPC</span><span class="sxs-lookup"><span data-stu-id="ab702-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="ab702-156">Interfejs API gRPC zapewnia dostęp do niektórych danych komunikatów HTTP/2, takich jak metoda, host, nagłówek i przyczepy.</span><span class="sxs-lookup"><span data-stu-id="ab702-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="ab702-157">Dostęp odbywa się za pomocą `ServerCallContext` argumentu przesłanego do każdej metody gRPC:</span><span class="sxs-lookup"><span data-stu-id="ab702-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="ab702-158">`ServerCallContext` nie zapewnia pełnego dostępu do `HttpContext` wszystkich interfejsów api ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ab702-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="ab702-159">`GetHttpContext`Metoda rozszerzająca zapewnia pełny dostęp do `HttpContext` reprezentowania podstawowego komunikatu http/2 w interfejsach API ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="ab702-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="ab702-160">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ab702-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
