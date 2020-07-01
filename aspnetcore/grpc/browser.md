---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby możliwe było wywoływanie z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 20f72deb9895111a6e691eb1ee5cd7419c8c4cb4
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793505"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="2a4ef-103">Używanie gRPC w aplikacjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2a4ef-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="2a4ef-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2a4ef-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="2a4ef-105">Dowiedz się, jak skonfigurować istniejącą ASP.NET Core usługę gRPC, która ma być wywoływana z aplikacji przeglądarki, przy użyciu protokołu [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="2a4ef-106">gRPC-Web umożliwia przeglądarce JavaScript i Blazor aplikacjom wywoływanie usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="2a4ef-107">Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="2a4ef-108">usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="2a4ef-109">Aby uzyskać instrukcje dotyczące dodawania usługi gRPC do istniejącej aplikacji ASP.NET Core, zobacz [Dodawanie usług gRPC do aplikacji ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="2a4ef-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="2a4ef-110">Instrukcje dotyczące tworzenia projektu gRPC można znaleźć w temacie <xref:tutorials/grpc/grpc-start> .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="2a4ef-111">gRPC — sieć Web w ASP.NET Core a wysłannika</span><span class="sxs-lookup"><span data-stu-id="2a4ef-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="2a4ef-112">Dostępne są dwa sposoby dodawania usługi gRPC-Web do aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="2a4ef-113">Obsługa gRPC-Web i gRPC protokołu HTTP/2 w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="2a4ef-114">Ta opcja powoduje użycie oprogramowania pośredniczącego dostarczonego przez `Grpc.AspNetCore.Web` pakiet.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="2a4ef-115">Użyj funkcji gRPC-Web [wysłannika serwera proxy](https://www.envoyproxy.io/) , aby przetłumaczyć GRPC-Web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="2a4ef-116">Przetłumaczone wywołanie jest następnie przekazywane do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="2a4ef-117">Każde podejście ma pewne wady i zalety.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="2a4ef-118">Jeśli środowisko aplikacji korzysta już z wysłannika jako serwera proxy, warto również użyć wysłannika do zapewnienia obsługi gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="2a4ef-119">W przypadku rozwiązania Basic for gRPC-Web, które wymaga tylko ASP.NET Core, `Grpc.AspNetCore.Web` to dobry wybór.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="2a4ef-120">Konfigurowanie gRPC-sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a4ef-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="2a4ef-121">usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="2a4ef-122">gRPC — sieć Web nie wymaga żadnych zmian w usługach.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="2a4ef-123">Jedyną modyfikacją jest konfiguracja uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="2a4ef-124">Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="2a4ef-125">Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="2a4ef-126">Skonfiguruj aplikację do używania gRPC-Web, dodając `UseGrpcWeb` i `EnableGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="2a4ef-127">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-127">The preceding code:</span></span>

* <span data-ttu-id="2a4ef-128">Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb` po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="2a4ef-129">Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje gRPC-Web with `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="2a4ef-130">Alternatywnie można skonfigurować oprogramowanie pośredniczące gRPC-Web, tak aby wszystkie usługi obsługiwały usługę gRPC-Web domyślnie i `EnableGrpcWeb` nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="2a4ef-131">Określ `new GrpcWebOptions { DefaultEnabled = true }` , kiedy zostanie dodane oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="2a4ef-132">Występuje znany problem, który powoduje niepowodzenie gRPC-Web, gdy jest [hostowany przez Http.sys](xref:fundamentals/servers/httpsys) w programie .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="2a4ef-133">Obejście problemu z gRPC-Web Work on Http.sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="2a4ef-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="2a4ef-134">gRPC — Web i CORS</span><span class="sxs-lookup"><span data-stu-id="2a4ef-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="2a4ef-135">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="2a4ef-136">To ograniczenie dotyczy tworzenia połączeń sieci Web gRPC z aplikacjami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="2a4ef-137">Na przykład aplikacja przeglądarki obsługiwana przez program `https://www.contoso.com` ma zablokowany dostęp do usług gRPC — sieci Web hostowanych w systemie `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="2a4ef-138">Aby osłabić to ograniczenie, można użyć funkcji udostępniania zasobów między źródłami (CORS).</span><span class="sxs-lookup"><span data-stu-id="2a4ef-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="2a4ef-139">Aby zezwolić aplikacji przeglądarki na wykonywanie wywołań sieci Web między źródłami gRPC, skonfiguruj mechanizm [CORS w ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="2a4ef-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="2a4ef-140">Użyj wbudowanej obsługi mechanizmu CORS i Uwidocznij nagłówki specyficzne dla gRPC za pomocą <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="2a4ef-141">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-141">The preceding code:</span></span>

* <span data-ttu-id="2a4ef-142">Wywołania `AddCors` dodawania usług CORS i konfigurowania zasad CORS, które ujawniają nagłówki specyficzne dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="2a4ef-143">Wywołuje `UseCors` Dodawanie oprogramowania CORS po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="2a4ef-144">Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje mechanizm CORS z `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="2a4ef-145">Wywołaj gRPC-Web z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2a4ef-145">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="2a4ef-146">Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-146">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="2a4ef-147">Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-147">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="2a4ef-148">Serwer musi być skonfigurowany do obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-148">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="2a4ef-149">Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-149">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="2a4ef-150">Przesyłanie strumieniowe serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-150">Server streaming is supported.</span></span>
* <span data-ttu-id="2a4ef-151">Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-151">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="2a4ef-152">JavaScript gRPC — klient sieci Web</span><span class="sxs-lookup"><span data-stu-id="2a4ef-152">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="2a4ef-153">Istnieje skrypt JavaScript gRPC-Web Client.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-153">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="2a4ef-154">Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="2a4ef-154">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="2a4ef-155">Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="2a4ef-155">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="2a4ef-156">Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-156">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="2a4ef-157">Jest to przydatne w przypadku [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplikacji, które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-157">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="2a4ef-158">Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="2a4ef-158">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="2a4ef-159">Jedyną modyfikacją jest sposób tworzenia kanału.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-159">The only modification is how the channel is created.</span></span>

<span data-ttu-id="2a4ef-160">Aby użyć gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-160">To use gRPC-Web:</span></span>

* <span data-ttu-id="2a4ef-161">Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-161">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="2a4ef-162">Upewnij się, że odwołanie do [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) Package ma wartość 2.29.0 lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-162">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="2a4ef-163">Skonfiguruj kanał, aby używał `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="2a4ef-163">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="2a4ef-164">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-164">The preceding code:</span></span>

* <span data-ttu-id="2a4ef-165">Konfiguruje kanał do korzystania z gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-165">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="2a4ef-166">Tworzy klienta i wywołuje połączenie przy użyciu kanału.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-166">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="2a4ef-167">`GrpcWebHandler`dostępne są następujące opcje konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a4ef-167">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="2a4ef-168">**InnerHandler**: podstawowy, <xref:System.Net.Http.HttpMessageHandler> który wysyła żądanie HTTP gRPC, na przykład `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-168">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="2a4ef-169">**GrpcWebMode**: typ wyliczeniowy, który określa, czy żądanie HTTP gRPC `Content-Type` ma wartość `application/grpc-web` lub `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-169">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="2a4ef-170">`GrpcWebMode.GrpcWeb`konfiguruje zawartość do wysłania bez kodowania.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-170">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="2a4ef-171">Wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-171">Default value.</span></span>
    * <span data-ttu-id="2a4ef-172">`GrpcWebMode.GrpcWebText`konfiguruje zawartość do kodowania base64.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-172">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="2a4ef-173">Wymagane dla wywołań przesyłania strumieniowego serwera w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-173">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="2a4ef-174">**HttpVersion**: protokół http `Version` służący do ustawiania [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) w źródłowym żądaniu HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-174">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="2a4ef-175">gRPC — sieć Web nie wymaga określonej wersji i nie przesłania jej domyślnie, chyba że zostanie określona.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-175">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2a4ef-176">Wygenerowane gRPC klienci mają metody synchronizacji i asynchroniczne do wywoływania metod jednoargumentowych.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-176">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="2a4ef-177">Na przykład `SayHello` jest synchronizowana i `SayHelloAsync` jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-177">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="2a4ef-178">Wywołanie metody synchronizacji w Blazor WebAssembly aplikacji spowoduje, że aplikacja przestanie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="2a4ef-178">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="2a4ef-179">Metody asynchroniczne muszą być zawsze używane w programie Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2a4ef-179">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a4ef-180">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2a4ef-180">Additional resources</span></span>

* [<span data-ttu-id="2a4ef-181">gRPC dla klientów sieci Web — projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="2a4ef-181">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
