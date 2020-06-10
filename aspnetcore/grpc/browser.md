---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby możliwe było wywoływanie z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106601"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="8f730-103">Używanie gRPC w aplikacjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="8f730-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="8f730-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="8f730-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="8f730-105">Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="8f730-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="8f730-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który umożliwia przeglądarce JavaScript i Blazor aplikacji Wywoływanie usługi gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="8f730-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="8f730-107">W tym artykule wyjaśniono, jak używać gRPC-Web w programie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f730-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="8f730-108">gRPC — sieć Web w ASP.NET Core a wysłannika</span><span class="sxs-lookup"><span data-stu-id="8f730-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="8f730-109">Dostępne są dwa sposoby dodawania usługi gRPC-Web do aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8f730-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="8f730-110">Obsługa gRPC-Web i gRPC protokołu HTTP/2 w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f730-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="8f730-111">Ta opcja powoduje użycie oprogramowania pośredniczącego dostarczonego przez `Grpc.AspNetCore.Web` pakiet.</span><span class="sxs-lookup"><span data-stu-id="8f730-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="8f730-112">Użyj funkcji gRPC-Web [wysłannika serwera proxy](https://www.envoyproxy.io/) , aby przetłumaczyć GRPC-Web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="8f730-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="8f730-113">Przetłumaczone wywołanie jest następnie przekazywane do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f730-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="8f730-114">Każde podejście ma pewne wady i zalety.</span><span class="sxs-lookup"><span data-stu-id="8f730-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="8f730-115">Jeśli używasz już wysłannika jako serwera proxy w środowisku aplikacji, warto również użyć go do zapewnienia obsługi gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8f730-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="8f730-116">Jeśli potrzebujesz prostego rozwiązania dla gRPC-Web, które wymaga ASP.NET Core, `Grpc.AspNetCore.Web` to dobry wybór.</span><span class="sxs-lookup"><span data-stu-id="8f730-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="8f730-117">Konfigurowanie gRPC-sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f730-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="8f730-118">usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8f730-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="8f730-119">gRPC — sieć Web nie wymaga żadnych zmian w usługach.</span><span class="sxs-lookup"><span data-stu-id="8f730-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="8f730-120">Jedyną modyfikacją jest konfiguracja uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8f730-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="8f730-121">Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8f730-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="8f730-122">Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="8f730-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="8f730-123">Skonfiguruj aplikację do używania gRPC-Web, dodając `UseGrpcWeb` i `EnableGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8f730-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="8f730-124">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8f730-124">The preceding code:</span></span>

* <span data-ttu-id="8f730-125">Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb` po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="8f730-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="8f730-126">Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje gRPC-Web with `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="8f730-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="8f730-127">Alternatywnie można skonfigurować oprogramowanie pośredniczące gRPC-Web, tak aby wszystkie usługi obsługiwały usługę gRPC-Web domyślnie i `EnableGrpcWeb` nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="8f730-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="8f730-128">Określ `new GrpcWebOptions { DefaultEnabled = true }` , kiedy zostanie dodane oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="8f730-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="8f730-129">Występuje znany problem, który powoduje niepowodzenie gRPC-Web w przypadku [hostowania przez http. sys](xref:fundamentals/servers/httpsys) w programie .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="8f730-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="8f730-130">Obejście problemu z gRPC-Web Working for http. sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="8f730-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="8f730-131">gRPC — Web i CORS</span><span class="sxs-lookup"><span data-stu-id="8f730-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="8f730-132">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8f730-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8f730-133">To ograniczenie dotyczy tworzenia połączeń sieci Web gRPC z aplikacjami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="8f730-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="8f730-134">Na przykład aplikacja przeglądarki obsługiwana przez program `https://www.contoso.com` ma zablokowany dostęp do usług gRPC — sieci Web hostowanych w systemie `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="8f730-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="8f730-135">Aby osłabić to ograniczenie, można użyć funkcji udostępniania zasobów między źródłami (CORS).</span><span class="sxs-lookup"><span data-stu-id="8f730-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="8f730-136">Aby zezwolić aplikacji przeglądarki na wykonywanie wywołań sieci Web między źródłami gRPC, skonfiguruj mechanizm [CORS w ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="8f730-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="8f730-137">Użyj wbudowanej obsługi mechanizmu CORS i Uwidocznij nagłówki specyficzne dla gRPC za pomocą <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="8f730-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="8f730-138">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8f730-138">The preceding code:</span></span>

