---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby możliwe było wywoływanie z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774746"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="6f2c6-103">Używanie gRPC w aplikacjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="6f2c6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="6f2c6-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6f2c6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6f2c6-105">**gRPC — obsługa sieci Web w programie .NET jest eksperymentalna**</span><span class="sxs-lookup"><span data-stu-id="6f2c6-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="6f2c6-106">gRPC-Web for .NET to eksperymentalny projekt, a nie produkt zatwierdzony.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="6f2c6-107">Chcemy:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-107">We want to:</span></span>
>
> * <span data-ttu-id="6f2c6-108">Przetestuj, że nasze podejście do implementacji gRPC-Web działa.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="6f2c6-109">Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET w porównaniu do tradycyjnego sposobu konfigurowania gRPC-sieci Web za pośrednictwem serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="6f2c6-110">Prosimy o przesłanie [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) opinii na temat, aby upewnić się, że deweloperzy lubią i wydajni.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="6f2c6-111">Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="6f2c6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który umożliwia przeglądarce JavaScript i Blazor aplikacji Wywoływanie usługi gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="6f2c6-113">W tym artykule wyjaśniono, jak używać gRPC-Web w programie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="6f2c6-114">gRPC — sieć Web w ASP.NET Core a wysłannika</span><span class="sxs-lookup"><span data-stu-id="6f2c6-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="6f2c6-115">Dostępne są dwa sposoby dodawania usługi gRPC-Web do aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="6f2c6-116">Obsługa gRPC-Web i gRPC protokołu HTTP/2 w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="6f2c6-117">Ta opcja powoduje użycie oprogramowania pośredniczącego dostarczonego przez `Grpc.AspNetCore.Web` pakiet.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="6f2c6-118">Użyj funkcji gRPC-Web [wysłannika serwera proxy](https://www.envoyproxy.io/) , aby przetłumaczyć GRPC-Web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="6f2c6-119">Przetłumaczone wywołanie jest następnie przekazywane do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="6f2c6-120">Każde podejście ma pewne wady i zalety.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="6f2c6-121">Jeśli używasz już wysłannika jako serwera proxy w środowisku aplikacji, warto również użyć go do zapewnienia obsługi gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="6f2c6-122">Jeśli potrzebujesz prostego rozwiązania dla gRPC-Web, które wymaga ASP.NET Core, `Grpc.AspNetCore.Web` to dobry wybór.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="6f2c6-123">Konfigurowanie gRPC-sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f2c6-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="6f2c6-124">usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="6f2c6-125">gRPC — sieć Web nie wymaga żadnych zmian w usługach.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="6f2c6-126">Jedyną modyfikacją jest konfiguracja uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="6f2c6-127">Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="6f2c6-128">Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="6f2c6-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="6f2c6-129">Skonfiguruj aplikację do używania gRPC-Web, dodając `AddGrpcWeb` i `UseGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="6f2c6-130">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-130">The preceding code:</span></span>

* <span data-ttu-id="6f2c6-131">Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb`po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="6f2c6-132">Określa, `endpoints.MapGrpcService<GreeterService>()` że metoda obsługuje GRPC-Web `EnableGrpcWeb`with.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="6f2c6-133">Alternatywnie można skonfigurować wszystkie usługi do obsługi gRPC-Web, dodając `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="6f2c6-134">Występuje znany problem, który powoduje niepowodzenie gRPC-Web w przypadku [hostowania przez http. sys](xref:fundamentals/servers/httpsys) w programie .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="6f2c6-135">Obejście problemu z gRPC-Web Working for http. sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="6f2c6-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="6f2c6-136">gRPC — Web i CORS</span><span class="sxs-lookup"><span data-stu-id="6f2c6-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="6f2c6-137">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6f2c6-138">To ograniczenie dotyczy tworzenia połączeń sieci Web gRPC z aplikacjami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="6f2c6-139">Na przykład aplikacja przeglądarki obsługiwana przez `https://www.contoso.com` program ma zablokowany dostęp do usług GRPC — sieci Web hostowanych `https://services.contoso.com`w systemie.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="6f2c6-140">Aby osłabić to ograniczenie, można użyć funkcji udostępniania zasobów między źródłami (CORS).</span><span class="sxs-lookup"><span data-stu-id="6f2c6-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="6f2c6-141">Aby zezwolić aplikacji przeglądarki na wykonywanie wywołań sieci Web między źródłami gRPC, skonfiguruj mechanizm [CORS w ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="6f2c6-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="6f2c6-142">Użyj wbudowanej obsługi mechanizmu CORS i Uwidocznij nagłówki specyficzne dla gRPC za pomocą <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="6f2c6-143">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-143">The preceding code:</span></span>

* <span data-ttu-id="6f2c6-144">Wywołania `AddCors` dodawania usług CORS i KONFIGUROWANIA zasad CORS, które ujawniają nagłówki specyficzne dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="6f2c6-145">Wywołuje `UseCors` Dodawanie oprogramowania CORS po stronie routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="6f2c6-146">Określa, `endpoints.MapGrpcService<GreeterService>()` że metoda obsługuje mechanizm `RequiresCors`CORS z.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="6f2c6-147">Wywołaj gRPC-Web z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="6f2c6-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="6f2c6-148">Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="6f2c6-149">Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="6f2c6-150">Serwer musi być skonfigurowany do obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="6f2c6-151">Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="6f2c6-152">Przesyłanie strumieniowe serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-152">Server streaming is supported.</span></span>
* <span data-ttu-id="6f2c6-153">Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="6f2c6-154">JavaScript gRPC — klient sieci Web</span><span class="sxs-lookup"><span data-stu-id="6f2c6-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="6f2c6-155">Istnieje skrypt JavaScript gRPC-Web Client.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="6f2c6-156">Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="6f2c6-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="6f2c6-157">Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="6f2c6-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="6f2c6-158">Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="6f2c6-159">Jest to przydatne w przypadku [ Blazor aplikacji webassembly](xref:blazor/index#blazor-webassembly) , które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="6f2c6-160">Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="6f2c6-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="6f2c6-161">Jedyną modyfikacją jest sposób tworzenia kanału.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="6f2c6-162">Aby użyć gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="6f2c6-163">Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="6f2c6-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="6f2c6-164">Upewnij się, że odwołanie do [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) Package ma wartość 2.27.0 lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="6f2c6-165">Skonfiguruj kanał, `GrpcWebHandler`aby używał:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="6f2c6-166">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-166">The preceding code:</span></span>

* <span data-ttu-id="6f2c6-167">Konfiguruje kanał do korzystania z gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="6f2c6-168">Tworzy klienta i wywołuje połączenie przy użyciu kanału.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="6f2c6-169">Podczas `GrpcWebHandler` tworzenia są dostępne następujące opcje konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="6f2c6-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="6f2c6-170">**InnerHandler**: podstawowy <xref:System.Net.Http.HttpMessageHandler> , który wysyła żądanie HTTP gRPC, na przykład `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="6f2c6-171">**Tryb**: typ wyliczeniowy, który określa, czy żądanie `Content-Type` http gRPC ma `application/grpc-web` wartość `application/grpc-web-text`lub.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="6f2c6-172">`GrpcWebMode.GrpcWeb`konfiguruje zawartość do wysłania bez kodowania.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="6f2c6-173">Wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-173">Default value.</span></span>
    * <span data-ttu-id="6f2c6-174">`GrpcWebMode.GrpcWebText`konfiguruje zawartość do kodowania base64.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="6f2c6-175">Wymagane dla wywołań przesyłania strumieniowego serwera w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="6f2c6-176">**HttpVersion**: protokół `Version` http służący do ustawiania [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) w źródłowym żądaniu HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="6f2c6-177">gRPC — sieć Web nie wymaga określonej wersji i nie przesłania jej domyślnie, chyba że zostanie określona.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6f2c6-178">Wygenerowane gRPC klienci mają metody synchronizacji i asynchroniczne do wywoływania metod jednoargumentowych.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="6f2c6-179">Na przykład `SayHello` jest synchronizowana i `SayHelloAsync` jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="6f2c6-180">Wywołanie metody synchronizacji w Blazor aplikacji webassembly spowoduje, że aplikacja przestanie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="6f2c6-181">Metody asynchroniczne muszą być zawsze używane w Blazor zestawie webassembly.</span><span class="sxs-lookup"><span data-stu-id="6f2c6-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f2c6-182">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6f2c6-182">Additional resources</span></span>

* [<span data-ttu-id="6f2c6-183">gRPC dla klientów sieci Web — projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="6f2c6-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
