---
title: Używanie protokołu HTTP/2 z serwerem sieci Web ASP.NET Core Kestrel
author: rick-anderson
description: Dowiedz się więcej na temat używania protokołu HTTP/2 z Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253983"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="31098-103">Używanie protokołu HTTP/2 z serwerem sieci Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="31098-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="31098-104">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="31098-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="31098-105">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="31098-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="31098-106">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="31098-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="31098-107">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="31098-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="31098-108">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="31098-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="31098-109">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="31098-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="31098-110">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="31098-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="31098-111">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="31098-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="31098-112">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="31098-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="31098-113">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="31098-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="31098-114">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="31098-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="31098-115">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="31098-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="31098-116">Począwszy od platformy .NET Core 3,0, protokół HTTP/2 jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="31098-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="31098-117">Aby uzyskać więcej informacji na temat konfiguracji, zobacz sekcję [limity protokołu HTTP/2 Kestrel](xref:fundamentals/servers/kestrel/options#http2-limits) i [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="31098-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="31098-118">Zaawansowane funkcje protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="31098-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="31098-119">Dodatkowe funkcje protokołu HTTP/2 w programie Kestrel obsługują gRPC, w tym obsługę przyczep z odpowiedziami i wysyłanie ramek resetowania.</span><span class="sxs-lookup"><span data-stu-id="31098-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="31098-120">Przyczep</span><span class="sxs-lookup"><span data-stu-id="31098-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="31098-121">Reset</span><span class="sxs-lookup"><span data-stu-id="31098-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
