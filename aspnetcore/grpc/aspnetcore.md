---
title: Usługi gRPC na platformie ASP.NET Core
author: juntaoluo
description: Poznaj podstawowe pojęcia podczas pisania usług gRPC z ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667630"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="c6f42-103">Usługi gRPC na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f42-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="c6f42-104">W tym dokumencie pokazano, jak rozpocząć pracę z usługami gRPC przy użyciu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6f42-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c6f42-105">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c6f42-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6f42-106">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6f42-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6f42-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6f42-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6f42-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c6f42-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="c6f42-109">Wprowadzenie do usługi gRPC na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f42-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="c6f42-110">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) [(jak pobrać).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c6f42-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6f42-111">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6f42-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c6f42-112">Szczegółowe instrukcje dotyczące tworzenia projektu gRPC można znaleźć w serwisie Get started z [usługami gRPC.](xref:tutorials/grpc/grpc-start)</span><span class="sxs-lookup"><span data-stu-id="c6f42-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6f42-113">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c6f42-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c6f42-114">Uruchom `dotnet new grpc -o GrpcGreeter` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="c6f42-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="c6f42-115">Dodawanie usług gRPC do aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6f42-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="c6f42-116">gRPC wymaga pakietu [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="c6f42-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="c6f42-117">Konfigurowanie gRPC</span><span class="sxs-lookup"><span data-stu-id="c6f42-117">Configure gRPC</span></span>

<span data-ttu-id="c6f42-118">W *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="c6f42-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="c6f42-119">gRPC jest włączona `AddGrpc` za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="c6f42-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="c6f42-120">Każda usługa gRPC jest dodawany do `MapGrpcService` potoku routingu za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="c6f42-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c6f42-121">ASP.NET podstawowe oprogramowanie pośredniczące i funkcje współużytkują potok routingu, dlatego aplikację można skonfigurować do obsługi dodatkowych programów obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="c6f42-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="c6f42-122">Dodatkowe programy obsługi żądań, takie jak kontrolery MVC, działają równolegle ze skonfigurowanymi usługami gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6f42-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="c6f42-123">Konfigurowanie pustułki</span><span class="sxs-lookup"><span data-stu-id="c6f42-123">Configure Kestrel</span></span>

