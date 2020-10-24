---
title: Implementacja serwera sieci Web Kestrel w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
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
ms.openlocfilehash: 50bf2a60f14238c9b71fe90a64c284da202bff59
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491603"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="11e61-103">Implementacja serwera sieci Web Kestrel w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e61-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="11e61-104">Autorzy [Dykstra](https://github.com/tdykstra), [Krzysztof Ross](https://github.com/Tratcher)i [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="11e61-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11e61-105">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="11e61-106">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e61-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="11e61-107">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="11e61-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="11e61-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="11e61-108">HTTPS</span></span>
* <span data-ttu-id="11e61-109">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="11e61-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="11e61-110">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="11e61-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="11e61-111">HTTP/2 (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="11e61-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="11e61-112">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="11e61-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="11e61-113">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e61-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="11e61-114">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11e61-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="11e61-115">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="11e61-115">HTTP/2 support</span></span>

<span data-ttu-id="11e61-116">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="11e61-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="11e61-117">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="11e61-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="11e61-118">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="11e61-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="11e61-119">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="11e61-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="11e61-120">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="11e61-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="11e61-121">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="11e61-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="11e61-122">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="11e61-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="11e61-123">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="11e61-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="11e61-124">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="11e61-125">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="11e61-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="11e61-126">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="11e61-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="11e61-127">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="11e61-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="11e61-128">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="11e61-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="11e61-129">Więcej informacji na temat konfiguracji można znaleźć w sekcjach [Kestrel Options](#kestrel-options) and [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="11e61-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="11e61-130">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="11e61-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="11e61-131">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="11e61-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="11e61-132">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="11e61-133">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="11e61-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="11e61-135">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="11e61-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="11e61-137">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="11e61-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="11e61-138">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="11e61-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="11e61-139">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="11e61-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="11e61-140">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="11e61-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="11e61-141">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="11e61-142">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="11e61-142">A reverse proxy:</span></span>

* <span data-ttu-id="11e61-143">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="11e61-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="11e61-144">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="11e61-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="11e61-145">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="11e61-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="11e61-146">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="11e61-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="11e61-147">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="11e61-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="11e61-148">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="11e61-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="11e61-149">Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e61-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="11e61-150">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="11e61-151">W *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> Metoda wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="11e61-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="11e61-152">Więcej informacji na temat tworzenia hosta znajduje się w sekcji *Konfigurowanie ustawień hosta* i *domyślnego konstruktora* w temacie <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="11e61-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="11e61-153">Aby zapewnić dodatkową konfigurację po wywołaniu `ConfigureWebHostDefaults` , użyj `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="11e61-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="11e61-154">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="11e61-154">Kestrel options</span></span>

<span data-ttu-id="11e61-155">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="11e61-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="11e61-156">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="11e61-157">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="11e61-158">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="11e61-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="11e61-159">W przykładach przedstawionych w dalszej części tego artykułu opcje Kestrel są konfigurowane w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="11e61-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="11e61-160">Opcje Kestrel można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="11e61-161">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację Kestrel z *appsettings.jsw* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="11e61-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="11e61-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfigurację punktu końcowego](#endpoint-configuration) można konfigurować z poziomu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="11e61-163">Pozostała konfiguracja Kestrel musi być skonfigurowana w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="11e61-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="11e61-164">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="11e61-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="11e61-165">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="11e61-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="11e61-166">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="11e61-167">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="11e61-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="11e61-168">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="11e61-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="11e61-169">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="11e61-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="11e61-170">W programie *program.cs*Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="11e61-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="11e61-171">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="11e61-172">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="11e61-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="11e61-173">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="11e61-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="11e61-174">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="11e61-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="11e61-175">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="11e61-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="11e61-176">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="11e61-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="11e61-177">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="11e61-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="11e61-178">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="11e61-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="11e61-179">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="11e61-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="11e61-180">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="11e61-181">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="11e61-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="11e61-182">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="11e61-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="11e61-183">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="11e61-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="11e61-184">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="11e61-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="11e61-185">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="11e61-186">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="11e61-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="11e61-187">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="11e61-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="11e61-188">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="11e61-189">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="11e61-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="11e61-190">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="11e61-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="11e61-191">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="11e61-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="11e61-192">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="11e61-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="11e61-193">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="11e61-194">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="11e61-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="11e61-195">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="11e61-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="11e61-196">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="11e61-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="11e61-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odwołania w poprzednim przykładzie nie występują w `HttpContext.Features` żądaniach HTTP/2, ponieważ Modyfikowanie limitów szybkości dla poszczególnych żądań jest ogólnie nieobsługiwane w przypadku protokołu HTTP/2 z powodu obsługi żądania multipleksowania żądań.</span><span class="sxs-lookup"><span data-stu-id="11e61-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="11e61-198">Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> nadal występuje `HttpContext.Features` dla żądań HTTP/2, ponieważ limit liczby odczytów nadal można *wyłączyć całkowicie* dla każdego żądania przez ustawienie `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` nawet dla żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="11e61-199">Próba odczytania `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby ustawienia jej na wartość inną niż `null` spowoduje `NotSupportedException` zgłoszenie żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="11e61-200">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="11e61-201">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="11e61-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="11e61-202">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="11e61-203">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="11e61-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="11e61-204">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="11e61-204">Maximum streams per connection</span></span>

<span data-ttu-id="11e61-205">`Http2.MaxStreamsPerConnection` ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="11e61-206">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="11e61-207">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="11e61-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="11e61-208">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="11e61-208">Header table size</span></span>

<span data-ttu-id="11e61-209">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="11e61-210">`Http2.HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="11e61-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="11e61-211">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="11e61-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="11e61-212">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="11e61-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="11e61-213">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="11e61-213">Maximum frame size</span></span>

<span data-ttu-id="11e61-214">`Http2.MaxFrameSize` wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer.</span><span class="sxs-lookup"><span data-stu-id="11e61-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="11e61-215">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="11e61-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="11e61-216">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="11e61-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="11e61-217">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-217">Maximum request header size</span></span>

<span data-ttu-id="11e61-218">`Http2.MaxRequestHeaderFieldSize` wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="11e61-219">Ten limit dotyczy zarówno nazwy, jak i wartości w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="11e61-220">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="11e61-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="11e61-221">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="11e61-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="11e61-222">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="11e61-222">Initial connection window size</span></span>

<span data-ttu-id="11e61-223">`Http2.InitialConnectionWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="11e61-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="11e61-224">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="11e61-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="11e61-225">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="11e61-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="11e61-226">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="11e61-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="11e61-227">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="11e61-227">Initial stream window size</span></span>

<span data-ttu-id="11e61-228">`Http2.InitialStreamWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="11e61-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="11e61-229">Żądania są również ograniczone przez `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="11e61-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="11e61-230">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="11e61-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="11e61-231">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="11e61-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="11e61-232">Konfiguracja polecenia ping Keep Alive protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="11e61-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="11e61-233">Kestrel można skonfigurować do wysyłania poleceń ping protokołu HTTP/2 do podłączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="11e61-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="11e61-234">Polecenia ping protokołu HTTP/2 współpracują z wieloma celami:</span><span class="sxs-lookup"><span data-stu-id="11e61-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="11e61-235">Utrzymywanie bezczynnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="11e61-235">Keep idle connections alive.</span></span> <span data-ttu-id="11e61-236">Niektórzy klienci i serwery proxy zamykają połączenia, które są w stanie bezczynności.</span><span class="sxs-lookup"><span data-stu-id="11e61-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="11e61-237">Polecenia ping protokołu HTTP/2 są uznawane za działania w ramach połączenia i uniemożliwiają zamknięcie połączenia w trybie bezczynności.</span><span class="sxs-lookup"><span data-stu-id="11e61-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="11e61-238">Zamknij połączenia w złej kondycji.</span><span class="sxs-lookup"><span data-stu-id="11e61-238">Close unhealthy connections.</span></span> <span data-ttu-id="11e61-239">Połączenia, w których klient nie odpowiada na polecenie "Keep Alive" w skonfigurowanym czasie, są zamykane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="11e61-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="11e61-240">Dostępne są dwie opcje konfiguracji związane z poleceniami "Keep Alive" protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="11e61-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="11e61-241">`Http2.KeepAlivePingInterval` to element `TimeSpan` , który konfiguruje wewnętrznie polecenie ping.</span><span class="sxs-lookup"><span data-stu-id="11e61-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="11e61-242">Serwer wysyła polecenie ping do klienta, jeśli nie otrzyma żadnej ramki przez ten okres.</span><span class="sxs-lookup"><span data-stu-id="11e61-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="11e61-243">Polecenia ping utrzymywania aktywności są wyłączone, gdy ta opcja jest ustawiona na `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="11e61-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="11e61-244">Wartość domyślna to `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="11e61-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="11e61-245">`Http2.KeepAlivePingTimeout``TimeSpan`umożliwia skonfigurowanie limitu czasu polecenia ping.</span><span class="sxs-lookup"><span data-stu-id="11e61-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="11e61-246">Jeśli serwer nie odbiera żadnych ramek, takich jak odpowiedzi ping, podczas tego limitu czasu połączenie jest zamknięte.</span><span class="sxs-lookup"><span data-stu-id="11e61-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="11e61-247">Limit czasu utrzymywania aktywności jest wyłączony, gdy ta opcja jest ustawiona na `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="11e61-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="11e61-248">Wartość domyślna to 20 sekund.</span><span class="sxs-lookup"><span data-stu-id="11e61-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="11e61-249">Przyczep</span><span class="sxs-lookup"><span data-stu-id="11e61-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="11e61-250">Reset</span><span class="sxs-lookup"><span data-stu-id="11e61-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="11e61-251">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="11e61-251">Synchronous I/O</span></span>

<span data-ttu-id="11e61-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="11e61-253">Wartość domyślna to `false`.</span><span class="sxs-lookup"><span data-stu-id="11e61-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="11e61-254">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="11e61-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="11e61-255">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="11e61-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="11e61-256">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="11e61-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="11e61-257">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="11e61-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="11e61-258">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="11e61-258">Endpoint configuration</span></span>

<span data-ttu-id="11e61-259">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="11e61-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="11e61-260">`https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="11e61-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="11e61-261">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="11e61-261">Specify URLs using the:</span></span>

* <span data-ttu-id="11e61-262">`ASPNETCORE_URLS` Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="11e61-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="11e61-263">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="11e61-264">`urls` klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="11e61-265">`UseUrls` Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="11e61-266">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="11e61-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="11e61-267">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="11e61-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="11e61-268">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="11e61-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="11e61-269">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="11e61-269">A development certificate is created:</span></span>

* <span data-ttu-id="11e61-270">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="11e61-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="11e61-271">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="11e61-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="11e61-272">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="11e61-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="11e61-273">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="11e61-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="11e61-274">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="11e61-275">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="11e61-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="11e61-276">`KestrelServerOptions` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="11e61-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="11e61-277">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="11e61-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="11e61-278">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="11e61-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="11e61-279">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="11e61-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="11e61-280">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="11e61-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="11e61-281">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="11e61-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="11e61-282">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="11e61-283">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="11e61-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="11e61-284">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="11e61-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="11e61-285">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="11e61-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="11e61-286">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="11e61-287">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="11e61-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="11e61-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="11e61-289">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="11e61-290">`ListenOptions.UseHttps` rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="11e61-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="11e61-291">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="11e61-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="11e61-292">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="11e61-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="11e61-293">`ListenOptions.UseHttps` wejściowe</span><span class="sxs-lookup"><span data-stu-id="11e61-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="11e61-294">`filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="11e61-295">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="11e61-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="11e61-296">`configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="11e61-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="11e61-297">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="11e61-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="11e61-298">`storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="11e61-299">`subject` to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="11e61-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="11e61-300">`allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="11e61-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="11e61-301">`location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="11e61-302">`serverCertificate` jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="11e61-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="11e61-303">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="11e61-304">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="11e61-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="11e61-305">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="11e61-305">Supported configurations described next:</span></span>

* <span data-ttu-id="11e61-306">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="11e61-306">No configuration</span></span>
* <span data-ttu-id="11e61-307">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="11e61-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="11e61-308">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="11e61-308">Change the defaults in code</span></span>

<span data-ttu-id="11e61-309">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-309">*No configuration*</span></span>

<span data-ttu-id="11e61-310">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="11e61-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="11e61-311">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="11e61-312">`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="11e61-313">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="11e61-314">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="11e61-315">W poniższym *appsettings.jsna* przykład:</span><span class="sxs-lookup"><span data-stu-id="11e61-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="11e61-316">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="11e61-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="11e61-317">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="11e61-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="11e61-318">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="11e61-319">Certyfikat domyślny można na **przykład**  >  **Default** określić jako:</span><span class="sxs-lookup"><span data-stu-id="11e61-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="11e61-320">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="11e61-320">Schema notes:</span></span>

* <span data-ttu-id="11e61-321">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="11e61-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="11e61-322">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="11e61-323">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="11e61-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="11e61-324">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="11e61-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="11e61-325">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="11e61-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="11e61-326">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="11e61-327">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="11e61-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="11e61-328">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="11e61-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="11e61-329">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="11e61-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="11e61-330">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash; **Password** , jak i certyfikaty magazynu **podmiotu** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="11e61-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="11e61-331">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="11e61-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="11e61-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="11e61-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="11e61-333">`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="11e61-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="11e61-334">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="11e61-335">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="11e61-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="11e61-336">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="11e61-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="11e61-337">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="11e61-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="11e61-338">`KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="11e61-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="11e61-339">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="11e61-340">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="11e61-340">*Change the defaults in code*</span></span>

<span data-ttu-id="11e61-341">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="11e61-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="11e61-342">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="11e61-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="11e61-343">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="11e61-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="11e61-344">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="11e61-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="11e61-345">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="11e61-346">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="11e61-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="11e61-347">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="11e61-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="11e61-348">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="11e61-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="11e61-349">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="11e61-349">SNI support requires:</span></span>

* <span data-ttu-id="11e61-350">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="11e61-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="11e61-351">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="11e61-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="11e61-352">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="11e61-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="11e61-353">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="11e61-354">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="11e61-355">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="11e61-355">Connection logging</span></span>

<span data-ttu-id="11e61-356">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="11e61-357">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="11e61-358">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="11e61-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="11e61-359">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="11e61-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="11e61-360">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="11e61-360">Bind to a TCP socket</span></span>

<span data-ttu-id="11e61-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="11e61-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="11e61-362">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="11e61-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="11e61-363">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="11e61-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="11e61-364">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="11e61-364">Bind to a Unix socket</span></span>

<span data-ttu-id="11e61-365">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="11e61-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="11e61-366">W pliku konfiguracji Nginx Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="11e61-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="11e61-367">`{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="11e61-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="11e61-368">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="11e61-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="11e61-369">Port 0</span><span class="sxs-lookup"><span data-stu-id="11e61-369">Port 0</span></span>

<span data-ttu-id="11e61-370">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="11e61-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="11e61-371">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="11e61-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="11e61-372">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11e61-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="11e61-373">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="11e61-373">Limitations</span></span>

<span data-ttu-id="11e61-374">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="11e61-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="11e61-375">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="11e61-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="11e61-376">`urls` klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="11e61-376">`urls` host configuration key</span></span>
* <span data-ttu-id="11e61-377">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="11e61-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="11e61-378">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="11e61-379">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="11e61-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="11e61-380">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="11e61-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="11e61-381">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="11e61-382">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="11e61-382">IIS endpoint configuration</span></span>

<span data-ttu-id="11e61-383">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="11e61-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="11e61-384">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="11e61-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="11e61-385">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="11e61-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="11e61-386">`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="11e61-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="11e61-387">Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="11e61-388">`HttpProtocols` wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="11e61-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="11e61-389">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="11e61-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="11e61-390">Tylko HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-390">HTTP/1.1 only.</span></span> <span data-ttu-id="11e61-391">Może być używany z protokołem TLS lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="11e61-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="11e61-392">Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-392">HTTP/2 only.</span></span> <span data-ttu-id="11e61-393">Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="11e61-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="11e61-394">HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="11e61-395">Protokół HTTP/2 wymaga od klienta wyboru protokołu HTTP/2 w uzgadnianiu [negocjowania protokołu TLS aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="11e61-396">Wartość domyślna `ListenOptions.Protocols` dla każdego punktu końcowego to `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="11e61-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="11e61-397">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="11e61-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="11e61-398">TLS w wersji 1,2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="11e61-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="11e61-399">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="11e61-399">Renegotiation disabled</span></span>
* <span data-ttu-id="11e61-400">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="11e61-400">Compression disabled</span></span>
* <span data-ttu-id="11e61-401">Minimalne rozmiary tymczasowych kluczy wymiany:</span><span class="sxs-lookup"><span data-stu-id="11e61-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="11e61-402">Krzywa eliptyczna Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimum 224 bitów</span><span class="sxs-lookup"><span data-stu-id="11e61-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="11e61-403">Ograniczone pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="11e61-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="11e61-404">Mechanizm szyfrowania jest niedozwolony.</span><span class="sxs-lookup"><span data-stu-id="11e61-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="11e61-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="11e61-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="11e61-406">Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="11e61-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="11e61-407">Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="11e61-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="11e61-408">Używaj oprogramowania pośredniczącego do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="11e61-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="11e61-409">Poniższy przykład zgłasza <xref:System.NotSupportedException> dowolny algorytm szyfrowania, który nie jest obsługiwany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="11e61-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="11e61-410">Alternatywnie można zdefiniować i porównać [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) z listą akceptowalnych mechanizmów szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="11e61-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="11e61-411">Nie jest używane szyfrowanie z algorytmem szyfrowania [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="11e61-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="11e61-412">Filtrowanie połączeń można również skonfigurować za pomocą wyrażenia <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="11e61-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="11e61-413">W systemie Linux <xref:System.Net.Security.CipherSuitesPolicy> można używać do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń:</span><span class="sxs-lookup"><span data-stu-id="11e61-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="11e61-414">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="11e61-415">`CreateDefaultBuilder` wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="11e61-416">Poniższy *appsettings.jsna* przykład ustanawia protokołu HTTP/1.1 jako domyślny protokół połączenia dla wszystkich punktów końcowych:</span><span class="sxs-lookup"><span data-stu-id="11e61-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="11e61-417">Poniższy *appsettings.jsna* przykład ustanawia protokół połączeń HTTP/1.1 dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="11e61-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="11e61-418">Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="11e61-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="11e61-419">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="11e61-419">Transport configuration</span></span>

<span data-ttu-id="11e61-420">Dla projektów, które wymagają użycia Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="11e61-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="11e61-421">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11e61-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="11e61-422">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="11e61-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="11e61-423">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="11e61-423">URL prefixes</span></span>

<span data-ttu-id="11e61-424">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="11e61-425">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="11e61-426">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="11e61-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="11e61-427">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="11e61-428">`0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="11e61-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="11e61-429">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="11e61-430">`[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="11e61-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="11e61-431">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="11e61-432">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="11e61-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="11e61-433">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="11e61-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="11e61-434">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="11e61-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="11e61-435">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="11e61-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="11e61-436">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="11e61-437">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="11e61-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="11e61-438">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="11e61-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="11e61-439">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="11e61-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="11e61-440">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="11e61-440">Host filtering</span></span>

<span data-ttu-id="11e61-441">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="11e61-442">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="11e61-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="11e61-443">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="11e61-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="11e61-444">`Host` nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="11e61-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="11e61-445">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="11e61-446">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest niejawnie dostarczany dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="11e61-447">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="11e61-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="11e61-448">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="11e61-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="11e61-449">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.jsna* / *appSettings. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="11e61-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="11e61-450">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="11e61-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="11e61-451">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="11e61-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="11e61-452">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="11e61-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="11e61-453">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="11e61-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="11e61-454">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="11e61-455">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="11e61-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="11e61-456">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="11e61-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="11e61-457">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="11e61-458">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e61-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="11e61-459">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="11e61-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="11e61-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="11e61-460">HTTPS</span></span>
* <span data-ttu-id="11e61-461">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="11e61-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="11e61-462">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="11e61-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="11e61-463">HTTP/2 (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="11e61-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="11e61-464">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="11e61-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="11e61-465">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e61-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="11e61-466">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11e61-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="11e61-467">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="11e61-467">HTTP/2 support</span></span>

<span data-ttu-id="11e61-468">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="11e61-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="11e61-469">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="11e61-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="11e61-470">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="11e61-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="11e61-471">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="11e61-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="11e61-472">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="11e61-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="11e61-473">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="11e61-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="11e61-474">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="11e61-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="11e61-475">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="11e61-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="11e61-476">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="11e61-477">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="11e61-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="11e61-478">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="11e61-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="11e61-479">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="11e61-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="11e61-480">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="11e61-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="11e61-481">Więcej informacji na temat konfiguracji można znaleźć w sekcjach [Kestrel Options](#kestrel-options) and [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="11e61-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="11e61-482">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="11e61-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="11e61-483">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="11e61-483">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="11e61-484">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="11e61-485">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="11e61-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="11e61-487">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="11e61-487">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="11e61-489">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="11e61-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="11e61-490">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="11e61-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="11e61-491">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="11e61-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="11e61-492">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="11e61-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="11e61-493">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="11e61-494">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="11e61-494">A reverse proxy:</span></span>

* <span data-ttu-id="11e61-495">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="11e61-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="11e61-496">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="11e61-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="11e61-497">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="11e61-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="11e61-498">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="11e61-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="11e61-499">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="11e61-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="11e61-500">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="11e61-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="11e61-501">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e61-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="11e61-502">Pakiet [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="11e61-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="11e61-503">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="11e61-504">W *program.cs*, kod szablonu wywołuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , który wywołuje się w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> tle.</span><span class="sxs-lookup"><span data-stu-id="11e61-504">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="11e61-505">Więcej informacji na temat `CreateDefaultBuilder` i kompilowanie hosta znajduje się w sekcji *Konfigurowanie hosta* w temacie <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="11e61-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="11e61-506">Aby zapewnić dodatkową konfigurację po wywołaniu `CreateDefaultBuilder` , użyj `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="11e61-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="11e61-507">Jeśli aplikacja nie wywoła połączenia `CreateDefaultBuilder` w celu skonfigurowania hosta, wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **przed** wywołaniem `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="11e61-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="11e61-508">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="11e61-508">Kestrel options</span></span>

<span data-ttu-id="11e61-509">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="11e61-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="11e61-510">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="11e61-511">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="11e61-512">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="11e61-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="11e61-513">Opcje Kestrel, które są konfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="11e61-514">Na przykład dostawca konfiguracji plików może załadować konfigurację Kestrel z *appsettings.jsw* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="11e61-514">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="11e61-515">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="11e61-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="11e61-516">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="11e61-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="11e61-517">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="11e61-518">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="11e61-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="11e61-519">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="11e61-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="11e61-520">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="11e61-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="11e61-521">W programie *program.cs*Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="11e61-521">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="11e61-522">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="11e61-523">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="11e61-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="11e61-524">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="11e61-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="11e61-525">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="11e61-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="11e61-526">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="11e61-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="11e61-527">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="11e61-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="11e61-528">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="11e61-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="11e61-529">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="11e61-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="11e61-530">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="11e61-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="11e61-531">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="11e61-532">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="11e61-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="11e61-533">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="11e61-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="11e61-534">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="11e61-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="11e61-535">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="11e61-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="11e61-536">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="11e61-537">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="11e61-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="11e61-538">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="11e61-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="11e61-539">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="11e61-540">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="11e61-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="11e61-541">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="11e61-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="11e61-542">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="11e61-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="11e61-543">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="11e61-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="11e61-544">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="11e61-545">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="11e61-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="11e61-546">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="11e61-546">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="11e61-547">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="11e61-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="11e61-548">Funkcja rate, do której odwołuje się poprzednia próba, nie jest dostępna w `HttpContext.Features` przypadku żądań HTTP/2, ponieważ Modyfikowanie limitów szybkości dla poszczególnych żądań nie jest obsługiwane w przypadku protokołu HTTP/2 ze względu na obsługę multipleksera żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="11e61-549">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="11e61-550">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="11e61-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="11e61-551">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="11e61-552">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="11e61-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="11e61-553">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="11e61-553">Maximum streams per connection</span></span>

<span data-ttu-id="11e61-554">`Http2.MaxStreamsPerConnection` ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="11e61-555">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="11e61-556">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="11e61-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="11e61-557">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="11e61-557">Header table size</span></span>

<span data-ttu-id="11e61-558">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="11e61-559">`Http2.HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="11e61-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="11e61-560">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="11e61-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="11e61-561">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="11e61-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="11e61-562">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="11e61-562">Maximum frame size</span></span>

<span data-ttu-id="11e61-563">`Http2.MaxFrameSize` wskazuje maksymalny rozmiar ładunku ramki połączenia HTTP/2 do odebrania.</span><span class="sxs-lookup"><span data-stu-id="11e61-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="11e61-564">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="11e61-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="11e61-565">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="11e61-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="11e61-566">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-566">Maximum request header size</span></span>

<span data-ttu-id="11e61-567">`Http2.MaxRequestHeaderFieldSize` wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="11e61-568">Ten limit dotyczy zarówno nazwy, jak i wartości razem w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="11e61-569">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="11e61-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="11e61-570">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="11e61-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="11e61-571">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="11e61-571">Initial connection window size</span></span>

<span data-ttu-id="11e61-572">`Http2.InitialConnectionWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="11e61-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="11e61-573">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="11e61-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="11e61-574">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="11e61-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="11e61-575">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="11e61-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="11e61-576">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="11e61-576">Initial stream window size</span></span>

<span data-ttu-id="11e61-577">`Http2.InitialStreamWindowSize` wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="11e61-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="11e61-578">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="11e61-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="11e61-579">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="11e61-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="11e61-580">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="11e61-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="11e61-581">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="11e61-581">Synchronous I/O</span></span>

<span data-ttu-id="11e61-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="11e61-583">Wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="11e61-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="11e61-584">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="11e61-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="11e61-585">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="11e61-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="11e61-586">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="11e61-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="11e61-587">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="11e61-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="11e61-588">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="11e61-588">Endpoint configuration</span></span>

<span data-ttu-id="11e61-589">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="11e61-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="11e61-590">`https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="11e61-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="11e61-591">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="11e61-591">Specify URLs using the:</span></span>

* <span data-ttu-id="11e61-592">`ASPNETCORE_URLS` Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="11e61-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="11e61-593">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="11e61-594">`urls` klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="11e61-595">`UseUrls` Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="11e61-596">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="11e61-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="11e61-597">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="11e61-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="11e61-598">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="11e61-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="11e61-599">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="11e61-599">A development certificate is created:</span></span>

* <span data-ttu-id="11e61-600">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="11e61-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="11e61-601">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="11e61-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="11e61-602">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="11e61-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="11e61-603">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="11e61-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="11e61-604">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="11e61-605">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="11e61-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="11e61-606">`KestrelServerOptions` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="11e61-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="11e61-607">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="11e61-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="11e61-608">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="11e61-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="11e61-609">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="11e61-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="11e61-610">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="11e61-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="11e61-611">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="11e61-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="11e61-612">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="11e61-613">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="11e61-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="11e61-614">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="11e61-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="11e61-615">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="11e61-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="11e61-616">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="11e61-617">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="11e61-618">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="11e61-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="11e61-619">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="11e61-620">`ListenOptions.UseHttps` rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="11e61-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="11e61-621">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="11e61-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="11e61-622">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="11e61-622">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="11e61-623">`ListenOptions.UseHttps` wejściowe</span><span class="sxs-lookup"><span data-stu-id="11e61-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="11e61-624">`filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="11e61-625">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="11e61-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="11e61-626">`configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="11e61-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="11e61-627">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="11e61-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="11e61-628">`storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="11e61-629">`subject` to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="11e61-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="11e61-630">`allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="11e61-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="11e61-631">`location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="11e61-632">`serverCertificate` jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="11e61-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="11e61-633">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="11e61-634">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="11e61-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="11e61-635">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="11e61-635">Supported configurations described next:</span></span>

* <span data-ttu-id="11e61-636">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="11e61-636">No configuration</span></span>
* <span data-ttu-id="11e61-637">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="11e61-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="11e61-638">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="11e61-638">Change the defaults in code</span></span>

<span data-ttu-id="11e61-639">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-639">*No configuration*</span></span>

<span data-ttu-id="11e61-640">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="11e61-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="11e61-641">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="11e61-642">`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="11e61-643">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="11e61-644">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="11e61-645">W poniższym *appsettings.jsna* przykład:</span><span class="sxs-lookup"><span data-stu-id="11e61-645">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="11e61-646">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="11e61-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="11e61-647">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="11e61-647">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="11e61-648">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="11e61-649">Certyfikat domyślny można na **przykład**  >  **Default** określić jako:</span><span class="sxs-lookup"><span data-stu-id="11e61-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="11e61-650">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="11e61-650">Schema notes:</span></span>

* <span data-ttu-id="11e61-651">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="11e61-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="11e61-652">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="11e61-653">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="11e61-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="11e61-654">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="11e61-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="11e61-655">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="11e61-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="11e61-656">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="11e61-657">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="11e61-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="11e61-658">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="11e61-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="11e61-659">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="11e61-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="11e61-660">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash; **Password** , jak i certyfikaty magazynu **podmiotu** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="11e61-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="11e61-661">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="11e61-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="11e61-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="11e61-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="11e61-663">`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="11e61-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="11e61-664">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="11e61-665">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="11e61-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="11e61-666">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="11e61-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="11e61-667">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="11e61-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="11e61-668">`KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="11e61-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="11e61-669">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="11e61-670">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="11e61-670">*Change the defaults in code*</span></span>

<span data-ttu-id="11e61-671">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="11e61-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="11e61-672">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="11e61-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="11e61-673">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="11e61-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="11e61-674">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="11e61-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="11e61-675">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="11e61-676">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="11e61-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="11e61-677">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="11e61-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="11e61-678">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="11e61-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="11e61-679">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="11e61-679">SNI support requires:</span></span>

* <span data-ttu-id="11e61-680">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="11e61-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="11e61-681">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="11e61-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="11e61-682">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="11e61-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="11e61-683">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="11e61-684">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="11e61-685">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="11e61-685">Connection logging</span></span>

<span data-ttu-id="11e61-686">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="11e61-687">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="11e61-688">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="11e61-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="11e61-689">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="11e61-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="11e61-690">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="11e61-690">Bind to a TCP socket</span></span>

<span data-ttu-id="11e61-691"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="11e61-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="11e61-692">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="11e61-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="11e61-693">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="11e61-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="11e61-694">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="11e61-694">Bind to a Unix socket</span></span>

<span data-ttu-id="11e61-695">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="11e61-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="11e61-696">W pliku Nginx confiuguration Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="11e61-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="11e61-697">`{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="11e61-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="11e61-698">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="11e61-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="11e61-699">Port 0</span><span class="sxs-lookup"><span data-stu-id="11e61-699">Port 0</span></span>

<span data-ttu-id="11e61-700">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="11e61-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="11e61-701">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="11e61-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="11e61-702">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11e61-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="11e61-703">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="11e61-703">Limitations</span></span>

<span data-ttu-id="11e61-704">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="11e61-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="11e61-705">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="11e61-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="11e61-706">`urls` klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="11e61-706">`urls` host configuration key</span></span>
* <span data-ttu-id="11e61-707">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="11e61-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="11e61-708">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="11e61-709">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="11e61-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="11e61-710">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="11e61-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="11e61-711">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="11e61-712">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="11e61-712">IIS endpoint configuration</span></span>

<span data-ttu-id="11e61-713">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="11e61-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="11e61-714">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="11e61-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="11e61-715">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="11e61-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="11e61-716">`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="11e61-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="11e61-717">Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="11e61-718">`HttpProtocols` wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="11e61-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="11e61-719">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="11e61-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="11e61-720">Tylko HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-720">HTTP/1.1 only.</span></span> <span data-ttu-id="11e61-721">Może być używany z protokołem TLS lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="11e61-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="11e61-722">Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-722">HTTP/2 only.</span></span> <span data-ttu-id="11e61-723">Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="11e61-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="11e61-724">HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="11e61-725">Protokół HTTP/2 wymaga połączenia protokołów TLS i [warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="11e61-726">Domyślnym protokołem jest HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="11e61-727">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="11e61-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="11e61-728">TLS w wersji 1,2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="11e61-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="11e61-729">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="11e61-729">Renegotiation disabled</span></span>
* <span data-ttu-id="11e61-730">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="11e61-730">Compression disabled</span></span>
* <span data-ttu-id="11e61-731">Minimalne rozmiary tymczasowych kluczy wymiany:</span><span class="sxs-lookup"><span data-stu-id="11e61-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="11e61-732">Krzywa eliptyczna Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimum 224 bitów</span><span class="sxs-lookup"><span data-stu-id="11e61-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="11e61-733">Ograniczone pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="11e61-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="11e61-734">Nie zablokowano pakietu szyfrowania</span><span class="sxs-lookup"><span data-stu-id="11e61-734">Cipher suite not blocked</span></span>

<span data-ttu-id="11e61-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="11e61-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="11e61-736">Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="11e61-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="11e61-737">Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="11e61-737">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="11e61-738">Opcjonalnie można utworzyć `IConnectionAdapter` implementację do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów:</span><span class="sxs-lookup"><span data-stu-id="11e61-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="11e61-739">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="11e61-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="11e61-741">W poniższym *appsettings.jsna* przykład dla wszystkich punktów końcowych (http/1.1 i http/2) jest ustanawiany domyślny protokół połączeń:</span><span class="sxs-lookup"><span data-stu-id="11e61-741">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="11e61-742">Poniższy przykład pliku konfiguracji ustanawia protokół połączenia dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="11e61-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="11e61-743">Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="11e61-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="11e61-744">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="11e61-744">Transport configuration</span></span>

<span data-ttu-id="11e61-745">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="11e61-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="11e61-746">Jest to istotna zmiana dla aplikacji ASP.NET Core 2,0 uaktualniana do 2,1, które wywołują <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="11e61-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="11e61-747">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="11e61-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="11e61-748">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="11e61-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="11e61-749">Dla projektów, które wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="11e61-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="11e61-750">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11e61-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="11e61-751">Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="11e61-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="11e61-752">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="11e61-752">URL prefixes</span></span>

<span data-ttu-id="11e61-753">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="11e61-754">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="11e61-755">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="11e61-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="11e61-756">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="11e61-757">`0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="11e61-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="11e61-758">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="11e61-759">`[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="11e61-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="11e61-760">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="11e61-761">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="11e61-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="11e61-762">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="11e61-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="11e61-763">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="11e61-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="11e61-764">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="11e61-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="11e61-765">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="11e61-766">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="11e61-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="11e61-767">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="11e61-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="11e61-768">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="11e61-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="11e61-769">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="11e61-769">Host filtering</span></span>

<span data-ttu-id="11e61-770">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="11e61-771">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="11e61-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="11e61-772">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="11e61-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="11e61-773">`Host` nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="11e61-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="11e61-774">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="11e61-775">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest zawarty w pakiecie [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub 2,2).</span><span class="sxs-lookup"><span data-stu-id="11e61-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="11e61-776">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="11e61-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="11e61-777">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="11e61-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="11e61-778">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.jsna* / *appSettings. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="11e61-778">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="11e61-779">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="11e61-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="11e61-780">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="11e61-780">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="11e61-781">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="11e61-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="11e61-782">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="11e61-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="11e61-783">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="11e61-784">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="11e61-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="11e61-785">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="11e61-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="11e61-786">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="11e61-787">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e61-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="11e61-788">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="11e61-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="11e61-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="11e61-789">HTTPS</span></span>
* <span data-ttu-id="11e61-790">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="11e61-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="11e61-791">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="11e61-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="11e61-792">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="11e61-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="11e61-793">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11e61-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="11e61-794">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="11e61-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="11e61-795">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="11e61-795">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="11e61-796">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="11e61-797">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="11e61-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="11e61-799">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="11e61-799">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="11e61-801">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="11e61-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="11e61-802">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="11e61-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="11e61-803">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="11e61-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="11e61-804">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="11e61-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="11e61-805">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="11e61-806">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="11e61-806">A reverse proxy:</span></span>

* <span data-ttu-id="11e61-807">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="11e61-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="11e61-808">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="11e61-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="11e61-809">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="11e61-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="11e61-810">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="11e61-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="11e61-811">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="11e61-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="11e61-812">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="11e61-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="11e61-813">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e61-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="11e61-814">Pakiet [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="11e61-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="11e61-815">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="11e61-816">W *program.cs*, kod szablonu wywołuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , który wywołuje się w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> tle.</span><span class="sxs-lookup"><span data-stu-id="11e61-816">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="11e61-817">Aby zapewnić dodatkową konfigurację po wywołaniu `CreateDefaultBuilder` , wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="11e61-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="11e61-818">Więcej informacji na temat `CreateDefaultBuilder` i kompilowanie hosta znajduje się w sekcji *Konfigurowanie hosta* w temacie <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="11e61-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="11e61-819">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="11e61-819">Kestrel options</span></span>

<span data-ttu-id="11e61-820">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="11e61-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="11e61-821">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="11e61-822">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="11e61-823">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="11e61-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="11e61-824">Opcje Kestrel, które są konfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="11e61-825">Na przykład dostawca konfiguracji plików może załadować konfigurację Kestrel z *appsettings.jsw* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="11e61-825">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="11e61-826">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="11e61-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="11e61-827">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="11e61-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="11e61-828">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="11e61-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="11e61-829">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="11e61-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="11e61-830">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="11e61-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="11e61-831">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="11e61-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="11e61-832">W programie *program.cs*Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="11e61-832">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="11e61-833">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="11e61-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="11e61-834">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="11e61-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="11e61-835">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="11e61-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="11e61-836">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="11e61-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="11e61-837">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="11e61-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="11e61-838">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="11e61-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="11e61-839">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="11e61-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="11e61-840">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="11e61-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="11e61-841">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="11e61-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="11e61-842">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="11e61-843">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="11e61-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="11e61-844">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="11e61-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="11e61-845">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="11e61-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="11e61-846">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="11e61-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="11e61-847">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="11e61-848">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="11e61-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="11e61-849">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="11e61-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="11e61-850">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="11e61-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="11e61-851">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="11e61-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="11e61-852">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="11e61-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="11e61-853">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="11e61-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="11e61-854">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="11e61-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="11e61-855">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="11e61-856">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="11e61-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="11e61-857">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="11e61-857">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="11e61-858">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="11e61-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="11e61-859">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="11e61-860">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="11e61-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="11e61-861">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="11e61-861">Synchronous I/O</span></span>

<span data-ttu-id="11e61-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="11e61-863">Wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="11e61-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="11e61-864">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="11e61-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="11e61-865">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="11e61-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="11e61-866">Poniższy przykład wyłącza synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="11e61-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="11e61-867">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="11e61-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="11e61-868">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="11e61-868">Endpoint configuration</span></span>

<span data-ttu-id="11e61-869">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="11e61-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="11e61-870">`https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="11e61-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="11e61-871">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="11e61-871">Specify URLs using the:</span></span>

* <span data-ttu-id="11e61-872">`ASPNETCORE_URLS` Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="11e61-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="11e61-873">`--urls` argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="11e61-874">`urls` klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="11e61-875">`UseUrls` Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="11e61-876">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="11e61-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="11e61-877">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="11e61-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="11e61-878">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="11e61-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="11e61-879">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="11e61-879">A development certificate is created:</span></span>

* <span data-ttu-id="11e61-880">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="11e61-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="11e61-881">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="11e61-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="11e61-882">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="11e61-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="11e61-883">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="11e61-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="11e61-884">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="11e61-885">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="11e61-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="11e61-886">`KestrelServerOptions` skonfigurować</span><span class="sxs-lookup"><span data-stu-id="11e61-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="11e61-887">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="11e61-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="11e61-888">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="11e61-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="11e61-889">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="11e61-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="11e61-890">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="11e61-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="11e61-891">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="11e61-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="11e61-892">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="11e61-893">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="11e61-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="11e61-894">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="11e61-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="11e61-895">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="11e61-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="11e61-896">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="11e61-897">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="11e61-898">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="11e61-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="11e61-899">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="11e61-900">`ListenOptions.UseHttps` rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="11e61-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="11e61-901">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="11e61-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="11e61-902">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="11e61-902">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="11e61-903">`ListenOptions.UseHttps` wejściowe</span><span class="sxs-lookup"><span data-stu-id="11e61-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="11e61-904">`filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="11e61-905">`password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="11e61-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="11e61-906">`configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="11e61-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="11e61-907">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="11e61-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="11e61-908">`storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="11e61-909">`subject` to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="11e61-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="11e61-910">`allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="11e61-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="11e61-911">`location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="11e61-912">`serverCertificate` jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="11e61-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="11e61-913">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="11e61-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="11e61-914">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="11e61-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="11e61-915">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="11e61-915">Supported configurations described next:</span></span>

* <span data-ttu-id="11e61-916">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="11e61-916">No configuration</span></span>
* <span data-ttu-id="11e61-917">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="11e61-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="11e61-918">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="11e61-918">Change the defaults in code</span></span>

<span data-ttu-id="11e61-919">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-919">*No configuration*</span></span>

<span data-ttu-id="11e61-920">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="11e61-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="11e61-921">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="11e61-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="11e61-922">`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="11e61-923">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="11e61-924">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="11e61-925">W poniższym *appsettings.jsna* przykład:</span><span class="sxs-lookup"><span data-stu-id="11e61-925">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="11e61-926">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="11e61-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="11e61-927">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="11e61-927">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="11e61-928">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="11e61-929">Certyfikat domyślny można na **przykład**  >  **Default** określić jako:</span><span class="sxs-lookup"><span data-stu-id="11e61-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="11e61-930">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="11e61-930">Schema notes:</span></span>

* <span data-ttu-id="11e61-931">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="11e61-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="11e61-932">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="11e61-933">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="11e61-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="11e61-934">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="11e61-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="11e61-935">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="11e61-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="11e61-936">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="11e61-937">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="11e61-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="11e61-938">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="11e61-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="11e61-939">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="11e61-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="11e61-940">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash; **Password** , jak i certyfikaty magazynu **podmiotu** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="11e61-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="11e61-941">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="11e61-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="11e61-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="11e61-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="11e61-943">`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="11e61-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="11e61-944">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="11e61-945">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="11e61-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="11e61-946">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="11e61-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="11e61-947">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="11e61-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="11e61-948">`KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="11e61-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="11e61-949">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="11e61-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="11e61-950">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="11e61-950">*Change the defaults in code*</span></span>

<span data-ttu-id="11e61-951">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="11e61-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="11e61-952">`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="11e61-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="11e61-953">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="11e61-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="11e61-954">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="11e61-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="11e61-955">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="11e61-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="11e61-956">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="11e61-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="11e61-957">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="11e61-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="11e61-958">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="11e61-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="11e61-959">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="11e61-959">SNI support requires:</span></span>

* <span data-ttu-id="11e61-960">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="11e61-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="11e61-961">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="11e61-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="11e61-962">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="11e61-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="11e61-963">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="11e61-964">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="11e61-965">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="11e61-965">Connection logging</span></span>

<span data-ttu-id="11e61-966">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="11e61-967">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="11e61-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="11e61-968">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="11e61-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="11e61-969">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="11e61-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="11e61-970">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="11e61-970">Bind to a TCP socket</span></span>

<span data-ttu-id="11e61-971"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="11e61-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="11e61-972">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="11e61-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="11e61-973">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="11e61-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="11e61-974">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="11e61-974">Bind to a Unix socket</span></span>

<span data-ttu-id="11e61-975">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="11e61-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="11e61-976">W pliku Nginx confiuguration Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="11e61-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="11e61-977">`{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="11e61-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="11e61-978">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="11e61-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="11e61-979">Port 0</span><span class="sxs-lookup"><span data-stu-id="11e61-979">Port 0</span></span>

<span data-ttu-id="11e61-980">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="11e61-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="11e61-981">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="11e61-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="11e61-982">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11e61-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="11e61-983">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="11e61-983">Limitations</span></span>

<span data-ttu-id="11e61-984">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="11e61-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="11e61-985">`--urls` argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="11e61-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="11e61-986">`urls` klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="11e61-986">`urls` host configuration key</span></span>
* <span data-ttu-id="11e61-987">`ASPNETCORE_URLS` Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="11e61-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="11e61-988">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="11e61-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="11e61-989">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="11e61-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="11e61-990">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="11e61-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="11e61-991">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="11e61-992">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="11e61-992">IIS endpoint configuration</span></span>

<span data-ttu-id="11e61-993">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="11e61-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="11e61-994">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="11e61-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="11e61-995">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="11e61-995">Transport configuration</span></span>

<span data-ttu-id="11e61-996">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="11e61-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="11e61-997">Jest to istotna zmiana dla aplikacji ASP.NET Core 2,0 uaktualniana do 2,1, które wywołują <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="11e61-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="11e61-998">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="11e61-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="11e61-999">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="11e61-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="11e61-1000">Dla projektów, które wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="11e61-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="11e61-1001">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11e61-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="11e61-1002">Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="11e61-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="11e61-1003">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="11e61-1003">URL prefixes</span></span>

<span data-ttu-id="11e61-1004">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="11e61-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="11e61-1005">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="11e61-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="11e61-1006">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="11e61-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="11e61-1007">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="11e61-1008">`0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="11e61-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="11e61-1009">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="11e61-1010">`[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="11e61-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="11e61-1011">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="11e61-1012">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="11e61-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="11e61-1013">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="11e61-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="11e61-1014">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="11e61-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="11e61-1015">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="11e61-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="11e61-1016">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="11e61-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="11e61-1017">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="11e61-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="11e61-1018">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="11e61-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="11e61-1019">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="11e61-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="11e61-1020">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="11e61-1020">Host filtering</span></span>

<span data-ttu-id="11e61-1021">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="11e61-1022">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="11e61-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="11e61-1023">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="11e61-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="11e61-1024">`Host` nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="11e61-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="11e61-1025">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="11e61-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="11e61-1026">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest zawarty w pakiecie [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub 2,2).</span><span class="sxs-lookup"><span data-stu-id="11e61-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="11e61-1027">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="11e61-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="11e61-1028">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="11e61-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="11e61-1029">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.jsna* / *appSettings. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="11e61-1029">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="11e61-1030">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="11e61-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="11e61-1031">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="11e61-1031">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="11e61-1032">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="11e61-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="11e61-1033">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="11e61-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="11e61-1034">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="11e61-1035">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="11e61-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="11e61-1036">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="11e61-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="11e61-1037">Opróżnianie żądań HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="11e61-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="11e61-1038">Otwieranie połączeń HTTP jest czasochłonne.</span><span class="sxs-lookup"><span data-stu-id="11e61-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="11e61-1039">W przypadku protokołu HTTPS jest to również czasochłonne zasoby.</span><span class="sxs-lookup"><span data-stu-id="11e61-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="11e61-1040">W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="11e61-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="11e61-1041">Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="11e61-1042">Aplikacja nie zawsze zużywa treści żądania, na przykład żądania, w `POST` których serwer zwraca przekierowanie lub odpowiedź 404.</span><span class="sxs-lookup"><span data-stu-id="11e61-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="11e61-1043">W `POST` przypadku przekierowania — przypadek:</span><span class="sxs-lookup"><span data-stu-id="11e61-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="11e61-1044">Klient mógł już wysłać część `POST` danych.</span><span class="sxs-lookup"><span data-stu-id="11e61-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="11e61-1045">Serwer zapisuje odpowiedź 301.</span><span class="sxs-lookup"><span data-stu-id="11e61-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="11e61-1046">Nie można użyć połączenia dla nowego żądania, dopóki `POST` dane z poprzedniej treści żądania nie zostały w pełni odczytane.</span><span class="sxs-lookup"><span data-stu-id="11e61-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="11e61-1047">Kestrel próbuje opróżnić treść żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="11e61-1048">Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="11e61-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="11e61-1049">Proces opróżniania polega na tym, że tradoff między zezwoleniem na ponowne użycie połączenia i czasem potrzebny do opróżnienia pozostałych danych:</span><span class="sxs-lookup"><span data-stu-id="11e61-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="11e61-1050">Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.</span><span class="sxs-lookup"><span data-stu-id="11e61-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="11e61-1051">Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="11e61-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="11e61-1052">Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu.</span><span class="sxs-lookup"><span data-stu-id="11e61-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="11e61-1053">Na przykład klienci mogą mieć restrykcyjne limity danych, więc ograniczenie przekazywania danych może być priorytetem.</span><span class="sxs-lookup"><span data-stu-id="11e61-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="11e61-1054">W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="11e61-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="11e61-1055">Istnieją zastrzeżenia dotyczące wywoływania `Abort` :</span><span class="sxs-lookup"><span data-stu-id="11e61-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="11e61-1056">Tworzenie nowych połączeń może być powolne i kosztowne.</span><span class="sxs-lookup"><span data-stu-id="11e61-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="11e61-1057">Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="11e61-1058">Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.</span><span class="sxs-lookup"><span data-stu-id="11e61-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="11e61-1059">Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany.</span><span class="sxs-lookup"><span data-stu-id="11e61-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="11e61-1060">Na przykład Wywołaj, `Abort` czy złośliwi klienci próbują uzyskać `POST` dane, czy w kodzie klienta występuje usterka powodująca duże lub liczne żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="11e61-1061">Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="11e61-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="11e61-1062">Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11e61-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="11e61-1063">Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="11e61-1064">Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia.</span><span class="sxs-lookup"><span data-stu-id="11e61-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="11e61-1065">Nie ma zastosowania pięć sekund limitu czasu opróżniania.</span><span class="sxs-lookup"><span data-stu-id="11e61-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="11e61-1066">Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11e61-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="11e61-1067">Dodatkowe ramki danych treści żądania są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="11e61-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="11e61-1068">Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="11e61-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="11e61-1069">Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.</span><span class="sxs-lookup"><span data-stu-id="11e61-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11e61-1070">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="11e61-1070">Additional resources</span></span>

* <span data-ttu-id="11e61-1071">W przypadku korzystania z gniazd systemu UNIX w systemie Linux gniazdo nie jest automatycznie usuwane podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="11e61-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="11e61-1072">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/14134)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="11e61-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="11e61-1073">RFC 7230: Składnia i Routing komunikatu (sekcja 5,4: Host)</span><span class="sxs-lookup"><span data-stu-id="11e61-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
