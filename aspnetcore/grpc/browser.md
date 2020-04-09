---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby były wywoływane z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977148"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="08f79-103">Używanie gRPC w aplikacjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="08f79-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="08f79-104">Przez [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="08f79-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="08f79-105">**gRPC-Web wsparcie w .NET jest eksperymentalne**</span><span class="sxs-lookup"><span data-stu-id="08f79-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="08f79-106">gRPC-Web dla .NET jest projektem eksperymentalnym, a nie produktem zatwierdzonym.</span><span class="sxs-lookup"><span data-stu-id="08f79-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="08f79-107">Chcemy:</span><span class="sxs-lookup"><span data-stu-id="08f79-107">We want to:</span></span>
>
> * <span data-ttu-id="08f79-108">Sprawdź, czy nasze podejście do wdrażania gRPC-Web działa.</span><span class="sxs-lookup"><span data-stu-id="08f79-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="08f79-109">Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET w porównaniu z tradycyjnym sposobem konfigurowania gRPC-Web za pośrednictwem serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="08f79-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="08f79-110">Prosimy o [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) wysunienie opinii, aby upewnić się, że budujemy coś, co deweloperzy lubią i są produktywni.</span><span class="sxs-lookup"><span data-stu-id="08f79-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="08f79-111">Nie można wywołać usługi HTTP/2 gRPC z aplikacji opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="08f79-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="08f79-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który pozwala przeglądarkom JavaScript i aplikacjom Blazor dzwonić do usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="08f79-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="08f79-113">W tym artykule wyjaśniono, jak używać gRPC-Web w .NET Core.</span><span class="sxs-lookup"><span data-stu-id="08f79-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="08f79-114">Konfigurowanie gRPC-Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08f79-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="08f79-115">Usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web obok HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="08f79-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="08f79-116">gRPC-Web nie wymaga żadnych zmian w usługach.</span><span class="sxs-lookup"><span data-stu-id="08f79-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="08f79-117">Jedyną modyfikacją jest konfiguracja uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="08f79-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="08f79-118">Aby włączyć gRPC-Web z usługą ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="08f79-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="08f79-119">Dodaj odwołanie do pakietu [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="08f79-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="08f79-120">Skonfiguruj aplikację do korzystania z `AddGrpcWeb` `UseGrpcWeb` gRPC-Web, dodając i *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="08f79-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="08f79-121">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="08f79-121">The preceding code:</span></span>

* <span data-ttu-id="08f79-122">Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb`po routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="08f79-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="08f79-123">Określa metodę, która `endpoints.MapGrpcService<GreeterService>()` obsługuje gRPC-Web za pomocą `EnableGrpcWeb`pliku .</span><span class="sxs-lookup"><span data-stu-id="08f79-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="08f79-124">Alternatywnie należy skonfigurować wszystkie usługi do obsługi `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` gRPC-Web, dodając do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="08f79-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="08f79-125">Istnieje znany problem, który powoduje, że gRPC-Web nie powiedzie się, gdy [hostowane przez http.sys](xref:fundamentals/servers/httpsys) w .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="08f79-125">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="08f79-126">Obejście, aby uzyskać gRPC-Web pracy na Http.sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="08f79-126">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="08f79-127">gRPC-Web i CORS</span><span class="sxs-lookup"><span data-stu-id="08f79-127">gRPC-Web and CORS</span></span>

<span data-ttu-id="08f79-128">Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="08f79-128">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="08f79-129">To ograniczenie dotyczy wykonywania połączeń gRPC-Web z aplikacjami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="08f79-129">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="08f79-130">Na przykład aplikacja przeglądarki obsługiwana przez `https://www.contoso.com` jest zablokowana w wywoływaniu `https://services.contoso.com`gRPC-Web usług hostowanych na .</span><span class="sxs-lookup"><span data-stu-id="08f79-130">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="08f79-131">Udostępnianie zasobów cross origin (CORS) może służyć do złagodzenia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="08f79-131">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="08f79-132">Aby umożliwić aplikacji przeglądarki wykonywanie połączeń gRPC-Web typu cross-origin, [skonfiguruj CORS w ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="08f79-132">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="08f79-133">Użyj wbudowanej obsługi CORS i udostępnij nagłówki specyficzne <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>dla gRPC za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="08f79-133">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="08f79-134">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="08f79-134">The preceding code:</span></span>

* <span data-ttu-id="08f79-135">Wywołania, `AddCors` aby dodać usługi CORS i konfiguruje zasady CORS, który udostępnia gRPC specyficzne nagłówki.</span><span class="sxs-lookup"><span data-stu-id="08f79-135">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="08f79-136">Wywołania, `UseCors` aby dodać oprogramowanie pośredniczące CORS po routingu i przed punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="08f79-136">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="08f79-137">Określa metodę `endpoints.MapGrpcService<GreeterService>()` obsługuje CORS `RequiresCors`z .</span><span class="sxs-lookup"><span data-stu-id="08f79-137">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="08f79-138">Zadzwoń do gRPC-Web z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="08f79-138">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="08f79-139">Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="08f79-139">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="08f79-140">Istnieją pewne wymagania i ograniczenia podczas wywoływania usług gRPC z gRPC-Web z przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="08f79-140">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="08f79-141">Serwer musi być skonfigurowany do obsługi gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="08f79-141">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="08f79-142">Przesyłanie strumieniowe klienta i dwukierunkowe przesyłanie strumieniowe nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="08f79-142">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="08f79-143">Przesyłanie strumieniowe serwera jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="08f79-143">Server streaming is supported.</span></span>
* <span data-ttu-id="08f79-144">Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [usługi CORS](xref:security/cors) na serwerze.</span><span class="sxs-lookup"><span data-stu-id="08f79-144">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="08f79-145">JavaScript gRPC-Klient sieci Web</span><span class="sxs-lookup"><span data-stu-id="08f79-145">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="08f79-146">Istnieje javascript gRPC-web klienta.</span><span class="sxs-lookup"><span data-stu-id="08f79-146">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="08f79-147">Aby uzyskać instrukcje dotyczące korzystania z gRPC-Web z Języka JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="08f79-147">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="08f79-148">Konfigurowanie gRPC-Web za pomocą klienta gRPC .NET</span><span class="sxs-lookup"><span data-stu-id="08f79-148">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="08f79-149">Klienta gRPC .NET można skonfigurować do wykonywania wywołań gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="08f79-149">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="08f79-150">Jest to przydatne w przypadku aplikacji [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) które są hostowane w przeglądarce i mają takie same ograniczenia HTTP kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="08f79-150">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="08f79-151">Wywołanie gRPC-Web za pomocą klienta .NET jest [takie samo jak HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="08f79-151">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="08f79-152">Jedyną modyfikacją jest sposób tworzenia kanału.</span><span class="sxs-lookup"><span data-stu-id="08f79-152">The only modification is how the channel is created.</span></span>

<span data-ttu-id="08f79-153">Aby użyć gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="08f79-153">To use gRPC-Web:</span></span>

* <span data-ttu-id="08f79-154">Dodaj odwołanie do pakietu [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="08f79-154">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="08f79-155">Upewnij się, że odwołanie do pakietu [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) jest 2.27.0 lub więcej.</span><span class="sxs-lookup"><span data-stu-id="08f79-155">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="08f79-156">Skonfiguruj kanał `GrpcWebHandler`do używania:</span><span class="sxs-lookup"><span data-stu-id="08f79-156">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="08f79-157">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="08f79-157">The preceding code:</span></span>

* <span data-ttu-id="08f79-158">Konfiguruje kanał do korzystania z gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="08f79-158">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="08f79-159">Tworzy klienta i nawiązuje połączenie za pomocą kanału.</span><span class="sxs-lookup"><span data-stu-id="08f79-159">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="08f79-160">Ma `GrpcWebHandler` następujące opcje konfiguracji podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="08f79-160">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="08f79-161">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Podstawowe, które sprawia, że żądanie `HttpClientHandler`HTTP gRPC, na przykład.</span><span class="sxs-lookup"><span data-stu-id="08f79-161">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="08f79-162">**Tryb**: Typ wyliczenia określający, czy żądanie `Content-Type` http `application/grpc-web` `application/grpc-web-text`gRPC jest czy .</span><span class="sxs-lookup"><span data-stu-id="08f79-162">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="08f79-163">`GrpcWebMode.GrpcWeb`konfiguruje zawartość, która ma być wysyłana bez kodowania.</span><span class="sxs-lookup"><span data-stu-id="08f79-163">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="08f79-164">Wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="08f79-164">Default value.</span></span>
    * <span data-ttu-id="08f79-165">`GrpcWebMode.GrpcWebText`konfiguruje zawartość do zakodowania base64.</span><span class="sxs-lookup"><span data-stu-id="08f79-165">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="08f79-166">Wymagane do przesyłania strumieniowego przez serwer połączeń w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="08f79-166">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="08f79-167">**HttpVersion**: `Version` Protokół HTTP używany do [ustawiania HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) na podstawowe żądanie HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="08f79-167">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="08f79-168">gRPC-Web nie wymaga określonej wersji i nie zastępuje wartości domyślnej, chyba że określono.</span><span class="sxs-lookup"><span data-stu-id="08f79-168">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="08f79-169">Wygenerowane gRPC klienci mają synchronizacji i asynchronicznych metod wywoływania metod arysowych.</span><span class="sxs-lookup"><span data-stu-id="08f79-169">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="08f79-170">Na przykład `SayHello` jest `SayHelloAsync` synchronizowana i jest asynchronizowana.</span><span class="sxs-lookup"><span data-stu-id="08f79-170">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="08f79-171">Wywołanie metody synchronizacji w aplikacji Blazor WebAssembly spowoduje, że aplikacja przestanie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="08f79-171">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="08f79-172">Metody asynchronizowe muszą być zawsze używane w Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="08f79-172">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08f79-173">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="08f79-173">Additional resources</span></span>

* [<span data-ttu-id="08f79-174">gRPC dla klientów sieci Web Projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="08f79-174">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