* <span data-ttu-id="8f730-139">Wywołania `AddCors` dodawania usług CORS i konfigurowania zasad CORS, które ujawniają nagłówki specyficzne dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="8f730-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="8f730-140">Wywołuje `UseCors` Dodawanie oprogramowania CORS po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="8f730-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="8f730-141">Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje mechanizm CORS z `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="8f730-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="8f730-142">Wywołaj gRPC-Web z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="8f730-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="8f730-143">Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="8f730-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="8f730-144">Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="8f730-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="8f730-145">Serwer musi być skonfigurowany do obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8f730-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="8f730-146">Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8f730-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="8f730-147">Przesyłanie strumieniowe serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8f730-147">Server streaming is supported.</span></span>
* <span data-ttu-id="8f730-148">Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="8f730-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="8f730-149">JavaScript gRPC — klient sieci Web</span><span class="sxs-lookup"><span data-stu-id="8f730-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="8f730-150">Istnieje skrypt JavaScript gRPC-Web Client.</span><span class="sxs-lookup"><span data-stu-id="8f730-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="8f730-151">Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="8f730-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="8f730-152">Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="8f730-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="8f730-153">Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8f730-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="8f730-154">Jest to przydatne w przypadku aplikacji [ Blazor webassembly](xref:blazor/index#blazor-webassembly) , które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8f730-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="8f730-155">Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="8f730-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="8f730-156">Jedyną modyfikacją jest sposób tworzenia kanału.</span><span class="sxs-lookup"><span data-stu-id="8f730-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="8f730-157">Aby użyć gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="8f730-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="8f730-158">Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="8f730-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="8f730-159">Upewnij się, że odwołanie do [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) Package ma wartość 2.29.0 lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="8f730-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="8f730-160">Skonfiguruj kanał, aby używał `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="8f730-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="8f730-161">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8f730-161">The preceding code:</span></span>

* <span data-ttu-id="8f730-162">Konfiguruje kanał do korzystania z gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8f730-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="8f730-163">Tworzy klienta i wywołuje połączenie przy użyciu kanału.</span><span class="sxs-lookup"><span data-stu-id="8f730-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="8f730-164">`GrpcWebHandler`dostępne są następujące opcje konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f730-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="8f730-165">**InnerHandler**: podstawowy, <xref:System.Net.Http.HttpMessageHandler> który wysyła żądanie HTTP gRPC, na przykład `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="8f730-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="8f730-166">**GrpcWebMode**: typ wyliczeniowy, który określa, czy żądanie HTTP gRPC `Content-Type` ma wartość `application/grpc-web` lub `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="8f730-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="8f730-167">`GrpcWebMode.GrpcWeb`konfiguruje zawartość do wysłania bez kodowania.</span><span class="sxs-lookup"><span data-stu-id="8f730-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="8f730-168">Wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="8f730-168">Default value.</span></span>
    * <span data-ttu-id="8f730-169">`GrpcWebMode.GrpcWebText`konfiguruje zawartość do kodowania base64.</span><span class="sxs-lookup"><span data-stu-id="8f730-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="8f730-170">Wymagane dla wywołań przesyłania strumieniowego serwera w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="8f730-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="8f730-171">**HttpVersion**: protokół http `Version` służący do ustawiania [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) w źródłowym żądaniu HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="8f730-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="8f730-172">gRPC — sieć Web nie wymaga określonej wersji i nie przesłania jej domyślnie, chyba że zostanie określona.</span><span class="sxs-lookup"><span data-stu-id="8f730-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8f730-173">Wygenerowane gRPC klienci mają metody synchronizacji i asynchroniczne do wywoływania metod jednoargumentowych.</span><span class="sxs-lookup"><span data-stu-id="8f730-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="8f730-174">Na przykład `SayHello` jest synchronizowana i `SayHelloAsync` jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="8f730-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="8f730-175">Wywołanie metody synchronizacji w Blazor aplikacji webassembly spowoduje, że aplikacja przestanie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="8f730-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="8f730-176">Metody asynchroniczne muszą być zawsze używane w Blazor zestawie webassembly.</span><span class="sxs-lookup"><span data-stu-id="8f730-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f730-177">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8f730-177">Additional resources</span></span>

* [<span data-ttu-id="8f730-178">gRPC dla klientów sieci Web — projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="8f730-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
