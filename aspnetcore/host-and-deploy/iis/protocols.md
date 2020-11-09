---
title: Używanie ASP.NET Core z protokołem HTTP/2 w usługach IIS
author: rick-anderson
description: Dowiedz się, jak używać funkcji protokołu HTTP/2 z usługami IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057417"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="66c98-103">Używanie ASP.NET Core z protokołem HTTP/2 w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="66c98-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="66c98-104">Autor [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="66c98-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="66c98-105">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania usług IIS:</span><span class="sxs-lookup"><span data-stu-id="66c98-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="66c98-106">System Windows Server 2016 lub nowszy/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="66c98-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="66c98-107">Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="66c98-107">IIS 10 or later</span></span>
* <span data-ttu-id="66c98-108">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="66c98-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="66c98-109">Podczas [hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model): połączenia z serwerem Edge dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="66c98-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="66c98-110">W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="66c98-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="66c98-111">W przypadku wdrożenia poza procesem w przypadku ustanowienia połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="66c98-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="66c98-112">Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="66c98-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="66c98-113">Protokół HTTP/2 jest domyślnie włączony dla połączeń HTTPS/TLS.</span><span class="sxs-lookup"><span data-stu-id="66c98-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="66c98-114">Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="66c98-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="66c98-115">Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="66c98-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="66c98-116">Zaawansowane funkcje protokołu HTTP/2 do obsługi gRPC</span><span class="sxs-lookup"><span data-stu-id="66c98-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="66c98-117">Dodatkowe funkcje protokołu HTTP/2 w usługach IIS obsługują gRPC, w tym obsługę przyczep z odpowiedziami i wysyłanie ramek resetowania.</span><span class="sxs-lookup"><span data-stu-id="66c98-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="66c98-118">Wymagania dotyczące uruchamiania gRPC w usługach IIS:</span><span class="sxs-lookup"><span data-stu-id="66c98-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="66c98-119">Hosting w procesie.</span><span class="sxs-lookup"><span data-stu-id="66c98-119">In-process hosting.</span></span>
* <span data-ttu-id="66c98-120">Windows 10, kompilacja systemu operacyjnego 20300,1000 lub nowsza.</span><span class="sxs-lookup"><span data-stu-id="66c98-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="66c98-121">Może wymagać użycia kompilacji niejawnego programu testów systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="66c98-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="66c98-122">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="66c98-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="66c98-123">Przyczep</span><span class="sxs-lookup"><span data-stu-id="66c98-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="66c98-124">Reset</span><span class="sxs-lookup"><span data-stu-id="66c98-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
