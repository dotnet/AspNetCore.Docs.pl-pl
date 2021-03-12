---
title: Implementacja serwera sieci Web Kestrel w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 54d7304430c2a77347b6e3279f638cf6d7add77e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589049"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="9385b-103">Implementacja serwera sieci Web Kestrel w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9385b-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="9385b-104">Autorzy [Dykstra](https://github.com/tdykstra), [Krzysztof Ross](https://github.com/Tratcher)i [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="9385b-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9385b-105">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="9385b-106">Kestrel to serwer sieci Web, który jest domyślnie dostępny i włączony w ASP.NET Core szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="9385b-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="9385b-107">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="9385b-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="9385b-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9385b-108">HTTPS</span></span>
* <span data-ttu-id="9385b-109">[Http/2](xref:fundamentals/servers/kestrel/http2) (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="9385b-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="9385b-110">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](xref:fundamentals/websockets)</span><span class="sxs-lookup"><span data-stu-id="9385b-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="9385b-111">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="9385b-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="9385b-112">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="9385b-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="9385b-113">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="9385b-114">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9385b-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="9385b-115">Rozpoczęcie pracy</span><span class="sxs-lookup"><span data-stu-id="9385b-115">Get started</span></span>

<span data-ttu-id="9385b-116">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="9385b-117">W *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> Metoda wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="9385b-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="9385b-118">Więcej informacji na temat tworzenia hosta znajduje się w sekcji *Konfigurowanie ustawień hosta* i *domyślnego konstruktora* w temacie <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="9385b-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9385b-119">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9385b-119">Additional resources</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="9385b-120">RFC 7230: Składnia i Routing komunikatu (sekcja 5,4: Host)</span><span class="sxs-lookup"><span data-stu-id="9385b-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="9385b-121">W przypadku korzystania z gniazd systemu UNIX w systemie Linux gniazdo nie jest automatycznie usuwane podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="9385b-122">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/14134)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="9385b-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="9385b-123">Począwszy od ASP.NET Core 5,0, transport libuv Kestrel jest przestarzały.</span><span class="sxs-lookup"><span data-stu-id="9385b-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="9385b-124">Transport libuv nie odbiera aktualizacji do obsługi nowych platform systemu operacyjnego, takich jak Windows ARM64 i zostanie usunięty w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="9385b-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="9385b-125">Usuń wszystkie wywołania metody przestarzałej <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> i zamiast tego użyj domyślnego transportu gniazda Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9385b-126">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="9385b-127">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="9385b-128">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="9385b-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="9385b-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9385b-129">HTTPS</span></span>
* <span data-ttu-id="9385b-130">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="9385b-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="9385b-131">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="9385b-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="9385b-132">HTTP/2 (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="9385b-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="9385b-133">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="9385b-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="9385b-134">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="9385b-135">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9385b-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="9385b-136">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9385b-136">HTTP/2 support</span></span>

<span data-ttu-id="9385b-137">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="9385b-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="9385b-138">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="9385b-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="9385b-139">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="9385b-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="9385b-140">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="9385b-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="9385b-141">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9385b-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="9385b-142">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="9385b-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="9385b-143">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="9385b-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="9385b-144">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="9385b-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="9385b-145">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="9385b-146">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="9385b-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="9385b-147">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="9385b-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="9385b-148">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="9385b-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="9385b-149">Począwszy od platformy .NET Core 3,0, protokół HTTP/2 jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="9385b-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="9385b-150">Więcej informacji na temat konfiguracji można znaleźć w sekcjach [Kestrel Options](#kestrel-options) and [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="9385b-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="9385b-151">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="9385b-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="9385b-152">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="9385b-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="9385b-153">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="9385b-154">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="9385b-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="9385b-156">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="9385b-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="9385b-158">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="9385b-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="9385b-159">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="9385b-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="9385b-160">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="9385b-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="9385b-161">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="9385b-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="9385b-162">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="9385b-163">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="9385b-163">A reverse proxy:</span></span>

* <span data-ttu-id="9385b-164">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="9385b-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="9385b-165">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="9385b-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="9385b-166">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="9385b-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="9385b-167">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9385b-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="9385b-168">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9385b-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="9385b-169">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9385b-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="9385b-170">Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9385b-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="9385b-171">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="9385b-172">W *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> Metoda wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="9385b-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="9385b-173">Więcej informacji na temat tworzenia hosta znajduje się w sekcji *Konfigurowanie ustawień hosta* i *domyślnego konstruktora* w temacie <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="9385b-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="9385b-174">Aby zapewnić dodatkową konfigurację po wywołaniu `ConfigureWebHostDefaults` , użyj `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="9385b-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="9385b-175">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="9385b-175">Kestrel options</span></span>

<span data-ttu-id="9385b-176">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="9385b-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="9385b-177">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="9385b-178">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="9385b-179">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="9385b-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="9385b-180">W przykładach przedstawionych w dalszej części tego artykułu opcje Kestrel są konfigurowane w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="9385b-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="9385b-181">Opcje Kestrel można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="9385b-182">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację Kestrel z *appsettings.json* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="9385b-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="9385b-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfigurację punktu końcowego](#endpoint-configuration) można konfigurować z poziomu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="9385b-184">Pozostała konfiguracja Kestrel musi być skonfigurowana w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="9385b-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="9385b-185">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9385b-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="9385b-186">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9385b-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="9385b-187">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="9385b-188">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="9385b-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="9385b-189">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="9385b-190">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="9385b-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="9385b-191">W programie *program.cs* Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="9385b-192">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="9385b-193">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="9385b-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="9385b-194">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="9385b-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="9385b-195">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="9385b-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="9385b-196">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="9385b-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="9385b-197">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="9385b-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="9385b-198">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="9385b-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="9385b-199">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="9385b-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="9385b-200">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="9385b-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="9385b-201">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="9385b-202">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9385b-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="9385b-203">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="9385b-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="9385b-204">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="9385b-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="9385b-205">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="9385b-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="9385b-206">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="9385b-207">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="9385b-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="9385b-208">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="9385b-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="9385b-209">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="9385b-210">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="9385b-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="9385b-211">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="9385b-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="9385b-212">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="9385b-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="9385b-213">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="9385b-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="9385b-214">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="9385b-215">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="9385b-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="9385b-216">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9385b-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="9385b-217">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="9385b-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="9385b-218"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odwołania w poprzednim przykładzie nie występują w `HttpContext.Features` żądaniach HTTP/2, ponieważ Modyfikowanie limitów szybkości dla poszczególnych żądań jest ogólnie nieobsługiwane w przypadku protokołu HTTP/2 z powodu obsługi żądania multipleksowania żądań.</span><span class="sxs-lookup"><span data-stu-id="9385b-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="9385b-219">Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> nadal występuje `HttpContext.Features` dla żądań HTTP/2, ponieważ limit liczby odczytów nadal można *wyłączyć całkowicie* dla każdego żądania przez ustawienie `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` nawet dla żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="9385b-220">Próba odczytania `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby ustawienia jej na wartość inną niż `null` spowoduje `NotSupportedException` zgłoszenie żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="9385b-221">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="9385b-222">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="9385b-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="9385b-223">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="9385b-224">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="9385b-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="9385b-225">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="9385b-225">Maximum streams per connection</span></span>

<span data-ttu-id="9385b-226">`Http2.MaxStreamsPerConnection` ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="9385b-227">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="9385b-228">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="9385b-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="9385b-229">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="9385b-229">Header table size</span></span>

<span data-ttu-id="9385b-230">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="9385b-231">`Http2.HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="9385b-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="9385b-232">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="9385b-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="9385b-233">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="9385b-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="9385b-234">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="9385b-234">Maximum frame size</span></span>

<span data-ttu-id="9385b-235">`Http2.MaxFrameSize` wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer.</span><span class="sxs-lookup"><span data-stu-id="9385b-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="9385b-236">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="9385b-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="9385b-237">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="9385b-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="9385b-238">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-238">Maximum request header size</span></span>

<span data-ttu-id="9385b-239">`Http2.MaxRequestHeaderFieldSize` wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="9385b-240">Ten limit dotyczy zarówno nazwy, jak i wartości w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="9385b-241">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="9385b-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="9385b-242">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="9385b-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="9385b-243">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="9385b-243">Initial connection window size</span></span>

<span data-ttu-id="9385b-244">`Http2.InitialConnectionWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="9385b-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="9385b-245">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="9385b-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="9385b-246">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="9385b-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="9385b-247">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="9385b-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="9385b-248">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="9385b-248">Initial stream window size</span></span>

<span data-ttu-id="9385b-249">`Http2.InitialStreamWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="9385b-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="9385b-250">Żądania są również ograniczone przez `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="9385b-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="9385b-251">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="9385b-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="9385b-252">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="9385b-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="9385b-253">Przyczep</span><span class="sxs-lookup"><span data-stu-id="9385b-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="9385b-254">Reset</span><span class="sxs-lookup"><span data-stu-id="9385b-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="9385b-255">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="9385b-255">Synchronous I/O</span></span>

<span data-ttu-id="9385b-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="9385b-257">Wartość domyślna to `false`.</span><span class="sxs-lookup"><span data-stu-id="9385b-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="9385b-258">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="9385b-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="9385b-259">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="9385b-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="9385b-260">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="9385b-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="9385b-261">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="9385b-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="9385b-262">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="9385b-262">Endpoint configuration</span></span>

<span data-ttu-id="9385b-263">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="9385b-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="9385b-264">`https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="9385b-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="9385b-265">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="9385b-265">Specify URLs using the:</span></span>

* <span data-ttu-id="9385b-266">`ASPNETCORE_URLS` Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="9385b-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="9385b-267">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="9385b-268">`urls` klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="9385b-269">`UseUrls` Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="9385b-270">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="9385b-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="9385b-271">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="9385b-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="9385b-272">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="9385b-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="9385b-273">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="9385b-273">A development certificate is created:</span></span>

* <span data-ttu-id="9385b-274">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="9385b-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="9385b-275">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="9385b-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="9385b-276">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="9385b-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="9385b-277">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="9385b-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9385b-278">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="9385b-279">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9385b-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="9385b-280">`KestrelServerOptions` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="9385b-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="9385b-281">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="9385b-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="9385b-282">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9385b-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="9385b-283">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="9385b-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="9385b-284">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="9385b-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="9385b-285">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="9385b-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="9385b-286">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="9385b-287">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="9385b-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="9385b-288">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="9385b-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="9385b-289">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="9385b-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="9385b-290">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="9385b-291">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="9385b-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="9385b-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="9385b-293">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="9385b-294">`ListenOptions.UseHttps` rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="9385b-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="9385b-295">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="9385b-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="9385b-296">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="9385b-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="9385b-297">`ListenOptions.UseHttps` wejściowe</span><span class="sxs-lookup"><span data-stu-id="9385b-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="9385b-298">`filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="9385b-299">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="9385b-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="9385b-300">`configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="9385b-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="9385b-301">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="9385b-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="9385b-302">`storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="9385b-303">`subject` to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9385b-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="9385b-304">`allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="9385b-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="9385b-305">`location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="9385b-306">`serverCertificate` jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="9385b-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="9385b-307">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9385b-308">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="9385b-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="9385b-309">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="9385b-309">Supported configurations described next:</span></span>

* <span data-ttu-id="9385b-310">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9385b-310">No configuration</span></span>
* <span data-ttu-id="9385b-311">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9385b-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="9385b-312">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="9385b-312">Change the defaults in code</span></span>

<span data-ttu-id="9385b-313">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-313">*No configuration*</span></span>

<span data-ttu-id="9385b-314">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="9385b-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="9385b-315">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="9385b-316">`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="9385b-317">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="9385b-318">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="9385b-319">W poniższym *appsettings.json* przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9385b-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="9385b-320">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="9385b-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="9385b-321">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="9385b-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="9385b-322">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="9385b-323">Certyfikat domyślny można na **przykład**  >   określić jako:</span><span class="sxs-lookup"><span data-stu-id="9385b-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="9385b-324">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="9385b-324">Schema notes:</span></span>

* <span data-ttu-id="9385b-325">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="9385b-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="9385b-326">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="9385b-327">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9385b-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="9385b-328">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="9385b-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="9385b-329">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="9385b-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="9385b-330">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="9385b-331">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="9385b-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="9385b-332">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="9385b-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="9385b-333">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="9385b-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="9385b-334">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash;  , jak i certyfikaty magazynu **podmiotu** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="9385b-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="9385b-335">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="9385b-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="9385b-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="9385b-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="9385b-337">`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="9385b-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="9385b-338">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="9385b-339">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="9385b-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="9385b-340">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="9385b-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="9385b-341">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="9385b-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="9385b-342">`KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="9385b-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="9385b-343">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="9385b-344">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="9385b-344">*Change the defaults in code*</span></span>

<span data-ttu-id="9385b-345">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="9385b-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="9385b-346">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9385b-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="9385b-347">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="9385b-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="9385b-348">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="9385b-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="9385b-349">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="9385b-350">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="9385b-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="9385b-351">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="9385b-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="9385b-352">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9385b-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="9385b-353">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="9385b-353">SNI support requires:</span></span>

* <span data-ttu-id="9385b-354">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="9385b-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="9385b-355">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="9385b-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="9385b-356">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="9385b-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="9385b-357">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="9385b-358">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="9385b-359">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="9385b-359">Connection logging</span></span>

<span data-ttu-id="9385b-360">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="9385b-361">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="9385b-362">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="9385b-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="9385b-363">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="9385b-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="9385b-364">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="9385b-364">Bind to a TCP socket</span></span>

<span data-ttu-id="9385b-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="9385b-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="9385b-366">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="9385b-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="9385b-367">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9385b-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="9385b-368">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="9385b-368">Bind to a Unix socket</span></span>

<span data-ttu-id="9385b-369">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9385b-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="9385b-370">W pliku konfiguracji Nginx Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="9385b-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="9385b-371">`{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="9385b-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="9385b-372">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="9385b-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="9385b-373">Port 0</span><span class="sxs-lookup"><span data-stu-id="9385b-373">Port 0</span></span>

<span data-ttu-id="9385b-374">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="9385b-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="9385b-375">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="9385b-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="9385b-376">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9385b-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="9385b-377">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="9385b-377">Limitations</span></span>

<span data-ttu-id="9385b-378">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9385b-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="9385b-379">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9385b-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="9385b-380">`urls` klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="9385b-380">`urls` host configuration key</span></span>
* <span data-ttu-id="9385b-381">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="9385b-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="9385b-382">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="9385b-383">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="9385b-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="9385b-384">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="9385b-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="9385b-385">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="9385b-386">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="9385b-386">IIS endpoint configuration</span></span>

<span data-ttu-id="9385b-387">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="9385b-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="9385b-388">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="9385b-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="9385b-389">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="9385b-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="9385b-390">`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="9385b-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="9385b-391">Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="9385b-392">`HttpProtocols` wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="9385b-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="9385b-393">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="9385b-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="9385b-394">Tylko HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-394">HTTP/1.1 only.</span></span> <span data-ttu-id="9385b-395">Może być używany z protokołem TLS lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="9385b-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="9385b-396">Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-396">HTTP/2 only.</span></span> <span data-ttu-id="9385b-397">Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="9385b-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="9385b-398">HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="9385b-399">Protokół HTTP/2 wymaga od klienta wyboru protokołu HTTP/2 w uzgadnianiu [negocjowania protokołu TLS aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="9385b-400">Wartość domyślna `ListenOptions.Protocols` dla każdego punktu końcowego to `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="9385b-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="9385b-401">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="9385b-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="9385b-402">TLS w wersji 1,2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="9385b-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="9385b-403">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="9385b-403">Renegotiation disabled</span></span>
* <span data-ttu-id="9385b-404">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="9385b-404">Compression disabled</span></span>
* <span data-ttu-id="9385b-405">Minimalne rozmiary tymczasowych kluczy wymiany:</span><span class="sxs-lookup"><span data-stu-id="9385b-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="9385b-406">Krzywa eliptyczna Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimum 224 bitów</span><span class="sxs-lookup"><span data-stu-id="9385b-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="9385b-407">Ograniczone pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="9385b-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="9385b-408">Mechanizm szyfrowania jest niedozwolony.</span><span class="sxs-lookup"><span data-stu-id="9385b-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="9385b-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="9385b-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="9385b-410">Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="9385b-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="9385b-411">Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="9385b-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="9385b-412">Używaj oprogramowania pośredniczącego do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="9385b-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="9385b-413">Poniższy przykład zgłasza <xref:System.NotSupportedException> dowolny algorytm szyfrowania, który nie jest obsługiwany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="9385b-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="9385b-414">Alternatywnie można zdefiniować i porównać [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) z listą akceptowalnych mechanizmów szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="9385b-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="9385b-415">Nie jest używane szyfrowanie z algorytmem szyfrowania [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="9385b-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="9385b-416">Filtrowanie połączeń można również skonfigurować za pomocą wyrażenia <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="9385b-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="9385b-417">W systemie Linux <xref:System.Net.Security.CipherSuitesPolicy> można używać do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń:</span><span class="sxs-lookup"><span data-stu-id="9385b-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="9385b-418">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="9385b-419">`CreateDefaultBuilder` wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="9385b-420">W poniższym *appsettings.json* przykładzie jako domyślny protokół połączenia dla wszystkich punktów końcowych jest przychodzący ruch HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="9385b-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="9385b-421">Poniższy *appsettings.json* przykład ustanawia protokół połączeń HTTP/1.1 dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="9385b-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="9385b-422">Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="9385b-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="9385b-423">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="9385b-423">URL prefixes</span></span>

<span data-ttu-id="9385b-424">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="9385b-425">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="9385b-426">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="9385b-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="9385b-427">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="9385b-428">`0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="9385b-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="9385b-429">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="9385b-430">`[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="9385b-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="9385b-431">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="9385b-432">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="9385b-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="9385b-433">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="9385b-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="9385b-434">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="9385b-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="9385b-435">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9385b-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="9385b-436">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="9385b-437">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="9385b-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="9385b-438">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="9385b-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="9385b-439">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="9385b-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="9385b-440">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="9385b-440">Host filtering</span></span>

<span data-ttu-id="9385b-441">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="9385b-442">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="9385b-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="9385b-443">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="9385b-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="9385b-444">`Host` nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="9385b-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="9385b-445">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="9385b-446">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest niejawnie dostarczany dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="9385b-447">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="9385b-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="9385b-448">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="9385b-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="9385b-449">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.json* / *appSettings. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="9385b-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="9385b-450">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="9385b-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="9385b-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9385b-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="9385b-452">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="9385b-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="9385b-453">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="9385b-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="9385b-454">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="9385b-455">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="9385b-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="9385b-456">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="9385b-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="9385b-457">Konfiguracja transportu Libuv</span><span class="sxs-lookup"><span data-stu-id="9385b-457">Libuv transport configuration</span></span>

<span data-ttu-id="9385b-458">Dla projektów, które wymagają użycia Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ):</span><span class="sxs-lookup"><span data-stu-id="9385b-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="9385b-459">Dodaj zależność dla [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) pakietu do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9385b-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="9385b-460">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9385b-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="9385b-461">Opróżnianie żądań HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="9385b-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="9385b-462">Otwieranie połączeń HTTP jest czasochłonne.</span><span class="sxs-lookup"><span data-stu-id="9385b-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="9385b-463">W przypadku protokołu HTTPS jest to również czasochłonne zasoby.</span><span class="sxs-lookup"><span data-stu-id="9385b-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="9385b-464">W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="9385b-465">Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="9385b-466">Aplikacja nie zawsze zużywa treści żądania, na przykład żądania, w `POST` których serwer zwraca przekierowanie lub odpowiedź 404.</span><span class="sxs-lookup"><span data-stu-id="9385b-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="9385b-467">W `POST` przypadku przekierowania — przypadek:</span><span class="sxs-lookup"><span data-stu-id="9385b-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="9385b-468">Klient mógł już wysłać część `POST` danych.</span><span class="sxs-lookup"><span data-stu-id="9385b-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="9385b-469">Serwer zapisuje odpowiedź 301.</span><span class="sxs-lookup"><span data-stu-id="9385b-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="9385b-470">Nie można użyć połączenia dla nowego żądania, dopóki `POST` dane z poprzedniej treści żądania nie zostały w pełni odczytane.</span><span class="sxs-lookup"><span data-stu-id="9385b-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="9385b-471">Kestrel próbuje opróżnić treść żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="9385b-472">Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="9385b-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="9385b-473">Proces opróżniania polega na tym, że tradoff między zezwoleniem na ponowne użycie połączenia i czasem potrzebny do opróżnienia pozostałych danych:</span><span class="sxs-lookup"><span data-stu-id="9385b-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="9385b-474">Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.</span><span class="sxs-lookup"><span data-stu-id="9385b-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="9385b-475">Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="9385b-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="9385b-476">Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu.</span><span class="sxs-lookup"><span data-stu-id="9385b-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="9385b-477">Na przykład klienci mogą mieć restrykcyjne limity danych, więc ograniczenie przekazywania danych może być priorytetem.</span><span class="sxs-lookup"><span data-stu-id="9385b-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="9385b-478">W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="9385b-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="9385b-479">Istnieją zastrzeżenia dotyczące wywoływania `Abort` :</span><span class="sxs-lookup"><span data-stu-id="9385b-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="9385b-480">Tworzenie nowych połączeń może być powolne i kosztowne.</span><span class="sxs-lookup"><span data-stu-id="9385b-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="9385b-481">Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="9385b-482">Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.</span><span class="sxs-lookup"><span data-stu-id="9385b-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="9385b-483">Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany.</span><span class="sxs-lookup"><span data-stu-id="9385b-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="9385b-484">Na przykład Wywołaj, `Abort` czy złośliwi klienci próbują uzyskać `POST` dane, czy w kodzie klienta występuje usterka powodująca duże lub liczne żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="9385b-485">Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="9385b-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="9385b-486">Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="9385b-487">Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="9385b-488">Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="9385b-489">Nie ma zastosowania pięć sekund limitu czasu opróżniania.</span><span class="sxs-lookup"><span data-stu-id="9385b-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="9385b-490">Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="9385b-491">Dodatkowe ramki danych treści żądania są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="9385b-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="9385b-492">Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="9385b-493">Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.</span><span class="sxs-lookup"><span data-stu-id="9385b-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9385b-494">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9385b-494">Additional resources</span></span>

* <span data-ttu-id="9385b-495">W przypadku korzystania z gniazd systemu UNIX w systemie Linux gniazdo nie jest automatycznie usuwane podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="9385b-496">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/14134)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="9385b-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="9385b-497">RFC 7230: Składnia i Routing komunikatu (sekcja 5,4: Host)</span><span class="sxs-lookup"><span data-stu-id="9385b-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9385b-498">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="9385b-499">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="9385b-500">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="9385b-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="9385b-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9385b-501">HTTPS</span></span>
* <span data-ttu-id="9385b-502">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="9385b-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="9385b-503">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="9385b-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="9385b-504">HTTP/2 (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="9385b-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="9385b-505">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="9385b-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="9385b-506">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="9385b-507">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9385b-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="9385b-508">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9385b-508">HTTP/2 support</span></span>

<span data-ttu-id="9385b-509">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="9385b-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="9385b-510">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="9385b-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="9385b-511">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="9385b-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="9385b-512">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="9385b-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="9385b-513">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9385b-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="9385b-514">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="9385b-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="9385b-515">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="9385b-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="9385b-516">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="9385b-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="9385b-517">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="9385b-518">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="9385b-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="9385b-519">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="9385b-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="9385b-520">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="9385b-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="9385b-521">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="9385b-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="9385b-522">Więcej informacji na temat konfiguracji można znaleźć w sekcjach [Kestrel Options](#kestrel-options) and [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="9385b-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="9385b-523">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="9385b-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="9385b-524">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="9385b-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="9385b-525">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="9385b-526">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="9385b-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="9385b-528">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="9385b-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="9385b-530">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="9385b-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="9385b-531">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="9385b-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="9385b-532">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="9385b-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="9385b-533">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="9385b-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="9385b-534">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="9385b-535">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="9385b-535">A reverse proxy:</span></span>

* <span data-ttu-id="9385b-536">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="9385b-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="9385b-537">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="9385b-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="9385b-538">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="9385b-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="9385b-539">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9385b-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="9385b-540">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9385b-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="9385b-541">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9385b-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="9385b-542">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9385b-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="9385b-543">Pakiet [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9385b-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9385b-544">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="9385b-545">W *program.cs*, kod szablonu wywołuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , który wywołuje się w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> tle.</span><span class="sxs-lookup"><span data-stu-id="9385b-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="9385b-546">Więcej informacji na temat `CreateDefaultBuilder` i kompilowanie hosta znajduje się w sekcji *Konfigurowanie hosta* w temacie <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="9385b-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="9385b-547">Aby zapewnić dodatkową konfigurację po wywołaniu `CreateDefaultBuilder` , użyj `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="9385b-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="9385b-548">Jeśli aplikacja nie wywoła połączenia `CreateDefaultBuilder` w celu skonfigurowania hosta, wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **przed** wywołaniem `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="9385b-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="9385b-549">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="9385b-549">Kestrel options</span></span>

<span data-ttu-id="9385b-550">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="9385b-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="9385b-551">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="9385b-552">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="9385b-553">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="9385b-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="9385b-554">Opcje Kestrel, które są konfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="9385b-555">Na przykład dostawca konfiguracji plików może załadować konfigurację Kestrel z *appsettings.json* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="9385b-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="9385b-556">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9385b-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="9385b-557">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9385b-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="9385b-558">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="9385b-559">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="9385b-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="9385b-560">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="9385b-561">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="9385b-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="9385b-562">W programie *program.cs* Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="9385b-563">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="9385b-564">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="9385b-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="9385b-565">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="9385b-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="9385b-566">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="9385b-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="9385b-567">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="9385b-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="9385b-568">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="9385b-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="9385b-569">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="9385b-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="9385b-570">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="9385b-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="9385b-571">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="9385b-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="9385b-572">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="9385b-573">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9385b-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="9385b-574">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="9385b-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="9385b-575">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="9385b-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="9385b-576">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="9385b-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="9385b-577">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="9385b-578">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="9385b-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="9385b-579">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="9385b-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="9385b-580">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="9385b-581">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="9385b-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="9385b-582">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="9385b-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="9385b-583">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="9385b-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="9385b-584">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="9385b-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="9385b-585">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="9385b-586">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="9385b-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="9385b-587">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9385b-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="9385b-588">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="9385b-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="9385b-589">Funkcja rate, do której odwołuje się poprzednia próba, nie jest dostępna w `HttpContext.Features` przypadku żądań HTTP/2, ponieważ Modyfikowanie limitów szybkości dla poszczególnych żądań nie jest obsługiwane w przypadku protokołu HTTP/2 ze względu na obsługę multipleksera żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="9385b-590">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="9385b-591">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="9385b-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="9385b-592">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="9385b-593">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="9385b-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="9385b-594">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="9385b-594">Maximum streams per connection</span></span>

<span data-ttu-id="9385b-595">`Http2.MaxStreamsPerConnection` ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="9385b-596">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="9385b-597">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="9385b-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="9385b-598">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="9385b-598">Header table size</span></span>

<span data-ttu-id="9385b-599">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="9385b-600">`Http2.HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="9385b-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="9385b-601">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="9385b-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="9385b-602">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="9385b-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="9385b-603">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="9385b-603">Maximum frame size</span></span>

<span data-ttu-id="9385b-604">`Http2.MaxFrameSize` wskazuje maksymalny rozmiar ładunku ramki połączenia HTTP/2 do odebrania.</span><span class="sxs-lookup"><span data-stu-id="9385b-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="9385b-605">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="9385b-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="9385b-606">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="9385b-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="9385b-607">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-607">Maximum request header size</span></span>

<span data-ttu-id="9385b-608">`Http2.MaxRequestHeaderFieldSize` wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="9385b-609">Ten limit dotyczy zarówno nazwy, jak i wartości razem w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="9385b-610">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="9385b-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="9385b-611">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="9385b-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="9385b-612">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="9385b-612">Initial connection window size</span></span>

<span data-ttu-id="9385b-613">`Http2.InitialConnectionWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="9385b-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="9385b-614">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="9385b-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="9385b-615">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="9385b-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="9385b-616">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="9385b-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="9385b-617">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="9385b-617">Initial stream window size</span></span>

<span data-ttu-id="9385b-618">`Http2.InitialStreamWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="9385b-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="9385b-619">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="9385b-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="9385b-620">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="9385b-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="9385b-621">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="9385b-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="9385b-622">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="9385b-622">Synchronous I/O</span></span>

<span data-ttu-id="9385b-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="9385b-624">Wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="9385b-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9385b-625">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="9385b-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="9385b-626">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="9385b-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="9385b-627">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="9385b-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="9385b-628">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="9385b-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="9385b-629">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="9385b-629">Endpoint configuration</span></span>

<span data-ttu-id="9385b-630">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="9385b-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="9385b-631">`https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="9385b-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="9385b-632">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="9385b-632">Specify URLs using the:</span></span>

* <span data-ttu-id="9385b-633">`ASPNETCORE_URLS` Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="9385b-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="9385b-634">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="9385b-635">`urls` klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="9385b-636">`UseUrls` Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="9385b-637">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="9385b-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="9385b-638">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="9385b-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="9385b-639">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="9385b-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="9385b-640">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="9385b-640">A development certificate is created:</span></span>

* <span data-ttu-id="9385b-641">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="9385b-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="9385b-642">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="9385b-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="9385b-643">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="9385b-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="9385b-644">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="9385b-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9385b-645">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="9385b-646">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9385b-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="9385b-647">`KestrelServerOptions` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="9385b-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="9385b-648">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="9385b-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="9385b-649">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9385b-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="9385b-650">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="9385b-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="9385b-651">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="9385b-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="9385b-652">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="9385b-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="9385b-653">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="9385b-654">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="9385b-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="9385b-655">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="9385b-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="9385b-656">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="9385b-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="9385b-657">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="9385b-658">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="9385b-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="9385b-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="9385b-660">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="9385b-661">`ListenOptions.UseHttps` rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="9385b-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="9385b-662">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="9385b-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="9385b-663">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="9385b-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="9385b-664">`ListenOptions.UseHttps` wejściowe</span><span class="sxs-lookup"><span data-stu-id="9385b-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="9385b-665">`filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="9385b-666">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="9385b-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="9385b-667">`configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="9385b-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="9385b-668">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="9385b-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="9385b-669">`storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="9385b-670">`subject` to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9385b-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="9385b-671">`allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="9385b-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="9385b-672">`location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="9385b-673">`serverCertificate` jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="9385b-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="9385b-674">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9385b-675">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="9385b-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="9385b-676">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="9385b-676">Supported configurations described next:</span></span>

* <span data-ttu-id="9385b-677">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9385b-677">No configuration</span></span>
* <span data-ttu-id="9385b-678">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9385b-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="9385b-679">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="9385b-679">Change the defaults in code</span></span>

<span data-ttu-id="9385b-680">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-680">*No configuration*</span></span>

<span data-ttu-id="9385b-681">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="9385b-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="9385b-682">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="9385b-683">`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="9385b-684">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="9385b-685">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="9385b-686">W poniższym *appsettings.json* przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9385b-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="9385b-687">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="9385b-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="9385b-688">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="9385b-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="9385b-689">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="9385b-690">Certyfikat domyślny można na **przykład**  >   określić jako:</span><span class="sxs-lookup"><span data-stu-id="9385b-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="9385b-691">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="9385b-691">Schema notes:</span></span>

* <span data-ttu-id="9385b-692">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="9385b-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="9385b-693">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="9385b-694">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9385b-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="9385b-695">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="9385b-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="9385b-696">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="9385b-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="9385b-697">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="9385b-698">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="9385b-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="9385b-699">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="9385b-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="9385b-700">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="9385b-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="9385b-701">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash;  , jak i certyfikaty magazynu **podmiotu** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="9385b-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="9385b-702">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="9385b-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="9385b-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="9385b-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="9385b-704">`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="9385b-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="9385b-705">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="9385b-706">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="9385b-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="9385b-707">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="9385b-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="9385b-708">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="9385b-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="9385b-709">`KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="9385b-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="9385b-710">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="9385b-711">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="9385b-711">*Change the defaults in code*</span></span>

<span data-ttu-id="9385b-712">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="9385b-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="9385b-713">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9385b-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="9385b-714">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="9385b-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="9385b-715">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="9385b-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="9385b-716">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="9385b-717">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="9385b-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="9385b-718">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="9385b-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="9385b-719">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9385b-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="9385b-720">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="9385b-720">SNI support requires:</span></span>

* <span data-ttu-id="9385b-721">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="9385b-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="9385b-722">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="9385b-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="9385b-723">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="9385b-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="9385b-724">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="9385b-725">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="9385b-726">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="9385b-726">Connection logging</span></span>

<span data-ttu-id="9385b-727">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="9385b-728">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="9385b-729">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="9385b-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="9385b-730">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="9385b-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="9385b-731">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="9385b-731">Bind to a TCP socket</span></span>

<span data-ttu-id="9385b-732"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="9385b-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="9385b-733">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="9385b-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="9385b-734">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9385b-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="9385b-735">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="9385b-735">Bind to a Unix socket</span></span>

<span data-ttu-id="9385b-736">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9385b-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="9385b-737">W pliku Nginx confiuguration Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="9385b-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="9385b-738">`{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="9385b-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="9385b-739">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="9385b-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="9385b-740">Port 0</span><span class="sxs-lookup"><span data-stu-id="9385b-740">Port 0</span></span>

<span data-ttu-id="9385b-741">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="9385b-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="9385b-742">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="9385b-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="9385b-743">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9385b-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="9385b-744">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="9385b-744">Limitations</span></span>

<span data-ttu-id="9385b-745">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9385b-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="9385b-746">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9385b-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="9385b-747">`urls` klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="9385b-747">`urls` host configuration key</span></span>
* <span data-ttu-id="9385b-748">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="9385b-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="9385b-749">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="9385b-750">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="9385b-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="9385b-751">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="9385b-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="9385b-752">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="9385b-753">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="9385b-753">IIS endpoint configuration</span></span>

<span data-ttu-id="9385b-754">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="9385b-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="9385b-755">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="9385b-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="9385b-756">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="9385b-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="9385b-757">`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="9385b-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="9385b-758">Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="9385b-759">`HttpProtocols` wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="9385b-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="9385b-760">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="9385b-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="9385b-761">Tylko HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-761">HTTP/1.1 only.</span></span> <span data-ttu-id="9385b-762">Może być używany z protokołem TLS lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="9385b-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="9385b-763">Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-763">HTTP/2 only.</span></span> <span data-ttu-id="9385b-764">Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="9385b-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="9385b-765">HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="9385b-766">Protokół HTTP/2 wymaga połączenia protokołów TLS i [warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="9385b-767">Domyślnym protokołem jest HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="9385b-768">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="9385b-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="9385b-769">TLS w wersji 1,2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="9385b-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="9385b-770">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="9385b-770">Renegotiation disabled</span></span>
* <span data-ttu-id="9385b-771">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="9385b-771">Compression disabled</span></span>
* <span data-ttu-id="9385b-772">Minimalne rozmiary tymczasowych kluczy wymiany:</span><span class="sxs-lookup"><span data-stu-id="9385b-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="9385b-773">Krzywa eliptyczna Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimum 224 bitów</span><span class="sxs-lookup"><span data-stu-id="9385b-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="9385b-774">Ograniczone pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="9385b-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="9385b-775">Nie zablokowano pakietu szyfrowania</span><span class="sxs-lookup"><span data-stu-id="9385b-775">Cipher suite not blocked</span></span>

<span data-ttu-id="9385b-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="9385b-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="9385b-777">Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="9385b-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="9385b-778">Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="9385b-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="9385b-779">Opcjonalnie można utworzyć `IConnectionAdapter` implementację do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów:</span><span class="sxs-lookup"><span data-stu-id="9385b-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="9385b-780">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="9385b-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="9385b-782">W poniższym *appsettings.json* przykładzie jest ustanowiony domyślny protokół połączeń (http/1.1 i http/2) dla wszystkich punktów końcowych Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="9385b-783">Poniższy przykład pliku konfiguracji ustanawia protokół połączenia dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="9385b-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="9385b-784">Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="9385b-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="9385b-785">Konfiguracja transportu Libuv</span><span class="sxs-lookup"><span data-stu-id="9385b-785">Libuv transport configuration</span></span>

<span data-ttu-id="9385b-786">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="9385b-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="9385b-787">Jest to istotna zmiana dla aplikacji ASP.NET Core 2,0 uaktualniana do 2,1, które wywołują <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="9385b-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="9385b-788">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="9385b-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="9385b-789">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="9385b-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="9385b-790">Dla projektów, które wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="9385b-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="9385b-791">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9385b-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="9385b-792">Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="9385b-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="9385b-793">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="9385b-793">URL prefixes</span></span>

<span data-ttu-id="9385b-794">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="9385b-795">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="9385b-796">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="9385b-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="9385b-797">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="9385b-798">`0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="9385b-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="9385b-799">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="9385b-800">`[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="9385b-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="9385b-801">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="9385b-802">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="9385b-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="9385b-803">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="9385b-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="9385b-804">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="9385b-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="9385b-805">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9385b-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="9385b-806">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="9385b-807">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="9385b-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="9385b-808">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="9385b-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="9385b-809">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="9385b-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="9385b-810">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="9385b-810">Host filtering</span></span>

<span data-ttu-id="9385b-811">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="9385b-812">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="9385b-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="9385b-813">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="9385b-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="9385b-814">`Host` nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="9385b-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="9385b-815">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="9385b-816">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest zawarty w pakiecie [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub 2,2).</span><span class="sxs-lookup"><span data-stu-id="9385b-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="9385b-817">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="9385b-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="9385b-818">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="9385b-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="9385b-819">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.json* / *appSettings. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="9385b-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="9385b-820">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="9385b-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="9385b-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9385b-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="9385b-822">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="9385b-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="9385b-823">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="9385b-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="9385b-824">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="9385b-825">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="9385b-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="9385b-826">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="9385b-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="9385b-827">Opróżnianie żądań HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="9385b-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="9385b-828">Otwieranie połączeń HTTP jest czasochłonne.</span><span class="sxs-lookup"><span data-stu-id="9385b-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="9385b-829">W przypadku protokołu HTTPS jest to również czasochłonne zasoby.</span><span class="sxs-lookup"><span data-stu-id="9385b-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="9385b-830">W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="9385b-831">Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="9385b-832">Aplikacja nie zawsze zużywa treści żądania, na przykład żądania, w `POST` których serwer zwraca przekierowanie lub odpowiedź 404.</span><span class="sxs-lookup"><span data-stu-id="9385b-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="9385b-833">W `POST` przypadku przekierowania — przypadek:</span><span class="sxs-lookup"><span data-stu-id="9385b-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="9385b-834">Klient mógł już wysłać część `POST` danych.</span><span class="sxs-lookup"><span data-stu-id="9385b-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="9385b-835">Serwer zapisuje odpowiedź 301.</span><span class="sxs-lookup"><span data-stu-id="9385b-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="9385b-836">Nie można użyć połączenia dla nowego żądania, dopóki `POST` dane z poprzedniej treści żądania nie zostały w pełni odczytane.</span><span class="sxs-lookup"><span data-stu-id="9385b-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="9385b-837">Kestrel próbuje opróżnić treść żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="9385b-838">Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="9385b-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="9385b-839">Proces opróżniania polega na tym, że tradoff między zezwoleniem na ponowne użycie połączenia i czasem potrzebny do opróżnienia pozostałych danych:</span><span class="sxs-lookup"><span data-stu-id="9385b-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="9385b-840">Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.</span><span class="sxs-lookup"><span data-stu-id="9385b-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="9385b-841">Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="9385b-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="9385b-842">Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu.</span><span class="sxs-lookup"><span data-stu-id="9385b-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="9385b-843">Na przykład klienci mogą mieć restrykcyjne limity danych, więc ograniczenie przekazywania danych może być priorytetem.</span><span class="sxs-lookup"><span data-stu-id="9385b-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="9385b-844">W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="9385b-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="9385b-845">Istnieją zastrzeżenia dotyczące wywoływania `Abort` :</span><span class="sxs-lookup"><span data-stu-id="9385b-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="9385b-846">Tworzenie nowych połączeń może być powolne i kosztowne.</span><span class="sxs-lookup"><span data-stu-id="9385b-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="9385b-847">Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="9385b-848">Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.</span><span class="sxs-lookup"><span data-stu-id="9385b-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="9385b-849">Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany.</span><span class="sxs-lookup"><span data-stu-id="9385b-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="9385b-850">Na przykład Wywołaj, `Abort` czy złośliwi klienci próbują uzyskać `POST` dane, czy w kodzie klienta występuje usterka powodująca duże lub liczne żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="9385b-851">Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="9385b-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="9385b-852">Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="9385b-853">Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="9385b-854">Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="9385b-855">Nie ma zastosowania pięć sekund limitu czasu opróżniania.</span><span class="sxs-lookup"><span data-stu-id="9385b-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="9385b-856">Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="9385b-857">Dodatkowe ramki danych treści żądania są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="9385b-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="9385b-858">Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="9385b-859">Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.</span><span class="sxs-lookup"><span data-stu-id="9385b-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9385b-860">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9385b-860">Additional resources</span></span>

* <span data-ttu-id="9385b-861">W przypadku korzystania z gniazd systemu UNIX w systemie Linux gniazdo nie jest automatycznie usuwane podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="9385b-862">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/14134)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="9385b-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="9385b-863">RFC 7230: Składnia i Routing komunikatu (sekcja 5,4: Host)</span><span class="sxs-lookup"><span data-stu-id="9385b-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="9385b-864">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="9385b-865">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="9385b-866">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="9385b-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="9385b-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9385b-867">HTTPS</span></span>
* <span data-ttu-id="9385b-868">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="9385b-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="9385b-869">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="9385b-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="9385b-870">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9385b-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="9385b-871">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9385b-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="9385b-872">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="9385b-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="9385b-873">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="9385b-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="9385b-874">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="9385b-875">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="9385b-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="9385b-877">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="9385b-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="9385b-879">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="9385b-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="9385b-880">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="9385b-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="9385b-881">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="9385b-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="9385b-882">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="9385b-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="9385b-883">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="9385b-884">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="9385b-884">A reverse proxy:</span></span>

* <span data-ttu-id="9385b-885">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="9385b-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="9385b-886">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="9385b-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="9385b-887">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="9385b-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="9385b-888">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9385b-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="9385b-889">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9385b-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="9385b-890">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9385b-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="9385b-891">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9385b-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="9385b-892">Pakiet [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9385b-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9385b-893">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="9385b-894">W *program.cs*, kod szablonu wywołuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , który wywołuje się w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> tle.</span><span class="sxs-lookup"><span data-stu-id="9385b-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="9385b-895">Aby zapewnić dodatkową konfigurację po wywołaniu `CreateDefaultBuilder` , wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="9385b-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="9385b-896">Więcej informacji na temat `CreateDefaultBuilder` i kompilowanie hosta znajduje się w sekcji *Konfigurowanie hosta* w temacie <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="9385b-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="9385b-897">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="9385b-897">Kestrel options</span></span>

<span data-ttu-id="9385b-898">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="9385b-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="9385b-899">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="9385b-900">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="9385b-901">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="9385b-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="9385b-902">Opcje Kestrel, które są konfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="9385b-903">Na przykład dostawca konfiguracji plików może załadować konfigurację Kestrel z *appsettings.json* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="9385b-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="9385b-904">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9385b-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="9385b-905">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9385b-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="9385b-906">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="9385b-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="9385b-907">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="9385b-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="9385b-908">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="9385b-909">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="9385b-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="9385b-910">W programie *program.cs* Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9385b-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="9385b-911">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9385b-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="9385b-912">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="9385b-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="9385b-913">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="9385b-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="9385b-914">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="9385b-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="9385b-915">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="9385b-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="9385b-916">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="9385b-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="9385b-917">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="9385b-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="9385b-918">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="9385b-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="9385b-919">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="9385b-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="9385b-920">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="9385b-921">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9385b-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="9385b-922">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="9385b-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="9385b-923">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="9385b-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="9385b-924">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="9385b-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="9385b-925">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="9385b-926">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="9385b-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="9385b-927">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="9385b-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="9385b-928">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="9385b-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="9385b-929">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="9385b-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="9385b-930">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="9385b-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="9385b-931">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="9385b-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="9385b-932">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="9385b-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="9385b-933">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="9385b-934">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="9385b-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="9385b-935">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9385b-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="9385b-936">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="9385b-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="9385b-937">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="9385b-938">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="9385b-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="9385b-939">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="9385b-939">Synchronous I/O</span></span>

<span data-ttu-id="9385b-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="9385b-941">Wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="9385b-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9385b-942">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="9385b-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="9385b-943">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="9385b-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="9385b-944">Poniższy przykład wyłącza synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="9385b-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="9385b-945">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="9385b-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="9385b-946">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="9385b-946">Endpoint configuration</span></span>

<span data-ttu-id="9385b-947">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="9385b-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="9385b-948">`https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="9385b-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="9385b-949">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="9385b-949">Specify URLs using the:</span></span>

* <span data-ttu-id="9385b-950">`ASPNETCORE_URLS` Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="9385b-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="9385b-951">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="9385b-952">`urls` klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="9385b-953">`UseUrls` Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="9385b-954">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="9385b-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="9385b-955">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="9385b-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="9385b-956">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="9385b-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="9385b-957">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="9385b-957">A development certificate is created:</span></span>

* <span data-ttu-id="9385b-958">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="9385b-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="9385b-959">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="9385b-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="9385b-960">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="9385b-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="9385b-961">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="9385b-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9385b-962">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="9385b-963">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9385b-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="9385b-964">`KestrelServerOptions` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="9385b-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="9385b-965">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="9385b-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="9385b-966">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9385b-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="9385b-967">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="9385b-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="9385b-968">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="9385b-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="9385b-969">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="9385b-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="9385b-970">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="9385b-971">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="9385b-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="9385b-972">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="9385b-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="9385b-973">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="9385b-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="9385b-974">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="9385b-975">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="9385b-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="9385b-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="9385b-977">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="9385b-978">`ListenOptions.UseHttps` rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="9385b-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="9385b-979">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="9385b-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="9385b-980">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="9385b-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="9385b-981">`ListenOptions.UseHttps` wejściowe</span><span class="sxs-lookup"><span data-stu-id="9385b-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="9385b-982">`filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="9385b-983">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="9385b-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="9385b-984">`configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="9385b-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="9385b-985">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="9385b-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="9385b-986">`storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="9385b-987">`subject` to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9385b-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="9385b-988">`allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="9385b-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="9385b-989">`location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="9385b-990">`serverCertificate` jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="9385b-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="9385b-991">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9385b-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9385b-992">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="9385b-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="9385b-993">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="9385b-993">Supported configurations described next:</span></span>

* <span data-ttu-id="9385b-994">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9385b-994">No configuration</span></span>
* <span data-ttu-id="9385b-995">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9385b-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="9385b-996">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="9385b-996">Change the defaults in code</span></span>

<span data-ttu-id="9385b-997">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-997">*No configuration*</span></span>

<span data-ttu-id="9385b-998">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="9385b-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="9385b-999">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="9385b-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="9385b-1000">`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="9385b-1001">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="9385b-1002">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="9385b-1003">W poniższym *appsettings.json* przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9385b-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="9385b-1004">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="9385b-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="9385b-1005">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="9385b-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="9385b-1006">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="9385b-1007">Certyfikat domyślny można na **przykład**  >   określić jako:</span><span class="sxs-lookup"><span data-stu-id="9385b-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="9385b-1008">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="9385b-1008">Schema notes:</span></span>

* <span data-ttu-id="9385b-1009">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="9385b-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="9385b-1010">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="9385b-1011">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9385b-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="9385b-1012">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="9385b-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="9385b-1013">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="9385b-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="9385b-1014">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="9385b-1015">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="9385b-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="9385b-1016">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="9385b-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="9385b-1017">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="9385b-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="9385b-1018">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash;  , jak i certyfikaty magazynu **podmiotu** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="9385b-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="9385b-1019">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="9385b-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="9385b-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="9385b-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="9385b-1021">`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="9385b-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="9385b-1022">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="9385b-1023">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="9385b-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="9385b-1024">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="9385b-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="9385b-1025">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="9385b-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="9385b-1026">`KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="9385b-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="9385b-1027">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9385b-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="9385b-1028">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="9385b-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="9385b-1029">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="9385b-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="9385b-1030">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9385b-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="9385b-1031">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="9385b-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="9385b-1032">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="9385b-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="9385b-1033">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="9385b-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="9385b-1034">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="9385b-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="9385b-1035">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="9385b-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="9385b-1036">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9385b-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="9385b-1037">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="9385b-1037">SNI support requires:</span></span>

* <span data-ttu-id="9385b-1038">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="9385b-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="9385b-1039">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="9385b-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="9385b-1040">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="9385b-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="9385b-1041">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="9385b-1042">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="9385b-1043">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="9385b-1043">Connection logging</span></span>

<span data-ttu-id="9385b-1044">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="9385b-1045">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="9385b-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="9385b-1046">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="9385b-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="9385b-1047">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="9385b-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="9385b-1048">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="9385b-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="9385b-1049"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="9385b-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="9385b-1050">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="9385b-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="9385b-1051">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9385b-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="9385b-1052">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="9385b-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="9385b-1053">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9385b-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="9385b-1054">W pliku Nginx confiuguration Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="9385b-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="9385b-1055">`{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="9385b-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="9385b-1056">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="9385b-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="9385b-1057">Port 0</span><span class="sxs-lookup"><span data-stu-id="9385b-1057">Port 0</span></span>

<span data-ttu-id="9385b-1058">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="9385b-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="9385b-1059">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="9385b-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="9385b-1060">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9385b-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="9385b-1061">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="9385b-1061">Limitations</span></span>

<span data-ttu-id="9385b-1062">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9385b-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="9385b-1063">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9385b-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="9385b-1064">`urls` klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="9385b-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="9385b-1065">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="9385b-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="9385b-1066">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9385b-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="9385b-1067">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="9385b-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="9385b-1068">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="9385b-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="9385b-1069">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="9385b-1070">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="9385b-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="9385b-1071">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="9385b-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="9385b-1072">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="9385b-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="9385b-1073">Konfiguracja transportu Libuv</span><span class="sxs-lookup"><span data-stu-id="9385b-1073">Libuv transport configuration</span></span>

<span data-ttu-id="9385b-1074">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="9385b-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="9385b-1075">Jest to istotna zmiana dla aplikacji ASP.NET Core 2,0 uaktualniana do 2,1, które wywołują <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="9385b-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="9385b-1076">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="9385b-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="9385b-1077">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="9385b-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="9385b-1078">Dla projektów, które wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="9385b-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="9385b-1079">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9385b-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="9385b-1080">Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="9385b-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="9385b-1081">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="9385b-1081">URL prefixes</span></span>

<span data-ttu-id="9385b-1082">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="9385b-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="9385b-1083">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="9385b-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="9385b-1084">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="9385b-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="9385b-1085">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="9385b-1086">`0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="9385b-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="9385b-1087">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="9385b-1088">`[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="9385b-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="9385b-1089">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="9385b-1090">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="9385b-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="9385b-1091">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="9385b-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="9385b-1092">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="9385b-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="9385b-1093">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9385b-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="9385b-1094">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="9385b-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="9385b-1095">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="9385b-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="9385b-1096">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="9385b-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="9385b-1097">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="9385b-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="9385b-1098">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="9385b-1098">Host filtering</span></span>

<span data-ttu-id="9385b-1099">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="9385b-1100">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="9385b-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="9385b-1101">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="9385b-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="9385b-1102">`Host` nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="9385b-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="9385b-1103">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="9385b-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="9385b-1104">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest zawarty w pakiecie [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub 2,2).</span><span class="sxs-lookup"><span data-stu-id="9385b-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="9385b-1105">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="9385b-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="9385b-1106">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="9385b-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="9385b-1107">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.json* / *appSettings. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="9385b-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="9385b-1108">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="9385b-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="9385b-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9385b-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="9385b-1110">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="9385b-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="9385b-1111">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="9385b-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="9385b-1112">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="9385b-1113">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="9385b-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="9385b-1114">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="9385b-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="9385b-1115">Opróżnianie żądań HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="9385b-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="9385b-1116">Otwieranie połączeń HTTP jest czasochłonne.</span><span class="sxs-lookup"><span data-stu-id="9385b-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="9385b-1117">W przypadku protokołu HTTPS jest to również czasochłonne zasoby.</span><span class="sxs-lookup"><span data-stu-id="9385b-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="9385b-1118">W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9385b-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="9385b-1119">Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="9385b-1120">Aplikacja nie zawsze zużywa treści żądania, na przykład żądania, w `POST` których serwer zwraca przekierowanie lub odpowiedź 404.</span><span class="sxs-lookup"><span data-stu-id="9385b-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="9385b-1121">W `POST` przypadku przekierowania — przypadek:</span><span class="sxs-lookup"><span data-stu-id="9385b-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="9385b-1122">Klient mógł już wysłać część `POST` danych.</span><span class="sxs-lookup"><span data-stu-id="9385b-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="9385b-1123">Serwer zapisuje odpowiedź 301.</span><span class="sxs-lookup"><span data-stu-id="9385b-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="9385b-1124">Nie można użyć połączenia dla nowego żądania, dopóki `POST` dane z poprzedniej treści żądania nie zostały w pełni odczytane.</span><span class="sxs-lookup"><span data-stu-id="9385b-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="9385b-1125">Kestrel próbuje opróżnić treść żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="9385b-1126">Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="9385b-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="9385b-1127">Proces opróżniania polega na tym, że tradoff między zezwoleniem na ponowne użycie połączenia i czasem potrzebny do opróżnienia pozostałych danych:</span><span class="sxs-lookup"><span data-stu-id="9385b-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="9385b-1128">Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.</span><span class="sxs-lookup"><span data-stu-id="9385b-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="9385b-1129">Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="9385b-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="9385b-1130">Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu.</span><span class="sxs-lookup"><span data-stu-id="9385b-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="9385b-1131">Na przykład klienci mogą mieć restrykcyjne limity danych, więc ograniczenie przekazywania danych może być priorytetem.</span><span class="sxs-lookup"><span data-stu-id="9385b-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="9385b-1132">W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="9385b-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="9385b-1133">Istnieją zastrzeżenia dotyczące wywoływania `Abort` :</span><span class="sxs-lookup"><span data-stu-id="9385b-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="9385b-1134">Tworzenie nowych połączeń może być powolne i kosztowne.</span><span class="sxs-lookup"><span data-stu-id="9385b-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="9385b-1135">Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="9385b-1136">Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.</span><span class="sxs-lookup"><span data-stu-id="9385b-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="9385b-1137">Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany.</span><span class="sxs-lookup"><span data-stu-id="9385b-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="9385b-1138">Na przykład Wywołaj, `Abort` czy złośliwi klienci próbują uzyskać `POST` dane, czy w kodzie klienta występuje usterka powodująca duże lub liczne żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="9385b-1139">Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="9385b-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="9385b-1140">Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9385b-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="9385b-1141">Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="9385b-1142">Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia.</span><span class="sxs-lookup"><span data-stu-id="9385b-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="9385b-1143">Nie ma zastosowania pięć sekund limitu czasu opróżniania.</span><span class="sxs-lookup"><span data-stu-id="9385b-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="9385b-1144">Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9385b-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="9385b-1145">Dodatkowe ramki danych treści żądania są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="9385b-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="9385b-1146">Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="9385b-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="9385b-1147">Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.</span><span class="sxs-lookup"><span data-stu-id="9385b-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9385b-1148">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9385b-1148">Additional resources</span></span>

* <span data-ttu-id="9385b-1149">W przypadku korzystania z gniazd systemu UNIX w systemie Linux gniazdo nie jest automatycznie usuwane podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9385b-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="9385b-1150">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/14134)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="9385b-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="9385b-1151">RFC 7230: Składnia i Routing komunikatu (sekcja 5,4: Host)</span><span class="sxs-lookup"><span data-stu-id="9385b-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
