---
title: Usługi gRPC na platformie ASP.NET Core
author: juntaoluo
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi pisania usług gRPC Services przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 57edfa31079cb3fca6e9e8d0fa55bcbb8bbfefca
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057486"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="adb94-103">Usługi gRPC na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="adb94-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="adb94-104">W tym dokumencie przedstawiono sposób rozpoczynania pracy z usługami gRPC Services przy użyciu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="adb94-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="adb94-105">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="adb94-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="adb94-106">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="adb94-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="adb94-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="adb94-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="adb94-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="adb94-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="adb94-109">Wprowadzenie do usługi gRPC na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="adb94-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="adb94-110">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="adb94-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="adb94-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="adb94-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="adb94-112">Szczegółowe instrukcje dotyczące tworzenia projektu gRPC można znaleźć w temacie Wprowadzenie do [usług gRPC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="adb94-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="adb94-113">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="adb94-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="adb94-114">Uruchom `dotnet new grpc -o GrpcGreeter` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="adb94-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="adb94-115">Dodawanie usług gRPC do aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="adb94-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="adb94-116">gRPC wymaga pakietu [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="adb94-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="adb94-117">Konfigurowanie gRPC</span><span class="sxs-lookup"><span data-stu-id="adb94-117">Configure gRPC</span></span>

<span data-ttu-id="adb94-118">W *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="adb94-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="adb94-119">gRPC jest włączona z `AddGrpc` metodą.</span><span class="sxs-lookup"><span data-stu-id="adb94-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="adb94-120">Każda usługa gRPC jest dodawana do potoku routingu za pomocą `MapGrpcService` metody.</span><span class="sxs-lookup"><span data-stu-id="adb94-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="adb94-121">ASP.NET Core oprogramowanie pośredniczące i funkcje współużytkują potok routingu, w związku z czym aplikacja może być skonfigurowana w taki sposób, aby obsługiwała dodatkowe procedury obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="adb94-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="adb94-122">Dodatkowe programy obsługi żądań, takie jak kontrolery MVC, pracują równolegle ze skonfigurowanymi usługami gRPC.</span><span class="sxs-lookup"><span data-stu-id="adb94-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="adb94-123">Opcje serwera</span><span class="sxs-lookup"><span data-stu-id="adb94-123">Server options</span></span>

<span data-ttu-id="adb94-124">usługi gRPC Services mogą być hostowane przez wszystkie wbudowane serwery ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="adb94-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="adb94-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="adb94-125">Kestrel</span></span>
> * <span data-ttu-id="adb94-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="adb94-126">TestServer</span></span>
> * <span data-ttu-id="adb94-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="adb94-127">IIS&dagger;</span></span>
> * <span data-ttu-id="adb94-128">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="adb94-128">HTTP.sys&dagger;</span></span>

<span data-ttu-id="adb94-129">&dagger;Usługi IIS i HTTP.sys wymagają programu .NET 5 i systemu Windows 10 Build 20241 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="adb94-129">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="adb94-130">Aby uzyskać więcej informacji na temat wybierania właściwego serwera dla aplikacji ASP.NET Core, zobacz <xref:fundamentals/servers/index> .</span><span class="sxs-lookup"><span data-stu-id="adb94-130">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="adb94-131">Kestrel</span><span class="sxs-lookup"><span data-stu-id="adb94-131">Kestrel</span></span>

