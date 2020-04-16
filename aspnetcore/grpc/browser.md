---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby były wywoływane z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440769"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="fbcbe-103">Używanie gRPC w aplikacjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="fbcbe-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="fbcbe-104">Przez [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fbcbe-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fbcbe-105">**gRPC-Web wsparcie w .NET jest eksperymentalne**</span><span class="sxs-lookup"><span data-stu-id="fbcbe-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="fbcbe-106">gRPC-Web dla .NET jest projektem eksperymentalnym, a nie produktem zatwierdzonym.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="fbcbe-107">Chcemy:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-107">We want to:</span></span>
>
> * <span data-ttu-id="fbcbe-108">Sprawdź, czy nasze podejście do wdrażania gRPC-Web działa.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="fbcbe-109">Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET w porównaniu z tradycyjnym sposobem konfigurowania gRPC-Web za pośrednictwem serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="fbcbe-110">Prosimy o [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) wysunienie opinii, aby upewnić się, że budujemy coś, co deweloperzy lubią i są produktywni.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="fbcbe-111">Nie można wywołać usługi HTTP/2 gRPC z aplikacji opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="fbcbe-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który pozwala przeglądarkom JavaScript i aplikacjom Blazor dzwonić do usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="fbcbe-113">W tym artykule wyjaśniono, jak używać gRPC-Web w .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="fbcbe-114">gRPC-Web w ASP.NET Core vs. Wysłannik</span><span class="sxs-lookup"><span data-stu-id="fbcbe-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="fbcbe-115">Istnieją dwie możliwości dodawania gRPC-Web do aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="fbcbe-116">Obsługa gRPC-Web wraz z gRPC HTTP/2 w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="fbcbe-117">Ta opcja używa oprogramowania `Grpc.AspNetCore.Web` pośredniczącego dostarczonego przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="fbcbe-118">Użyj obsługi gRPC-Web [serwera proxy wysłannika,](https://www.envoyproxy.io/) aby przetłumaczyć gRPC-Web na gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="fbcbe-119">Przetłumaczone połączenie jest następnie przekazywane do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="fbcbe-120">Istnieją plusy i minusy każdego podejścia.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="fbcbe-121">Jeśli używasz już wysłannika jako serwera proxy w środowisku aplikacji, może warto również użyć go do zapewnienia obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="fbcbe-122">Jeśli chcesz proste rozwiązanie dla gRPC-Web, który wymaga `Grpc.AspNetCore.Web` tylko ASP.NET Core, jest dobrym wyborem.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="fbcbe-123">Konfigurowanie gRPC-Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fbcbe-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="fbcbe-124">Usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web obok HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="fbcbe-125">gRPC-Web nie wymaga żadnych zmian w usługach.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="fbcbe-126">Jedyną modyfikacją jest konfiguracja uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="fbcbe-127">Aby włączyć gRPC-Web z usługą ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="fbcbe-128">Dodaj odwołanie do pakietu [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="fbcbe-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="fbcbe-129">Skonfiguruj aplikację do korzystania z `AddGrpcWeb` `UseGrpcWeb` gRPC-Web, dodając i *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="fbcbe-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="fbcbe-130">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-130">The preceding code:</span></span>

* <span data-ttu-id="fbcbe-131">Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb`po routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="fbcbe-132">Określa metodę, która `endpoints.MapGrpcService<GreeterService>()` obsługuje gRPC-Web za pomocą `EnableGrpcWeb`pliku .</span><span class="sxs-lookup"><span data-stu-id="fbcbe-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="fbcbe-133">Alternatywnie należy skonfigurować wszystkie usługi do obsługi `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` gRPC-Web, dodając do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="fbcbe-134">Istnieje znany problem, który powoduje, że gRPC-Web nie powiedzie się, gdy [hostowane przez http.sys](xref:fundamentals/servers/httpsys) w .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="fbcbe-135">Obejście, aby uzyskać gRPC-Web pracy na Http.sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="fbcbe-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="fbcbe-136">gRPC-Web i CORS</span><span class="sxs-lookup"><span data-stu-id="fbcbe-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="fbcbe-137">Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="fbcbe-138">To ograniczenie dotyczy wykonywania połączeń gRPC-Web z aplikacjami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="fbcbe-139">Na przykład aplikacja przeglądarki obsługiwana przez `https://www.contoso.com` jest zablokowana w wywoływaniu `https://services.contoso.com`gRPC-Web usług hostowanych na .</span><span class="sxs-lookup"><span data-stu-id="fbcbe-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="fbcbe-140">Udostępnianie zasobów cross origin (CORS) może służyć do złagodzenia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="fbcbe-141">Aby umożliwić aplikacji przeglądarki wykonywanie połączeń gRPC-Web typu cross-origin, [skonfiguruj CORS w ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="fbcbe-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="fbcbe-142">Użyj wbudowanej obsługi CORS i udostępnij nagłówki specyficzne <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>dla gRPC za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="fbcbe-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="fbcbe-143">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-143">The preceding code:</span></span>

* <span data-ttu-id="fbcbe-144">Wywołania, `AddCors` aby dodać usługi CORS i konfiguruje zasady CORS, który udostępnia gRPC specyficzne nagłówki.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="fbcbe-145">Wywołania, `UseCors` aby dodać oprogramowanie pośredniczące CORS po routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="fbcbe-146">Określa metodę `endpoints.MapGrpcService<GreeterService>()` obsługuje CORS `RequiresCors`z .</span><span class="sxs-lookup"><span data-stu-id="fbcbe-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="fbcbe-147">Zadzwoń do gRPC-Web z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="fbcbe-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="fbcbe-148">Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="fbcbe-149">Istnieją pewne wymagania i ograniczenia podczas wywoływania usług gRPC z gRPC-Web z przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="fbcbe-150">Serwer musi być skonfigurowany do obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="fbcbe-151">Przesyłanie strumieniowe klienta i dwukierunkowe przesyłanie strumieniowe nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="fbcbe-152">Przesyłanie strumieniowe serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-152">Server streaming is supported.</span></span>
* <span data-ttu-id="fbcbe-153">Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [usługi CORS](xref:security/cors) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="fbcbe-154">JavaScript gRPC-Klient sieci Web</span><span class="sxs-lookup"><span data-stu-id="fbcbe-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="fbcbe-155">Istnieje javascript gRPC-web klienta.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="fbcbe-156">Aby uzyskać instrukcje dotyczące korzystania z gRPC-Web z Języka JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="fbcbe-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="fbcbe-157">Konfigurowanie gRPC-Web za pomocą klienta gRPC .NET</span><span class="sxs-lookup"><span data-stu-id="fbcbe-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="fbcbe-158">Klienta gRPC .NET można skonfigurować do wykonywania wywołań gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="fbcbe-159">Jest to przydatne w przypadku aplikacji [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) które są hostowane w przeglądarce i mają takie same ograniczenia HTTP kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="fbcbe-160">Wywołanie gRPC-Web za pomocą klienta .NET jest [takie samo jak HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="fbcbe-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="fbcbe-161">Jedyną modyfikacją jest sposób tworzenia kanału.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="fbcbe-162">Aby użyć gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="fbcbe-163">Dodaj odwołanie do pakietu [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="fbcbe-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="fbcbe-164">Upewnij się, że odwołanie do pakietu [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) jest 2.27.0 lub więcej.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="fbcbe-165">Skonfiguruj kanał `GrpcWebHandler`do używania:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="fbcbe-166">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-166">The preceding code:</span></span>

* <span data-ttu-id="fbcbe-167">Konfiguruje kanał do korzystania z gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="fbcbe-168">Tworzy klienta i nawiązuje połączenie za pomocą kanału.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="fbcbe-169">Ma `GrpcWebHandler` następujące opcje konfiguracji podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="fbcbe-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="fbcbe-170">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Podstawowe, które sprawia, że żądanie `HttpClientHandler`HTTP gRPC, na przykład.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="fbcbe-171">**Tryb**: Typ wyliczenia określający, czy żądanie `Content-Type` `application/grpc-web` HTTP `application/grpc-web-text`gRPC jest czy .</span><span class="sxs-lookup"><span data-stu-id="fbcbe-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="fbcbe-172">`GrpcWebMode.GrpcWeb`konfiguruje zawartość, która ma być wysyłana bez kodowania.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="fbcbe-173">Wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-173">Default value.</span></span>
    * <span data-ttu-id="fbcbe-174">`GrpcWebMode.GrpcWebText`konfiguruje zawartość do zakodowania base64.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="fbcbe-175">Wymagane do przesyłania strumieniowego przez serwer połączeń w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="fbcbe-176">**HttpVersion**: `Version` Protokół HTTP używany do [ustawiania HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) na podstawowe żądanie HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="fbcbe-177">gRPC-Web nie wymaga określonej wersji i nie zastępuje wartości domyślnej, chyba że określono.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fbcbe-178">Wygenerowane gRPC klienci mają synchronizacji i asynchronicznych metod wywoływania metod arysowych.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="fbcbe-179">Na przykład `SayHello` jest `SayHelloAsync` synchronizowana i jest asynchronizowana.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="fbcbe-180">Wywołanie metody synchronizacji w aplikacji Blazor WebAssembly spowoduje, że aplikacja przestanie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="fbcbe-181">Metody asynchronizowe muszą być zawsze używane w Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="fbcbe-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbcbe-182">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="fbcbe-182">Additional resources</span></span>

* [<span data-ttu-id="fbcbe-183">gRPC dla klientów sieci Web Projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="fbcbe-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