<span data-ttu-id="c6f42-124">Punkty końcowe pustułki gRPC:</span><span class="sxs-lookup"><span data-stu-id="c6f42-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="c6f42-125">Wymagaj protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6f42-125">Require HTTP/2.</span></span>
* <span data-ttu-id="c6f42-126">Powinny być zabezpieczone za pomocą [zabezpieczeń warstwy transportowej (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="c6f42-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="c6f42-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c6f42-127">HTTP/2</span></span>

<span data-ttu-id="c6f42-128">GRPC wymaga protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6f42-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="c6f42-129">gRPC dla ASP.NET Core sprawdza poprawność [httpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) jest `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c6f42-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="c6f42-130">Kestrel [obsługuje protokół HTTP/2](xref:fundamentals/servers/kestrel#http2-support) w większości nowoczesnych systemów operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="c6f42-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="c6f42-131">Punkty końcowe pustułki są domyślnie skonfigurowane do obsługi połączeń HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6f42-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="c6f42-132">TLS</span><span class="sxs-lookup"><span data-stu-id="c6f42-132">TLS</span></span>

<span data-ttu-id="c6f42-133">Punkty końcowe pustułki stosowane w gRPC powinny być zabezpieczone TLS.</span><span class="sxs-lookup"><span data-stu-id="c6f42-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="c6f42-134">W rozwoju punktu końcowego zabezpieczonego tls `https://localhost:5001` jest tworzony automatycznie w momencie obecności certyfikatu rozwoju ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6f42-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="c6f42-135">Nie jest wymagana żadna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="c6f42-135">No configuration is required.</span></span> <span data-ttu-id="c6f42-136">`https` Prefiks weryfikuje kestrel punkt końcowy używa TLS.</span><span class="sxs-lookup"><span data-stu-id="c6f42-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="c6f42-137">W produkcji TLS musi być jawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="c6f42-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="c6f42-138">W poniższym przykładzie *appsettings.json* znajduje się punkt końcowy HTTP/2 zabezpieczony protokołem TLS:</span><span class="sxs-lookup"><span data-stu-id="c6f42-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="c6f42-139">Alternatywnie, punkty końcowe kestrel można skonfigurować w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="c6f42-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="c6f42-140">Negocjacje protokołu</span><span class="sxs-lookup"><span data-stu-id="c6f42-140">Protocol negotiation</span></span>

<span data-ttu-id="c6f42-141">TLS służy do więcej niż zabezpieczania komunikacji.</span><span class="sxs-lookup"><span data-stu-id="c6f42-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="c6f42-142">Uzgadnianie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS służy do negocjowania protokołu połączenia między klientem a serwerem, gdy punkt końcowy obsługuje wiele protokołów.</span><span class="sxs-lookup"><span data-stu-id="c6f42-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="c6f42-143">Ta negocjacja określa, czy połączenie używa protokołu HTTP/1.1 lub HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6f42-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="c6f42-144">Jeśli punkt końcowy HTTP/2 jest skonfigurowany bez protokołu TLS, punkt końcowy [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) musi być ustawiony na `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="c6f42-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="c6f42-145">Punktu końcowego z wieloma protokołami `HttpProtocols.Http1AndHttp2`(na przykład ) nie można używać bez protokołu TLS, ponieważ nie ma żadnych negocjacji.</span><span class="sxs-lookup"><span data-stu-id="c6f42-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="c6f42-146">Wszystkie połączenia z niezabezpieczonym punktem końcowym domyślnie HTTP/1.1 i wywołania gRPC nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="c6f42-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="c6f42-147">Aby uzyskać więcej informacji na temat włączania protokołu HTTP/2 i TLS z kestrelem, zobacz [Konfiguracja punktu końcowego kestrelu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="c6f42-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="c6f42-148">system macOS nie obsługuje ASP.NET core gRPC z TLS.</span><span class="sxs-lookup"><span data-stu-id="c6f42-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="c6f42-149">Dodatkowa konfiguracja jest wymagana do pomyślnego uruchomienia usług gRPC w systemie macOS.</span><span class="sxs-lookup"><span data-stu-id="c6f42-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="c6f42-150">Aby uzyskać więcej informacji, zobacz [Nie można uruchomić aplikacji ASP.NET Core gRPC w systemie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="c6f42-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="c6f42-151">Integracja z ASP.NET podstawowymi interfejsami API</span><span class="sxs-lookup"><span data-stu-id="c6f42-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="c6f42-152">Usługi gRPC mają pełny dostęp do ASP.NET podstawowych funkcji, takich jak [iniekcja zależności](xref:fundamentals/dependency-injection) (DI) i [rejestrowanie.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="c6f42-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c6f42-153">Na przykład implementacji usługi można rozwiązać usługi rejestratora z kontenera DI za pośrednictwem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="c6f42-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="c6f42-154">Domyślnie implementacja usługi gRPC można rozwiązać inne usługi DI z dowolnego okresu istnienia (Singleton, Scoped lub Przejściowy).</span><span class="sxs-lookup"><span data-stu-id="c6f42-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="c6f42-155">Rozwiązywanie problemów z protokołem HttpContext w metodach gRPC</span><span class="sxs-lookup"><span data-stu-id="c6f42-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="c6f42-156">Interfejs API gRPC zapewnia dostęp do niektórych danych komunikatów HTTP/2, takich jak metoda, host, nagłówek i zwiastuny.</span><span class="sxs-lookup"><span data-stu-id="c6f42-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="c6f42-157">Dostęp odbywa `ServerCallContext` się za pośrednictwem argumentu przekazywanego do każdej metody gRPC:</span><span class="sxs-lookup"><span data-stu-id="c6f42-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="c6f42-158">`ServerCallContext`nie zapewnia pełnego `HttpContext` dostępu do wszystkich ASP.NET interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="c6f42-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="c6f42-159">Metoda `GetHttpContext` rozszerzenia zapewnia pełny `HttpContext` dostęp do reprezentującego podstawową wiadomość HTTP/2 w ASP.NET interfejsów API:</span><span class="sxs-lookup"><span data-stu-id="c6f42-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="c6f42-160">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c6f42-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