<span data-ttu-id="adb94-132">[Kestrel](xref:fundamentals/servers/kestrel) to Międzyplatformowy serwer sieci web dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="adb94-132">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="adb94-133">Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji w HTTP.sys, takich jak Udostępnianie portów.</span><span class="sxs-lookup"><span data-stu-id="adb94-133">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="adb94-134">Punkty końcowe Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="adb94-134">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="adb94-135">Wymagaj protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-135">Require HTTP/2.</span></span>
* <span data-ttu-id="adb94-136">Powinien być zabezpieczony przy użyciu [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="adb94-136">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="adb94-137">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="adb94-137">HTTP/2</span></span>

<span data-ttu-id="adb94-138">gRPC wymaga protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-138">gRPC requires HTTP/2.</span></span> <span data-ttu-id="adb94-139">gRPC dla ASP.NET Core sprawdza poprawność elementu [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="adb94-139">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="adb94-140">Kestrel [obsługuje protokół HTTP/2](xref:fundamentals/servers/kestrel/http2) w większości nowoczesnych systemów operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="adb94-140">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="adb94-141">Punkty końcowe Kestrel są domyślnie skonfigurowane do obsługi połączeń HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-141">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="adb94-142">TLS</span><span class="sxs-lookup"><span data-stu-id="adb94-142">TLS</span></span>

<span data-ttu-id="adb94-143">Punkty końcowe Kestrel używane dla gRPC powinny być zabezpieczone przy użyciu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-143">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="adb94-144">W trakcie opracowywania, punkt końcowy zabezpieczony przy użyciu protokołu TLS jest automatycznie tworzony na `https://localhost:5001` czas, gdy obecny jest ASP.NET Core certyfikat deweloperski.</span><span class="sxs-lookup"><span data-stu-id="adb94-144">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="adb94-145">Nie jest wymagana żadna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="adb94-145">No configuration is required.</span></span> <span data-ttu-id="adb94-146">`https`Prefiks sprawdza, czy punkt końcowy Kestrel korzysta z protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-146">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="adb94-147">W środowisku produkcyjnym należy jawnie skonfigurować protokół TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-147">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="adb94-148">W poniższym *appsettings.json* przykładzie jest dostępny punkt końcowy HTTP/2 zabezpieczony przy użyciu protokołu TLS:</span><span class="sxs-lookup"><span data-stu-id="adb94-148">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="adb94-149">Alternatywnie można skonfigurować punkty końcowe Kestrel w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="adb94-149">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="adb94-150">Negocjowanie protokołu</span><span class="sxs-lookup"><span data-stu-id="adb94-150">Protocol negotiation</span></span>

<span data-ttu-id="adb94-151">Protokół TLS jest używany przez więcej niż Zabezpieczanie komunikacji.</span><span class="sxs-lookup"><span data-stu-id="adb94-151">TLS is used for more than securing communication.</span></span> <span data-ttu-id="adb94-152">Uzgadnianie [protokołu warstwy aplikacji TLS (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) służy do negocjowania protokołu połączenia między klientem a serwerem, gdy punkt końcowy obsługuje wiele protokołów.</span><span class="sxs-lookup"><span data-stu-id="adb94-152">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="adb94-153">Ta negocjacja określa, czy połączenie używa protokołu HTTP/1.1 czy HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-153">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="adb94-154">Jeśli punkt końcowy protokołu HTTP/2 jest skonfigurowany bez protokołu TLS, [ListenOptions. protokoły](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) punktu końcowego muszą mieć ustawioną wartość `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="adb94-154">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="adb94-155">Punkt końcowy z wieloma protokołami (na przykład `HttpProtocols.Http1AndHttp2` ) nie może być używany bez protokołu TLS, ponieważ nie ma negocjacji.</span><span class="sxs-lookup"><span data-stu-id="adb94-155">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="adb94-156">Wszystkie połączenia z niezabezpieczonym punktem końcowym domyślne do protokołu HTTP/1.1 i wywołania gRPC kończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="adb94-156">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="adb94-157">Aby uzyskać więcej informacji na temat włączania protokołów HTTP/2 i TLS przy użyciu usługi Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="adb94-157">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="adb94-158">Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-158">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="adb94-159">Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="adb94-159">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="adb94-160">Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="adb94-160">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="adb94-161">IIS</span><span class="sxs-lookup"><span data-stu-id="adb94-161">IIS</span></span>

<span data-ttu-id="adb94-162">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) to elastyczny, bezpieczny i zarządzany serwer sieci Web do hostowania aplikacji sieci Web, w tym ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="adb94-162">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="adb94-163">Do hostowania usług gRPC Services za pomocą usług IIS wymagane są platformy .NET 5 i Windows 10 Build 20241 lub nowsze.</span><span class="sxs-lookup"><span data-stu-id="adb94-163">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="adb94-164">Usługi IIS muszą być skonfigurowane do używania protokołów TLS i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-164">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="adb94-165">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="adb94-165">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="adb94-166">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="adb94-166">HTTP.sys</span></span>

<span data-ttu-id="adb94-167">[HTTP.sys](xref:fundamentals/servers/httpsys) to serwer sieci web dla ASP.NET Core, który działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="adb94-167">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="adb94-168">Program .NET 5 i system Windows 10 Build 20241 lub nowszy są wymagane do hostowania usług gRPC Services przy użyciu HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="adb94-168">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="adb94-169">HTTP.sys musi być skonfigurowany do używania protokołów TLS i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-169">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="adb94-170">Aby uzyskać więcej informacji, zobacz  [HTTP.sys serwera sieci Web obsługa protokołu HTTP/2](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="adb94-170">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="adb94-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="adb94-171">Kestrel</span></span>

<span data-ttu-id="adb94-172">[Kestrel](xref:fundamentals/servers/kestrel) to Międzyplatformowy serwer sieci web dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="adb94-172">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="adb94-173">Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji w HTTP.sys, takich jak Udostępnianie portów.</span><span class="sxs-lookup"><span data-stu-id="adb94-173">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="adb94-174">Punkty końcowe Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="adb94-174">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="adb94-175">Wymagaj protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-175">Require HTTP/2.</span></span>
* <span data-ttu-id="adb94-176">Powinien być zabezpieczony przy użyciu [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="adb94-176">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="adb94-177">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="adb94-177">HTTP/2</span></span>

<span data-ttu-id="adb94-178">gRPC wymaga protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-178">gRPC requires HTTP/2.</span></span> <span data-ttu-id="adb94-179">gRPC dla ASP.NET Core sprawdza poprawność elementu [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="adb94-179">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="adb94-180">Kestrel [obsługuje protokół HTTP/2](xref:fundamentals/servers/kestrel#http2-support) w większości nowoczesnych systemów operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="adb94-180">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="adb94-181">Punkty końcowe Kestrel są domyślnie skonfigurowane do obsługi połączeń HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-181">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="adb94-182">TLS</span><span class="sxs-lookup"><span data-stu-id="adb94-182">TLS</span></span>

<span data-ttu-id="adb94-183">Punkty końcowe Kestrel używane dla gRPC powinny być zabezpieczone przy użyciu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-183">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="adb94-184">W trakcie opracowywania, punkt końcowy zabezpieczony przy użyciu protokołu TLS jest automatycznie tworzony na `https://localhost:5001` czas, gdy obecny jest ASP.NET Core certyfikat deweloperski.</span><span class="sxs-lookup"><span data-stu-id="adb94-184">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="adb94-185">Nie jest wymagana żadna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="adb94-185">No configuration is required.</span></span> <span data-ttu-id="adb94-186">`https`Prefiks sprawdza, czy punkt końcowy Kestrel korzysta z protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-186">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="adb94-187">W środowisku produkcyjnym należy jawnie skonfigurować protokół TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-187">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="adb94-188">W poniższym *appsettings.json* przykładzie jest dostępny punkt końcowy HTTP/2 zabezpieczony przy użyciu protokołu TLS:</span><span class="sxs-lookup"><span data-stu-id="adb94-188">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="adb94-189">Alternatywnie można skonfigurować punkty końcowe Kestrel w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="adb94-189">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="adb94-190">Negocjowanie protokołu</span><span class="sxs-lookup"><span data-stu-id="adb94-190">Protocol negotiation</span></span>

<span data-ttu-id="adb94-191">Protokół TLS jest używany przez więcej niż Zabezpieczanie komunikacji.</span><span class="sxs-lookup"><span data-stu-id="adb94-191">TLS is used for more than securing communication.</span></span> <span data-ttu-id="adb94-192">Uzgadnianie [protokołu warstwy aplikacji TLS (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) służy do negocjowania protokołu połączenia między klientem a serwerem, gdy punkt końcowy obsługuje wiele protokołów.</span><span class="sxs-lookup"><span data-stu-id="adb94-192">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="adb94-193">Ta negocjacja określa, czy połączenie używa protokołu HTTP/1.1 czy HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="adb94-193">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="adb94-194">Jeśli punkt końcowy protokołu HTTP/2 jest skonfigurowany bez protokołu TLS, [ListenOptions. protokoły](xref:fundamentals/servers/kestrel#listenoptionsprotocols) punktu końcowego muszą mieć ustawioną wartość `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="adb94-194">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="adb94-195">Punkt końcowy z wieloma protokołami (na przykład `HttpProtocols.Http1AndHttp2` ) nie może być używany bez protokołu TLS, ponieważ nie ma negocjacji.</span><span class="sxs-lookup"><span data-stu-id="adb94-195">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="adb94-196">Wszystkie połączenia z niezabezpieczonym punktem końcowym domyślne do protokołu HTTP/1.1 i wywołania gRPC kończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="adb94-196">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="adb94-197">Aby uzyskać więcej informacji na temat włączania protokołów HTTP/2 i TLS przy użyciu usługi Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="adb94-197">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="adb94-198">Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="adb94-198">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="adb94-199">Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="adb94-199">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="adb94-200">Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="adb94-200">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="adb94-201">Integracja z interfejsami API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="adb94-201">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="adb94-202">usługi gRPC mają pełny dostęp do funkcji ASP.NET Core, takich jak [iniekcja zależności](xref:fundamentals/dependency-injection) (di) i [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="adb94-202">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="adb94-203">Na przykład implementacja usługi może rozpoznać usługę rejestratora z kontenera DI za pośrednictwem konstruktora:</span><span class="sxs-lookup"><span data-stu-id="adb94-203">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="adb94-204">Domyślnie implementacja usługi gRPC może rozwiązywać inne usługi DI z dowolnym okresem istnienia (pojedynczym, zakresowym lub przejściowym).</span><span class="sxs-lookup"><span data-stu-id="adb94-204">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="adb94-205">Rozwiąż element HttpContext w metodach gRPC</span><span class="sxs-lookup"><span data-stu-id="adb94-205">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="adb94-206">Interfejs API gRPC zapewnia dostęp do niektórych danych komunikatów HTTP/2, takich jak metoda, host, nagłówek i przyczepy.</span><span class="sxs-lookup"><span data-stu-id="adb94-206">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="adb94-207">Dostęp odbywa się za pomocą `ServerCallContext` argumentu przesłanego do każdej metody gRPC:</span><span class="sxs-lookup"><span data-stu-id="adb94-207">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="adb94-208">`ServerCallContext` nie zapewnia pełnego dostępu do `HttpContext` wszystkich interfejsów api ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="adb94-208">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="adb94-209">`GetHttpContext`Metoda rozszerzająca zapewnia pełny dostęp do `HttpContext` reprezentowania podstawowego komunikatu http/2 w interfejsach API ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="adb94-209">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="adb94-210">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="adb94-210">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
