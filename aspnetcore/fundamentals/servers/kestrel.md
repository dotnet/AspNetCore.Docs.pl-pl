---
title: Implementacja serwera www Kestrel w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o Kestrel, międzyplatformowym serwerze www dla ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 18846d60fd5c29f17cb4e59192795fd92251e2d0
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976771"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="1c964-103">Implementacja serwera www Kestrel w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c964-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="1c964-104">Przez [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher)i Stephen [Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="1c964-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1c964-105">Pustułka to wieloplatformowy [serwer www dla ASP.NET Core.](xref:fundamentals/servers/index)</span><span class="sxs-lookup"><span data-stu-id="1c964-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="1c964-106">Pustułka to serwer www, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="1c964-107">Pustułka obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="1c964-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="1c964-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1c964-108">HTTPS</span></span>
* <span data-ttu-id="1c964-109">Nieprzezroczyste uaktualnienie używane do włączania [websockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="1c964-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="1c964-110">Gniazda Unix o wysokiej wydajności za Nginx</span><span class="sxs-lookup"><span data-stu-id="1c964-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="1c964-111">HTTP/2 (z wyjątkiem&dagger;systemu macOS)</span><span class="sxs-lookup"><span data-stu-id="1c964-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="1c964-112">&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1c964-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="1c964-113">Pustułka jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez program .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="1c964-114">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c964-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="1c964-115">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1c964-115">HTTP/2 support</span></span>

<span data-ttu-id="1c964-116">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="1c964-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="1c964-117">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="1c964-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="1c964-118">Windows Server 2016/Windows 10 lub nowsze&Dagger;</span><span class="sxs-lookup"><span data-stu-id="1c964-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="1c964-119">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16.04 lub nowszym)</span><span class="sxs-lookup"><span data-stu-id="1c964-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="1c964-120">Struktura docelowa: .NET Core 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="1c964-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="1c964-121">Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="1c964-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="1c964-122">Połączenie TLS 1.2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="1c964-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="1c964-123">&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1c964-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="1c964-124">&Dagger;Pustułka ma ograniczoną obsługę protokołu HTTP/2 w systemach Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="1c964-125">Obsługa jest ograniczona, ponieważ lista obsługiwanych pakietów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="1c964-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="1c964-126">Certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego krzywej eliptycznej (ECDSA) może być wymagany do zabezpieczenia połączeń TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="1c964-127">Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="1c964-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="1c964-128">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="1c964-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="1c964-129">Aby uzyskać więcej informacji na temat konfiguracji, zobacz [opcje Kestrel](#kestrel-options) i [ListenOptions.Protocols](#listenoptionsprotocols) sekcje.</span><span class="sxs-lookup"><span data-stu-id="1c964-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="1c964-130">Kiedy używać Kestrel z odwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="1c964-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="1c964-131">Pustułka może być używana samodzielnie lub z *odwrotnym serwerem proxy,* takim jak [Internetowe Usługi Informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1c964-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="1c964-132">Serwer odwrotnego serwera proxy odbiera żądania HTTP z sieci i przekazuje je do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="1c964-133">Pustułka używana jako serwer internetowy edge (web-facing):</span><span class="sxs-lookup"><span data-stu-id="1c964-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="1c964-135">Pustułka używana w konfiguracji odwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="1c964-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="1c964-137">Konfiguracja z odwrotnym serwerem proxy lub bez niego jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="1c964-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="1c964-138">Pustułka używana jako serwer brzegowy bez odwrotnego serwera proxy nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="1c964-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="1c964-139">Gdy Kestrel jest skonfigurowany do nasłuchiwać na porcie, Kestrel obsługuje `Host` cały ruch dla tego portu, niezależnie od nagłówków żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="1c964-140">Odwrotny serwer proxy, który może udostępniać porty, ma możliwość przesyłania dalej żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="1c964-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="1c964-141">Nawet jeśli serwer odwrotnego serwera proxy nie jest wymagany, dobrym wyborem może być użycie odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="1c964-142">Odwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="1c964-142">A reverse proxy:</span></span>

* <span data-ttu-id="1c964-143">Można ograniczyć narażony obszar powierzchni publicznej aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="1c964-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="1c964-144">Zapewnij dodatkową warstwę konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="1c964-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="1c964-145">Może lepiej zintegrować się z istniejącą infrastrukturą.</span><span class="sxs-lookup"><span data-stu-id="1c964-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="1c964-146">Uprość równoważenie obciążenia i bezpieczną komunikację (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c964-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="1c964-147">Tylko serwer odwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1c964-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="1c964-148">Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1c964-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="1c964-149">Pustułka w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c964-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="1c964-150">ASP.NET szablony projektów Core domyślnie używają Pustułki.</span><span class="sxs-lookup"><span data-stu-id="1c964-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="1c964-151">W *Program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metoda wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*></span><span class="sxs-lookup"><span data-stu-id="1c964-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="1c964-152">Aby uzyskać więcej informacji na temat tworzenia hosta, zobacz sekcje <xref:fundamentals/host/generic-host#set-up-a-host> *Konfigurowanie hosta* i *Domyślne ustawienia konstruktora* w obszarze .</span><span class="sxs-lookup"><span data-stu-id="1c964-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="1c964-153">Aby zapewnić dodatkową `ConfigureWebHostDefaults`konfigurację `ConfigureKestrel`po wywołaniu , użyj :</span><span class="sxs-lookup"><span data-stu-id="1c964-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="1c964-154">Opcje pustułki</span><span class="sxs-lookup"><span data-stu-id="1c964-154">Kestrel options</span></span>

<span data-ttu-id="1c964-155">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w wdrożeniach internetowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="1c964-156">Ustaw ograniczenia właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="1c964-157">Właściwość `Limits` zawiera wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="1c964-158">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obszaru nazw:</span><span class="sxs-lookup"><span data-stu-id="1c964-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="1c964-159">W przykładach pokazano w dalszej części tego artykułu Opcje Kestrel są skonfigurowane w kodzie języka C#.</span><span class="sxs-lookup"><span data-stu-id="1c964-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="1c964-160">Opcje pustułki można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1c964-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="1c964-161">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację kestrelu z *pliku appsettings.json* lub *appsettings.{ Środowisko}.json* plik:</span><span class="sxs-lookup"><span data-stu-id="1c964-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="1c964-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfiguracji punktu końcowego](#endpoint-configuration) można konfigurować od dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="1c964-163">Pozostała konfiguracja pustułka musi być skonfigurowana w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="1c964-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="1c964-164">Użyj **jednego** z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="1c964-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="1c964-165">Skonfiguruj `Startup.ConfigureServices`Pustułkę w :</span><span class="sxs-lookup"><span data-stu-id="1c964-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="1c964-166">Wstrzyknąć `IConfiguration` `Startup` wystąpienie do klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="1c964-167">W poniższym przykładzie przyjęto założenie, że `Configuration` wstrzyknięta konfiguracja jest przypisana do właściwości.</span><span class="sxs-lookup"><span data-stu-id="1c964-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="1c964-168">W `Startup.ConfigureServices`programie `Kestrel` załaduj sekcję konfiguracji do konfiguracji Kestrela:</span><span class="sxs-lookup"><span data-stu-id="1c964-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="1c964-169">Konfigurowanie Pustułki podczas tworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="1c964-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="1c964-170">W *Program.cs*załaduj `Kestrel` sekcję konfiguracji do konfiguracji Kestrela:</span><span class="sxs-lookup"><span data-stu-id="1c964-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="1c964-171">Oba powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="1c964-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="1c964-172">Limit czasu utrzymywanie aktywności przy życiu</span><span class="sxs-lookup"><span data-stu-id="1c964-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="1c964-173">Pobiera lub ustawia [limit czasu keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="1c964-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="1c964-174">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="1c964-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="1c964-175">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="1c964-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="1c964-176">Maksymalną liczbę równoczesnych otwartych połączeń TCP można ustawić dla całej aplikacji za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1c964-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="1c964-177">Istnieje oddzielny limit dla połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu WebSockets).</span><span class="sxs-lookup"><span data-stu-id="1c964-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="1c964-178">Po uaktualnieniu połączenia nie jest wliczane `MaxConcurrentConnections` do limitu.</span><span class="sxs-lookup"><span data-stu-id="1c964-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="1c964-179">Maksymalna liczba połączeń jest domyślnie nieograniczona (zerowa).</span><span class="sxs-lookup"><span data-stu-id="1c964-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="1c964-180">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="1c964-181">Domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="1c964-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="1c964-182">Zalecane podejście do zastąpienia limitu w ASP.NET Core MVC aplikacji jest <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> użycie atrybutu w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="1c964-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="1c964-183">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji na każde żądanie:</span><span class="sxs-lookup"><span data-stu-id="1c964-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="1c964-184">Zastąp ustawienie dla określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="1c964-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="1c964-185">Wyjątek jest zgłaszany, jeśli aplikacja konfiguruje limit żądania po aplikacji zaczął odczytywać żądanie.</span><span class="sxs-lookup"><span data-stu-id="1c964-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="1c964-186">Istnieje `IsReadOnly` właściwość, która wskazuje, `MaxRequestBodySize` czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="1c964-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="1c964-187">Gdy aplikacja jest prowadzona [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [Core Module](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączona, ponieważ iIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="1c964-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="1c964-188">Minimalna szybkość danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="1c964-189">Pustułka sprawdza co sekundę, czy dane są przychodzące z określoną szybkością w bajtach/sekundę.</span><span class="sxs-lookup"><span data-stu-id="1c964-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="1c964-190">Jeśli szybkość spadnie poniżej minimum, upłynie limit czasu połączenia. Okres prolongaty to czas, który Kestrel daje klientowi na zwiększenie szybkości wysyłania do minimum; stawka nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="1c964-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="1c964-191">Okres prolongaty pomaga uniknąć porzucania połączeń, które początkowo wysyłają dane z powolnym tempem ze względu na spowolnienie tcp.</span><span class="sxs-lookup"><span data-stu-id="1c964-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="1c964-192">Domyślna minimalna stawka wynosi 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="1c964-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="1c964-193">Minimalna stawka ma również zastosowanie do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1c964-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="1c964-194">Kod, aby ustawić limit żądania i limit odpowiedzi `RequestBody` jest `Response` taka sama, z wyjątkiem posiadania lub w nazwy właściwości i interfejsu.</span><span class="sxs-lookup"><span data-stu-id="1c964-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="1c964-195">Oto przykład, który pokazuje, jak skonfigurować minimalne szybkości transmisji danych w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="1c964-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="1c964-196">Zastąp minimalne limity szybkości na żądanie w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="1c964-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="1c964-197">Odwołuje <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> się w poprzednim przykładzie `HttpContext.Features` nie jest obecny w dla żądań HTTP/2, ponieważ modyfikowanie limitów szybkości na podstawie żądania na ogół nie jest obsługiwany dla PROTOKOŁU HTTP/2 ze względu na obsługę protokołu dla multipleksowania żądania.</span><span class="sxs-lookup"><span data-stu-id="1c964-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="1c964-198">Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> jest nadal `HttpContext.Features` obecny dla żądań HTTP/2, ponieważ limit szybkości odczytu nadal można `IHttpMinRequestBodyDataRateFeature.MinDataRate` całkowicie `null` *wyłączyć* na podstawie żądania, ustawiając nawet dla żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="1c964-199">Próba odczytu `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby skonfigurowania go na `null` wartość inną `NotSupportedException` niż spowoduje, że zostanie wrzucony, biorąc pod uwagę żądanie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="1c964-200">Limity szybkości dla całego `KestrelServerOptions.Limits` serwera skonfigurowane za pośrednictwem nadal mają zastosowanie zarówno do połączeń HTTP/1.x, jak i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="1c964-201">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="1c964-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="1c964-202">Pobiera lub ustawia maksymalną ilość czasu serwer spędza odbieranie nagłówków żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="1c964-203">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="1c964-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="1c964-204">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="1c964-204">Maximum streams per connection</span></span>

<span data-ttu-id="1c964-205">`Http2.MaxStreamsPerConnection`ogranicza liczbę strumieni żądań równoczesnych na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="1c964-206">Nadmiar strumieni są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="1c964-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="1c964-207">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="1c964-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="1c964-208">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="1c964-208">Header table size</span></span>

<span data-ttu-id="1c964-209">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="1c964-210">`Http2.HeaderTableSize`ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="1c964-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="1c964-211">Wartość jest dostarczana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="1c964-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="1c964-212">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="1c964-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="1c964-213">Maksymalny rozmiar klatki</span><span class="sxs-lookup"><span data-stu-id="1c964-213">Maximum frame size</span></span>

<span data-ttu-id="1c964-214">`Http2.MaxFrameSize`wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer.</span><span class="sxs-lookup"><span data-stu-id="1c964-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="1c964-215">Wartość jest dostarczana w oktetach i musi zawierać się w przedziale od 2^14 (16 384) do 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="1c964-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="1c964-216">Wartość domyślna to 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="1c964-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="1c964-217">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-217">Maximum request header size</span></span>

<span data-ttu-id="1c964-218">`Http2.MaxRequestHeaderFieldSize`wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="1c964-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="1c964-219">Ten limit ma zastosowanie zarówno do nazwy, jak i wartości w ich skompresowanych i nieskompresowanych reprezentacjach.</span><span class="sxs-lookup"><span data-stu-id="1c964-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="1c964-220">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="1c964-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="1c964-221">Wartość domyślna to 8192.</span><span class="sxs-lookup"><span data-stu-id="1c964-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="1c964-222">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="1c964-222">Initial connection window size</span></span>

<span data-ttu-id="1c964-223">`Http2.InitialConnectionWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w tym samym czasie agregowane przez wszystkie żądania (strumienie) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="1c964-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="1c964-224">Liczba wniosków jest `Http2.InitialStreamWindowSize`ograniczona przez .</span><span class="sxs-lookup"><span data-stu-id="1c964-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="1c964-225">Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="1c964-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="1c964-226">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="1c964-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="1c964-227">Początkowy rozmiar okna strumienia</span><span class="sxs-lookup"><span data-stu-id="1c964-227">Initial stream window size</span></span>

<span data-ttu-id="1c964-228">`Http2.InitialStreamWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w jednym czasie na żądanie (strumień).</span><span class="sxs-lookup"><span data-stu-id="1c964-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="1c964-229">Liczba wniosków jest `Http2.InitialConnectionWindowSize`ograniczona przez .</span><span class="sxs-lookup"><span data-stu-id="1c964-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="1c964-230">Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="1c964-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="1c964-231">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="1c964-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="1c964-232">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="1c964-232">Synchronous I/O</span></span>

<span data-ttu-id="1c964-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>określa, czy synchroniczne we/wy są dozwolone dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1c964-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="1c964-234">Wartością domyślną jest `false`.</span><span class="sxs-lookup"><span data-stu-id="1c964-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="1c964-235">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do głodu puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="1c964-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="1c964-236">Włącz `AllowSynchronousIO` tylko podczas korzystania z biblioteki, która nie obsługuje asynchronicznych we/wy.</span><span class="sxs-lookup"><span data-stu-id="1c964-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="1c964-237">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="1c964-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="1c964-238">Aby uzyskać informacje o innych opcjach i ograniczeniach kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="1c964-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="1c964-239">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="1c964-239">Endpoint configuration</span></span>

<span data-ttu-id="1c964-240">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="1c964-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="1c964-241">`https://localhost:5001`(gdy certyfikat rozwoju lokalnego jest obecny)</span><span class="sxs-lookup"><span data-stu-id="1c964-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="1c964-242">Określ adresy URL za pomocą:</span><span class="sxs-lookup"><span data-stu-id="1c964-242">Specify URLs using the:</span></span>

* <span data-ttu-id="1c964-243">`ASPNETCORE_URLS`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="1c964-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="1c964-244">`--urls`argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="1c964-245">`urls`klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="1c964-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="1c964-246">`UseUrls`metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="1c964-247">Wartość podana przy użyciu tych metod może być co najmniej jeden punkt końcowy HTTP i HTTPS (HTTPS, jeśli dostępny jest domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="1c964-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="1c964-248">Skonfiguruj wartość jako listę oddzieloną średnikami `"Urls": "http://localhost:8000;http://localhost:8001"`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="1c964-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="1c964-249">Aby uzyskać więcej informacji na temat tych metod, zobacz [Adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastępowanie konfiguracji](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="1c964-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="1c964-250">Tworzony jest certyfikat dewelopera:</span><span class="sxs-lookup"><span data-stu-id="1c964-250">A development certificate is created:</span></span>

* <span data-ttu-id="1c964-251">Po zainstalowaniu [pliku .NET Core SDK.](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="1c964-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="1c964-252">[Narzędzie dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="1c964-253">Niektóre przeglądarki wymagają udzielenia jawnego uprawnienia do ufania certyfikatowi lokalnego rozwoju.</span><span class="sxs-lookup"><span data-stu-id="1c964-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="1c964-254">Szablony projektów domyślnie konfigurują aplikacje do uruchamiania na https i obejmują [przekierowanie HTTPS i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1c964-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="1c964-255"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody, aby skonfigurować prefiksy adresów URL i porty dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="1c964-256">`UseUrls`argument `--urls` wiersza polecenia, `urls` klucz konfiguracji hosta i zmienna `ASPNETCORE_URLS` środowiskowa również działają, ale mają ograniczenia odnotowane w dalszej części tej sekcji (domyślny certyfikat musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c964-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="1c964-257">`KestrelServerOptions`Konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="1c964-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="1c964-258">ConfigureEndpointDefaults(>)\<</span><span class="sxs-lookup"><span data-stu-id="1c964-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="1c964-259">Określa konfigurację `Action` uruchamianą dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1c964-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="1c964-260">Wywołanie `ConfigureEndpointDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.</span><span class="sxs-lookup"><span data-stu-id="1c964-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="1c964-261">Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="1c964-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="1c964-262">Konfigurowanie>httpsdefaults(akcja\<httpsconnectionadapteroptions)</span><span class="sxs-lookup"><span data-stu-id="1c964-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="1c964-263">Określa konfigurację `Action` uruchamianą dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c964-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="1c964-264">Wywołanie `ConfigureHttpsDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.</span><span class="sxs-lookup"><span data-stu-id="1c964-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="1c964-265">Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="1c964-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="1c964-266">Konfigurowanie(Konfiguracja IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="1c964-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="1c964-267">Tworzy moduł ładujący konfiguracji do konfigurowania <xref:Microsoft.Extensions.Configuration.IConfiguration> Kestrel, który przyjmuje jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="1c964-268">Konfiguracja musi być w zakresie do sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="1c964-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="1c964-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="1c964-270">Skonfiguruj Kestrel do używania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c964-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="1c964-271">`ListenOptions.UseHttps`Rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="1c964-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="1c964-272">`UseHttps`&ndash; Skonfiguruj Kestrel do używania protokołu HTTPS z certyfikatem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="1c964-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="1c964-273">Zgłasza wyjątek, jeśli nie skonfigurowano certyfikatu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="1c964-274">`ListenOptions.UseHttps`Parametry:</span><span class="sxs-lookup"><span data-stu-id="1c964-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="1c964-275">`filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1c964-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="1c964-276">`password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.</span><span class="sxs-lookup"><span data-stu-id="1c964-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="1c964-277">`configureOptions`jest `Action` skonfigurowaniem `HttpsConnectionAdapterOptions`pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="1c964-278">Zwraca `ListenOptions`wartość .</span><span class="sxs-lookup"><span data-stu-id="1c964-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="1c964-279">`storeName`to magazyn certyfikatów, z którego ma być załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="1c964-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="1c964-280">`subject`jest nazwą podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="1c964-281">`allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="1c964-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="1c964-282">`location`to lokalizacja magazynu, z wyładowania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="1c964-283">`serverCertificate`to certyfikat X.509.</span><span class="sxs-lookup"><span data-stu-id="1c964-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="1c964-284">W produkcji protokół HTTPS musi być jawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="1c964-285">Należy co najmniej dostarczyć certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="1c964-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="1c964-286">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="1c964-286">Supported configurations described next:</span></span>

* <span data-ttu-id="1c964-287">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="1c964-287">No configuration</span></span>
* <span data-ttu-id="1c964-288">Zastępowanie certyfikatu domyślnego z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="1c964-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="1c964-289">Zmienianie wartości domyślnych w kodzie</span><span class="sxs-lookup"><span data-stu-id="1c964-289">Change the defaults in code</span></span>

<span data-ttu-id="1c964-290">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-290">*No configuration*</span></span>

<span data-ttu-id="1c964-291">Kestrel `http://localhost:5000` nasłuchuje `https://localhost:5001` i (jeśli dostępny jest domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="1c964-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="1c964-292">*Zastępowanie certyfikatu domyślnego z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="1c964-293">`CreateDefaultBuilder`domyślnie `Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="1c964-294">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="1c964-295">Skonfiguruj wiele punktów końcowych, w tym adresy URL i certyfikaty do użycia, z pliku na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="1c964-296">W poniższym *przykładzie appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c964-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="1c964-297">Ustaw **AllowInvalid,** aby `true` zezwolić na używanie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="1c964-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="1c964-298">Każdy punkt końcowy HTTPS, który nie określa certyfikatu **(HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowanego w obszarze **Domyślne** **certyfikaty** > lub certyfikat dewelopera.</span><span class="sxs-lookup"><span data-stu-id="1c964-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="1c964-299">Alternatywą dla **używania ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="1c964-300">Na przykład certyfikat**domyślny** **certyfikat certyfikatów** > można określić jako:</span><span class="sxs-lookup"><span data-stu-id="1c964-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="1c964-301">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="1c964-301">Schema notes:</span></span>

* <span data-ttu-id="1c964-302">Nazwy punktów końcowych są bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="1c964-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="1c964-303">Na przykład `HTTPS` `Https` i są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="1c964-304">Parametr `Url` jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1c964-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="1c964-305">Format dla tego parametru jest taki `Urls` sam jak parametr konfiguracji najwyższego poziomu, z tą różnicą, że jest on ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="1c964-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="1c964-306">Te punkty końcowe zastępują te `Urls` zdefiniowane w konfiguracji najwyższego poziomu, a nie do ich dodawania.</span><span class="sxs-lookup"><span data-stu-id="1c964-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="1c964-307">Punkty końcowe zdefiniowane `Listen` w kodzie za pośrednictwem kumulują się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="1c964-308">Sekcja `Certificate` jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="1c964-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="1c964-309">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="1c964-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="1c964-310">Jeśli nie są dostępne żadne ustawienia domyślne, serwer zgłasza wyjątek i nie można go uruchomić.</span><span class="sxs-lookup"><span data-stu-id="1c964-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="1c964-311">Sekcja `Certificate` obsługuje **Path**&ndash;zarówno**certyfikaty Path Password, jak** i **Subject**&ndash;**Store.**</span><span class="sxs-lookup"><span data-stu-id="1c964-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="1c964-312">Dowolna liczba punktów końcowych może być zdefiniowana w ten sposób, o ile nie powodują konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="1c964-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="1c964-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` metodę, `.Endpoint(string name, listenOptions => { })` która może służyć do uzupełniania ustawień skonfigurowanego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="1c964-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="1c964-314">`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do dalszej iteracji na istniejącym ładowarce, takim jak ten dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>program .</span><span class="sxs-lookup"><span data-stu-id="1c964-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="1c964-315">Sekcja konfiguracji dla każdego punktu końcowego jest `Endpoint` dostępna na opcje w metodzie, dzięki czemu ustawienia niestandardowe mogą być odczytywane.</span><span class="sxs-lookup"><span data-stu-id="1c964-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="1c964-316">Wiele konfiguracji może być `options.Configure(context.Configuration.GetSection("{SECTION}"))` ładowanych przez ponowne wywołanie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="1c964-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="1c964-317">Używana jest tylko ostatnia `Load` konfiguracja, chyba że jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="1c964-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="1c964-318">Metapakiet nie jest `Load` wywoływany, więc jego domyślna sekcja konfiguracji może zostać zastąpiona.</span><span class="sxs-lookup"><span data-stu-id="1c964-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="1c964-319">`KestrelConfigurationLoader`dubluje `Listen` rodzinę interfejsów `KestrelServerOptions` `Endpoint` API z jako przeciążenia, więc kod i config punktów końcowych mogą być skonfigurowane w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="1c964-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="1c964-320">Te przeciążenia nie używają nazw i zużywają tylko ustawienia domyślne z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="1c964-321">*Zmienianie wartości domyślnych w kodzie*</span><span class="sxs-lookup"><span data-stu-id="1c964-321">*Change the defaults in code*</span></span>

<span data-ttu-id="1c964-322">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany `ListenOptions` `HttpsConnectionAdapterOptions`ustawień domyślnych dla i , w tym zastąpienie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="1c964-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="1c964-323">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="1c964-324">*Obsługa pustułki dla SNI*</span><span class="sxs-lookup"><span data-stu-id="1c964-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="1c964-325">[Wskazanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do obsługi wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="1c964-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="1c964-326">Aby usługa SNI działała, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania protokołu TLS, aby serwer mógł dostarczyć poprawny certyfikat.</span><span class="sxs-lookup"><span data-stu-id="1c964-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="1c964-327">Klient używa dostarczonego certyfikatu do szyfrowanej komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="1c964-328">Kestrel obsługuje SNI `ServerCertificateSelector` za pomocą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="1c964-329">Wywołanie zwrotne jest wywoływane raz na połączenie, aby umożliwić aplikacji sprawdzenie nazwy hosta i wybranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="1c964-330">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="1c964-330">SNI support requires:</span></span>

* <span data-ttu-id="1c964-331">Uruchamianie w `netcoreapp2.1` ramach docelowej lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="1c964-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="1c964-332">Włączone `net461` lub późniejsze wywołanie zwrotne `name` jest `null`wywoływane, ale jest zawsze .</span><span class="sxs-lookup"><span data-stu-id="1c964-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="1c964-333">Jest `name` również, `null` jeśli klient nie udostępnia parametru nazwy hosta w uzgadnianiu TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="1c964-334">Wszystkie strony internetowe działają w tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="1c964-335">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="1c964-336">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="1c964-336">Connection logging</span></span>

<span data-ttu-id="1c964-337">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> emitowania dzienników poziomu debugowania dla komunikacji na poziomie bajtów w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="1c964-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="1c964-338">Rejestrowanie połączeń jest przydatne do rozwiązywania problemów w komunikacji niskiego poziomu, takich jak podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="1c964-339">Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony przed , zaszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="1c964-340">Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony po , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="1c964-341">Powiązanie z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="1c964-341">Bind to a TCP socket</span></span>

<span data-ttu-id="1c964-342">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X.509:</span><span class="sxs-lookup"><span data-stu-id="1c964-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="1c964-343">W przykładzie konfiguruje protokół <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS dla punktu końcowego za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="1c964-344">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="1c964-345">Powiązanie z gniazdem Unixa</span><span class="sxs-lookup"><span data-stu-id="1c964-345">Bind to a Unix socket</span></span>

<span data-ttu-id="1c964-346">Słuchaj na gnieździe Unix z <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lepszej wydajności z Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1c964-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="1c964-347">W pliku konfiguracyjnym `server`  >  `location`  >  `proxy_pass` Nginx ustaw wpis na `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="1c964-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="1c964-348">`{KESTREL SOCKET}`jest nazwą gniazda dostarczonego <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (na `kestrel-test.sock` przykład w poprzednim przykładzie).</span><span class="sxs-lookup"><span data-stu-id="1c964-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="1c964-349">Upewnij się, że gniazdo jest zapisywalne przez `chmod go+w /tmp/kestrel-test.sock`Nginx (na przykład).</span><span class="sxs-lookup"><span data-stu-id="1c964-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="1c964-350">Port 0</span><span class="sxs-lookup"><span data-stu-id="1c964-350">Port 0</span></span>

<span data-ttu-id="1c964-351">Po określeniu numeru `0` portu Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="1c964-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="1c964-352">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie związany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="1c964-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="1c964-353">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, do którego można uzyskać kontakt z aplikacją:</span><span class="sxs-lookup"><span data-stu-id="1c964-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="1c964-354">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="1c964-354">Limitations</span></span>

<span data-ttu-id="1c964-355">Konfigurowanie punktów końcowych przy następujących podejściach:</span><span class="sxs-lookup"><span data-stu-id="1c964-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="1c964-356">`--urls`argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="1c964-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="1c964-357">`urls`klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="1c964-357">`urls` host configuration key</span></span>
* <span data-ttu-id="1c964-358">`ASPNETCORE_URLS`zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="1c964-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="1c964-359">Te metody są przydatne do tworzenia kodu pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="1c964-360">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="1c964-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="1c964-361">Protokołu HTTPS nie można używać z tymi podejściami, chyba że domyślny certyfikat jest `KestrelServerOptions` podany w konfiguracji punktu końcowego HTTPS (na przykład przy użyciu konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="1c964-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="1c964-362">Gdy zarówno `Listen` `UseUrls` i podejścia są używane `Listen` jednocześnie, punkty końcowe zastępują punkty `UseUrls` końcowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="1c964-363">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="1c964-363">IIS endpoint configuration</span></span>

<span data-ttu-id="1c964-364">Podczas korzystania z usługi IIS powiązania adresów URL dla powiązań `Listen` zastępowania usługi IIS są ustawiane przez jedną lub `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="1c964-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="1c964-365">Aby uzyskać więcej informacji, zobacz [ASP.NET temat modułu podstawowego.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="1c964-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="1c964-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="1c964-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="1c964-367">Właściwość `Protocols` ustanawia protokoły HTTP`HttpProtocols`( ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="1c964-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="1c964-368">Przypisz wartość `Protocols` do właściwości `HttpProtocols` z wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="1c964-369">`HttpProtocols`wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="1c964-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="1c964-370">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="1c964-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="1c964-371">Tylko http/1.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-371">HTTP/1.1 only.</span></span> <span data-ttu-id="1c964-372">Może być używany z tls lub bez.</span><span class="sxs-lookup"><span data-stu-id="1c964-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="1c964-373">Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-373">HTTP/2 only.</span></span> <span data-ttu-id="1c964-374">Może być używany bez protokołu TLS tylko wtedy, gdy klient obsługuje [tryb wcześniejszej wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="1c964-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="1c964-375">HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="1c964-376">Protokół HTTP/2 wymaga, aby klient wybrał protokół HTTP/2 w uzgadnianiu [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) protokołu TLS; w przeciwnym razie połączenie jest domyślnie dozwolone na HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="1c964-377">Wartością `ListenOptions.Protocols` domyślną dla `HttpProtocols.Http1AndHttp2`dowolnego punktu końcowego jest .</span><span class="sxs-lookup"><span data-stu-id="1c964-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="1c964-378">Ograniczenia TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="1c964-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="1c964-379">TLS w wersji 1.2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="1c964-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="1c964-380">Renegocjacja wyłączona</span><span class="sxs-lookup"><span data-stu-id="1c964-380">Renegotiation disabled</span></span>
* <span data-ttu-id="1c964-381">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="1c964-381">Compression disabled</span></span>
* <span data-ttu-id="1c964-382">Minimalne rozmiary efemerycznej wymiany kluczy:</span><span class="sxs-lookup"><span data-stu-id="1c964-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="1c964-383">Krzywa eliptyczna Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minimum</span><span class="sxs-lookup"><span data-stu-id="1c964-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="1c964-384">Pole skończone Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="1c964-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="1c964-385">Pakiet szyfrowania nie znajduje się na czarnej liście</span><span class="sxs-lookup"><span data-stu-id="1c964-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="1c964-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` z krzywą &rbrack; eliptyczną P-256 jest domyślnie obsługiwana. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="1c964-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="1c964-387">Poniższy przykład zezwala na połączenia HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="1c964-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="1c964-388">Połączenia są zabezpieczone tls z dostarczonym certyfikatem:</span><span class="sxs-lookup"><span data-stu-id="1c964-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="1c964-389">Użyj oprogramowania pośredniczącego połączenia, aby w razie potrzeby filtrować uzgadniania TLS dla poszczególnych szyfrów.</span><span class="sxs-lookup"><span data-stu-id="1c964-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="1c964-390">Poniższy przykład <xref:System.NotSupportedException> jest rzuty dla dowolnego algorytmu szyfrowania, który aplikacja nie obsługuje.</span><span class="sxs-lookup"><span data-stu-id="1c964-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="1c964-391">Alternatywnie zdefiniuj i porównaj [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) z listą dopuszczalnych mechanizmów szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="1c964-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="1c964-392">Szyfrowanie nie jest używane z algorytmem szyfrowania [CipherAlgorithmType.Null.](xref:System.Security.Authentication.CipherAlgorithmType)</span><span class="sxs-lookup"><span data-stu-id="1c964-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="1c964-393">Filtrowanie połączeń można również skonfigurować za pomocą <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="1c964-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="1c964-394">W systemie <xref:System.Net.Security.CipherSuitesPolicy> Linux, może służyć do filtrowania uzgadniania TLS na podstawie połączenia:</span><span class="sxs-lookup"><span data-stu-id="1c964-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="1c964-395">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="1c964-396">`CreateDefaultBuilder`domyślnie `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="1c964-397">Poniższy przykład *appsettings.json* ustanawia protokół HTTP/1.1 jako domyślny protokół połączenia dla wszystkich punktów końcowych:</span><span class="sxs-lookup"><span data-stu-id="1c964-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="1c964-398">Poniższy przykład *appsettings.json* ustanawia protokół połączenia HTTP/1.1 dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="1c964-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="1c964-399">Protokoły określone w kodzie zastępują wartości ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="1c964-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="1c964-400">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="1c964-400">Transport configuration</span></span>

<span data-ttu-id="1c964-401">W przypadku projektów wymagających stosowania<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>leku Libuv ( ):</span><span class="sxs-lookup"><span data-stu-id="1c964-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="1c964-402">Dodaj zależność pakietu [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1c964-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="1c964-403">Zadzwoń <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na: `IWebHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="1c964-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="1c964-404">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="1c964-404">URL prefixes</span></span>

<span data-ttu-id="1c964-405">W `UseUrls`przypadku `--urls` korzystania z `urls` argumentu wiersza `ASPNETCORE_URLS` polecenia, klucza konfiguracji hosta lub zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w dowolnym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="1c964-406">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="1c964-407">Pustułka nie obsługuje protokołu HTTPS podczas `UseUrls`konfigurowania powiązań adresów URL przy użyciu pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="1c964-408">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="1c964-409">`0.0.0.0`jest specjalnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="1c964-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="1c964-410">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="1c964-411">`[::]`jest odpowiednikiem IPv6 IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="1c964-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="1c964-412">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="1c964-413">Nazwy hostów `*` `+`, i , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="1c964-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="1c964-414">Wszystko, co nie jest `localhost` rozpoznawane jako prawidłowy adres IP lub wiąże się ze wszystkimi adresami IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="1c964-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="1c964-415">Aby powiązać różne nazwy hostów z różnymi aplikacjami ASP.NET Core na tym samym porcie, użyj [protokołu HTTP.sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="1c964-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="1c964-416">Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1c964-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="1c964-417">Nazwa `localhost` hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="1c964-418">Po `localhost` określeniu Kestrel próbuje powiązać interfejsy sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="1c964-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="1c964-419">Jeśli żądany port jest używany przez inną usługę w interfejsie sprzężenia zwrotnego, kestrel nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="1c964-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="1c964-420">Jeśli którykolwiek z interfejsów sprzężenia zwrotnego jest niedostępny z jakiegokolwiek innego powodu (najczęściej dlatego, że IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="1c964-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="1c964-421">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="1c964-421">Host filtering</span></span>

<span data-ttu-id="1c964-422">Gdy Kestrel obsługuje konfigurację opartą `http://example.com:5000`na prefiksach, takich jak Kestrel, w dużej mierze ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="1c964-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="1c964-423">Host `localhost` jest specjalnym przypadkiem używanym do wiązania adresów sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="1c964-424">Każdy host inny niż jawny adres IP wiąże się ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="1c964-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="1c964-425">`Host`nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="1c964-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="1c964-426">Aby obejść ten problem, użyj oprogramowania pośredniczącego filtrowania hostów.</span><span class="sxs-lookup"><span data-stu-id="1c964-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="1c964-427">Oprogramowanie pośredniczące filtrowania hosta jest dostarczane przez pakiet [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) który jest niejawnie przewidziany dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="1c964-428">Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>jest dodawane przez , który wywołuje: <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*></span><span class="sxs-lookup"><span data-stu-id="1c964-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="1c964-429">Oprogramowanie pośredniczące filtrowania hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="1c964-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="1c964-430">Aby włączyć oprogramowanie pośredniczące, `AllowedHosts` zdefiniuj klucz w *appsettings.json*/*appsettings.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="1c964-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="1c964-431">Wartość jest listą nazw hostów rozdzielanych średnikami bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="1c964-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="1c964-432">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1c964-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="1c964-433">[Oprogramowanie pośredniczące nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) również ma <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="1c964-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="1c964-434">Oprogramowanie pośredniczące nagłówków przesyłanych dalej i oprogramowanie pośredniczące filtrowania hostów mają podobną funkcjonalność w różnych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="1c964-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="1c964-435">Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego `Host` Nagłówków przesyłanych dalej jest właściwe, gdy nagłówek nie jest zachowywany podczas przekazywania żądań za pomocą serwera odwrotnego serwera proxy lub modułu równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="1c964-436">Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego filtrowania hostów jest właściwe, gdy `Host` Kestrel jest używany jako serwer brzegowy skierowany do publicznej wiadomości lub gdy nagłówek jest bezpośrednio przekazywaniem dalej.</span><span class="sxs-lookup"><span data-stu-id="1c964-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="1c964-437">Aby uzyskać więcej informacji na temat <xref:host-and-deploy/proxy-load-balancer>oprogramowania pośredniczącego nagłówków przesyłanych dalej, zobacz .</span><span class="sxs-lookup"><span data-stu-id="1c964-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1c964-438">Pustułka to wieloplatformowy [serwer www dla ASP.NET Core.](xref:fundamentals/servers/index)</span><span class="sxs-lookup"><span data-stu-id="1c964-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="1c964-439">Pustułka to serwer www, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="1c964-440">Pustułka obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="1c964-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="1c964-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1c964-441">HTTPS</span></span>
* <span data-ttu-id="1c964-442">Nieprzezroczyste uaktualnienie używane do włączania [websockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="1c964-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="1c964-443">Gniazda Unix o wysokiej wydajności za Nginx</span><span class="sxs-lookup"><span data-stu-id="1c964-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="1c964-444">HTTP/2 (z wyjątkiem&dagger;systemu macOS)</span><span class="sxs-lookup"><span data-stu-id="1c964-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="1c964-445">&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1c964-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="1c964-446">Pustułka jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez program .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="1c964-447">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c964-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="1c964-448">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1c964-448">HTTP/2 support</span></span>

<span data-ttu-id="1c964-449">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="1c964-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="1c964-450">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="1c964-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="1c964-451">Windows Server 2016/Windows 10 lub nowsze&Dagger;</span><span class="sxs-lookup"><span data-stu-id="1c964-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="1c964-452">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16.04 lub nowszym)</span><span class="sxs-lookup"><span data-stu-id="1c964-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="1c964-453">Struktura docelowa: .NET Core 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="1c964-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="1c964-454">Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="1c964-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="1c964-455">Połączenie TLS 1.2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="1c964-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="1c964-456">&dagger;Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1c964-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="1c964-457">&Dagger;Pustułka ma ograniczoną obsługę protokołu HTTP/2 w systemach Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="1c964-458">Obsługa jest ograniczona, ponieważ lista obsługiwanych pakietów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="1c964-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="1c964-459">Certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego krzywej eliptycznej (ECDSA) może być wymagany do zabezpieczenia połączeń TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="1c964-460">Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="1c964-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="1c964-461">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="1c964-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="1c964-462">Aby uzyskać więcej informacji na temat konfiguracji, zobacz [opcje Kestrel](#kestrel-options) i [ListenOptions.Protocols](#listenoptionsprotocols) sekcje.</span><span class="sxs-lookup"><span data-stu-id="1c964-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="1c964-463">Kiedy używać Kestrel z odwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="1c964-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="1c964-464">Pustułka może być używana samodzielnie lub z *odwrotnym serwerem proxy,* takim jak [Internetowe Usługi Informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1c964-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="1c964-465">Serwer odwrotnego serwera proxy odbiera żądania HTTP z sieci i przekazuje je do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="1c964-466">Pustułka używana jako serwer internetowy edge (web-facing):</span><span class="sxs-lookup"><span data-stu-id="1c964-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="1c964-468">Pustułka używana w konfiguracji odwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="1c964-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="1c964-470">Konfiguracja z odwrotnym serwerem proxy lub bez niego jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="1c964-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="1c964-471">Pustułka używana jako serwer brzegowy bez odwrotnego serwera proxy nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="1c964-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="1c964-472">Gdy Kestrel jest skonfigurowany do nasłuchiwać na porcie, Kestrel obsługuje `Host` cały ruch dla tego portu, niezależnie od nagłówków żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="1c964-473">Odwrotny serwer proxy, który może udostępniać porty, ma możliwość przesyłania dalej żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="1c964-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="1c964-474">Nawet jeśli serwer odwrotnego serwera proxy nie jest wymagany, dobrym wyborem może być użycie odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="1c964-475">Odwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="1c964-475">A reverse proxy:</span></span>

* <span data-ttu-id="1c964-476">Można ograniczyć narażony obszar powierzchni publicznej aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="1c964-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="1c964-477">Zapewnij dodatkową warstwę konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="1c964-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="1c964-478">Może lepiej zintegrować się z istniejącą infrastrukturą.</span><span class="sxs-lookup"><span data-stu-id="1c964-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="1c964-479">Uprość równoważenie obciążenia i bezpieczną komunikację (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c964-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="1c964-480">Tylko serwer odwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1c964-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="1c964-481">Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1c964-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="1c964-482">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c964-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="1c964-483">Pakiet [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1c964-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="1c964-484">ASP.NET szablony projektów Core domyślnie używają Pustułki.</span><span class="sxs-lookup"><span data-stu-id="1c964-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="1c964-485">W *Program.cs*, wywoła kod <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>szablonu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , który wywołuje za kulisami.</span><span class="sxs-lookup"><span data-stu-id="1c964-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="1c964-486">Aby uzyskać `CreateDefaultBuilder` więcej informacji na temat i tworzenia hosta, zobacz *sekcję Konfigurowanie hosta* w programie <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="1c964-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="1c964-487">Aby zapewnić dodatkową `CreateDefaultBuilder`konfigurację `ConfigureKestrel`po wywołaniu , użyj :</span><span class="sxs-lookup"><span data-stu-id="1c964-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="1c964-488">Jeśli aplikacja nie `CreateDefaultBuilder` wywołuje, aby skonfigurować <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> hosta, zadzwoń **przed** wywołaniem: `ConfigureKestrel`</span><span class="sxs-lookup"><span data-stu-id="1c964-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="1c964-489">Opcje pustułki</span><span class="sxs-lookup"><span data-stu-id="1c964-489">Kestrel options</span></span>

<span data-ttu-id="1c964-490">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w wdrożeniach internetowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="1c964-491">Ustaw ograniczenia właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="1c964-492">Właściwość `Limits` zawiera wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="1c964-493">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obszaru nazw:</span><span class="sxs-lookup"><span data-stu-id="1c964-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="1c964-494">Opcje pustułki, które są skonfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1c964-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="1c964-495">Na przykład dostawca konfiguracji plików może załadować konfigurację kestrelu z *pliku appsettings.json* lub *appsettings.{ Środowisko}.json* plik:</span><span class="sxs-lookup"><span data-stu-id="1c964-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="1c964-496">Użyj **jednego** z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="1c964-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="1c964-497">Skonfiguruj `Startup.ConfigureServices`Pustułkę w :</span><span class="sxs-lookup"><span data-stu-id="1c964-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="1c964-498">Wstrzyknąć `IConfiguration` `Startup` wystąpienie do klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="1c964-499">W poniższym przykładzie przyjęto założenie, że `Configuration` wstrzyknięta konfiguracja jest przypisana do właściwości.</span><span class="sxs-lookup"><span data-stu-id="1c964-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="1c964-500">W `Startup.ConfigureServices`programie `Kestrel` załaduj sekcję konfiguracji do konfiguracji Kestrela:</span><span class="sxs-lookup"><span data-stu-id="1c964-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="1c964-501">Konfigurowanie Pustułki podczas tworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="1c964-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="1c964-502">W *Program.cs*załaduj `Kestrel` sekcję konfiguracji do konfiguracji Kestrela:</span><span class="sxs-lookup"><span data-stu-id="1c964-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="1c964-503">Oba powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="1c964-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="1c964-504">Limit czasu utrzymywanie aktywności przy życiu</span><span class="sxs-lookup"><span data-stu-id="1c964-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="1c964-505">Pobiera lub ustawia [limit czasu keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="1c964-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="1c964-506">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="1c964-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="1c964-507">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="1c964-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="1c964-508">Maksymalną liczbę równoczesnych otwartych połączeń TCP można ustawić dla całej aplikacji za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1c964-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="1c964-509">Istnieje oddzielny limit dla połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu WebSockets).</span><span class="sxs-lookup"><span data-stu-id="1c964-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="1c964-510">Po uaktualnieniu połączenia nie jest wliczane `MaxConcurrentConnections` do limitu.</span><span class="sxs-lookup"><span data-stu-id="1c964-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="1c964-511">Maksymalna liczba połączeń jest domyślnie nieograniczona (zerowa).</span><span class="sxs-lookup"><span data-stu-id="1c964-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="1c964-512">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="1c964-513">Domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="1c964-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="1c964-514">Zalecane podejście do zastąpienia limitu w ASP.NET Core MVC aplikacji jest <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> użycie atrybutu w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="1c964-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="1c964-515">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji na każde żądanie:</span><span class="sxs-lookup"><span data-stu-id="1c964-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="1c964-516">Zastąp ustawienie dla określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="1c964-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="1c964-517">Wyjątek jest zgłaszany, jeśli aplikacja konfiguruje limit żądania po aplikacji zaczął odczytywać żądanie.</span><span class="sxs-lookup"><span data-stu-id="1c964-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="1c964-518">Istnieje `IsReadOnly` właściwość, która wskazuje, `MaxRequestBodySize` czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="1c964-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="1c964-519">Gdy aplikacja jest prowadzona [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [Core Module](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączona, ponieważ iIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="1c964-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="1c964-520">Minimalna szybkość danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="1c964-521">Pustułka sprawdza co sekundę, czy dane są przychodzące z określoną szybkością w bajtach/sekundę.</span><span class="sxs-lookup"><span data-stu-id="1c964-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="1c964-522">Jeśli szybkość spadnie poniżej minimum, upłynie limit czasu połączenia. Okres prolongaty to czas, który Kestrel daje klientowi na zwiększenie szybkości wysyłania do minimum; stawka nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="1c964-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="1c964-523">Okres prolongaty pomaga uniknąć porzucania połączeń, które początkowo wysyłają dane z powolnym tempem ze względu na spowolnienie tcp.</span><span class="sxs-lookup"><span data-stu-id="1c964-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="1c964-524">Domyślna minimalna stawka wynosi 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="1c964-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="1c964-525">Minimalna stawka ma również zastosowanie do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1c964-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="1c964-526">Kod, aby ustawić limit żądania i limit odpowiedzi `RequestBody` jest `Response` taka sama, z wyjątkiem posiadania lub w nazwy właściwości i interfejsu.</span><span class="sxs-lookup"><span data-stu-id="1c964-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="1c964-527">Oto przykład, który pokazuje, jak skonfigurować minimalne szybkości transmisji danych w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="1c964-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="1c964-528">Zastąp minimalne limity szybkości na żądanie w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="1c964-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="1c964-529">Żadna z funkcji rate, do których `HttpContext.Features` odwołuje się w poprzednim przykładzie, nie jest obecna w przypadku żądań HTTP/2, ponieważ modyfikowanie limitów szybkości dla na żądanie nie jest obsługiwane dla protokołu HTTP/2 ze względu na obsługę protokołu dla multipleksowania żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="1c964-530">Limity szybkości dla całego `KestrelServerOptions.Limits` serwera skonfigurowane za pośrednictwem nadal mają zastosowanie zarówno do połączeń HTTP/1.x, jak i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="1c964-531">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="1c964-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="1c964-532">Pobiera lub ustawia maksymalną ilość czasu serwer spędza odbieranie nagłówków żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="1c964-533">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="1c964-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="1c964-534">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="1c964-534">Maximum streams per connection</span></span>

<span data-ttu-id="1c964-535">`Http2.MaxStreamsPerConnection`ogranicza liczbę strumieni żądań równoczesnych na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="1c964-536">Nadmiar strumieni są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="1c964-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="1c964-537">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="1c964-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="1c964-538">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="1c964-538">Header table size</span></span>

<span data-ttu-id="1c964-539">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="1c964-540">`Http2.HeaderTableSize`ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="1c964-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="1c964-541">Wartość jest dostarczana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="1c964-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="1c964-542">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="1c964-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="1c964-543">Maksymalny rozmiar klatki</span><span class="sxs-lookup"><span data-stu-id="1c964-543">Maximum frame size</span></span>

<span data-ttu-id="1c964-544">`Http2.MaxFrameSize`wskazuje maksymalny rozmiar ładunku ramki połączenia HTTP/2 do odebrania.</span><span class="sxs-lookup"><span data-stu-id="1c964-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="1c964-545">Wartość jest dostarczana w oktetach i musi zawierać się w przedziale od 2^14 (16 384) do 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="1c964-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="1c964-546">Wartość domyślna to 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="1c964-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="1c964-547">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-547">Maximum request header size</span></span>

<span data-ttu-id="1c964-548">`Http2.MaxRequestHeaderFieldSize`wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="1c964-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="1c964-549">Ten limit ma zastosowanie zarówno do nazwy, jak i wartości razem w ich skompresowanych i nieskompresowanych reprezentacjach.</span><span class="sxs-lookup"><span data-stu-id="1c964-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="1c964-550">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="1c964-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="1c964-551">Wartość domyślna to 8192.</span><span class="sxs-lookup"><span data-stu-id="1c964-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="1c964-552">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="1c964-552">Initial connection window size</span></span>

<span data-ttu-id="1c964-553">`Http2.InitialConnectionWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w tym samym czasie agregowane przez wszystkie żądania (strumienie) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="1c964-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="1c964-554">Liczba wniosków jest `Http2.InitialStreamWindowSize`ograniczona przez .</span><span class="sxs-lookup"><span data-stu-id="1c964-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="1c964-555">Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="1c964-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="1c964-556">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="1c964-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="1c964-557">Początkowy rozmiar okna strumienia</span><span class="sxs-lookup"><span data-stu-id="1c964-557">Initial stream window size</span></span>

<span data-ttu-id="1c964-558">`Http2.InitialStreamWindowSize`wskazuje maksymalne dane treści żądania w bajtach buforów serwera w jednym czasie na żądanie (strumień).</span><span class="sxs-lookup"><span data-stu-id="1c964-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="1c964-559">Liczba wniosków jest `Http2.InitialStreamWindowSize`ograniczona przez .</span><span class="sxs-lookup"><span data-stu-id="1c964-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="1c964-560">Wartość musi być większa lub równa 65 535 i mniejsza niż 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="1c964-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="1c964-561">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="1c964-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="1c964-562">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="1c964-562">Synchronous I/O</span></span>

<span data-ttu-id="1c964-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>określa, czy synchroniczne we/wy są dozwolone dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1c964-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="1c964-564">Wartością domyślną jest `true`.</span><span class="sxs-lookup"><span data-stu-id="1c964-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="1c964-565">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do głodu puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="1c964-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="1c964-566">Włącz `AllowSynchronousIO` tylko podczas korzystania z biblioteki, która nie obsługuje asynchronicznych we/wy.</span><span class="sxs-lookup"><span data-stu-id="1c964-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="1c964-567">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="1c964-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="1c964-568">Aby uzyskać informacje o innych opcjach i ograniczeniach kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="1c964-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="1c964-569">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="1c964-569">Endpoint configuration</span></span>

<span data-ttu-id="1c964-570">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="1c964-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="1c964-571">`https://localhost:5001`(gdy certyfikat rozwoju lokalnego jest obecny)</span><span class="sxs-lookup"><span data-stu-id="1c964-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="1c964-572">Określ adresy URL za pomocą:</span><span class="sxs-lookup"><span data-stu-id="1c964-572">Specify URLs using the:</span></span>

* <span data-ttu-id="1c964-573">`ASPNETCORE_URLS`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="1c964-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="1c964-574">`--urls`argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="1c964-575">`urls`klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="1c964-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="1c964-576">`UseUrls`metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="1c964-577">Wartość podana przy użyciu tych metod może być co najmniej jeden punkt końcowy HTTP i HTTPS (HTTPS, jeśli dostępny jest domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="1c964-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="1c964-578">Skonfiguruj wartość jako listę oddzieloną średnikami `"Urls": "http://localhost:8000;http://localhost:8001"`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="1c964-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="1c964-579">Aby uzyskać więcej informacji na temat tych metod, zobacz [Adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastępowanie konfiguracji](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="1c964-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="1c964-580">Tworzony jest certyfikat dewelopera:</span><span class="sxs-lookup"><span data-stu-id="1c964-580">A development certificate is created:</span></span>

* <span data-ttu-id="1c964-581">Po zainstalowaniu [pliku .NET Core SDK.](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="1c964-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="1c964-582">[Narzędzie dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="1c964-583">Niektóre przeglądarki wymagają udzielenia jawnego uprawnienia do ufania certyfikatowi lokalnego rozwoju.</span><span class="sxs-lookup"><span data-stu-id="1c964-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="1c964-584">Szablony projektów domyślnie konfigurują aplikacje do uruchamiania na https i obejmują [przekierowanie HTTPS i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1c964-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="1c964-585"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody, aby skonfigurować prefiksy adresów URL i porty dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="1c964-586">`UseUrls`argument `--urls` wiersza polecenia, `urls` klucz konfiguracji hosta i zmienna `ASPNETCORE_URLS` środowiskowa również działają, ale mają ograniczenia odnotowane w dalszej części tej sekcji (domyślny certyfikat musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c964-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="1c964-587">`KestrelServerOptions`Konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="1c964-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="1c964-588">ConfigureEndpointDefaults(>)\<</span><span class="sxs-lookup"><span data-stu-id="1c964-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="1c964-589">Określa konfigurację `Action` uruchamianą dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1c964-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="1c964-590">Wywołanie `ConfigureEndpointDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.</span><span class="sxs-lookup"><span data-stu-id="1c964-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="1c964-591">Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="1c964-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="1c964-592">Konfigurowanie>httpsdefaults(akcja\<httpsconnectionadapteroptions)</span><span class="sxs-lookup"><span data-stu-id="1c964-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="1c964-593">Określa konfigurację `Action` uruchamianą dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c964-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="1c964-594">Wywołanie `ConfigureHttpsDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.</span><span class="sxs-lookup"><span data-stu-id="1c964-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="1c964-595">Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="1c964-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="1c964-596">Konfigurowanie(Konfiguracja IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="1c964-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="1c964-597">Tworzy moduł ładujący konfiguracji do konfigurowania <xref:Microsoft.Extensions.Configuration.IConfiguration> Kestrel, który przyjmuje jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="1c964-598">Konfiguracja musi być w zakresie do sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="1c964-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="1c964-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="1c964-600">Skonfiguruj Kestrel do używania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c964-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="1c964-601">`ListenOptions.UseHttps`Rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="1c964-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="1c964-602">`UseHttps`&ndash; Skonfiguruj Kestrel do używania protokołu HTTPS z certyfikatem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="1c964-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="1c964-603">Zgłasza wyjątek, jeśli nie skonfigurowano certyfikatu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="1c964-604">`ListenOptions.UseHttps`Parametry:</span><span class="sxs-lookup"><span data-stu-id="1c964-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="1c964-605">`filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1c964-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="1c964-606">`password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.</span><span class="sxs-lookup"><span data-stu-id="1c964-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="1c964-607">`configureOptions`jest `Action` skonfigurowaniem `HttpsConnectionAdapterOptions`pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="1c964-608">Zwraca `ListenOptions`wartość .</span><span class="sxs-lookup"><span data-stu-id="1c964-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="1c964-609">`storeName`to magazyn certyfikatów, z którego ma być załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="1c964-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="1c964-610">`subject`jest nazwą podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="1c964-611">`allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="1c964-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="1c964-612">`location`to lokalizacja magazynu, z wyładowania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="1c964-613">`serverCertificate`to certyfikat X.509.</span><span class="sxs-lookup"><span data-stu-id="1c964-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="1c964-614">W produkcji protokół HTTPS musi być jawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="1c964-615">Należy co najmniej dostarczyć certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="1c964-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="1c964-616">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="1c964-616">Supported configurations described next:</span></span>

* <span data-ttu-id="1c964-617">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="1c964-617">No configuration</span></span>
* <span data-ttu-id="1c964-618">Zastępowanie certyfikatu domyślnego z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="1c964-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="1c964-619">Zmienianie wartości domyślnych w kodzie</span><span class="sxs-lookup"><span data-stu-id="1c964-619">Change the defaults in code</span></span>

<span data-ttu-id="1c964-620">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-620">*No configuration*</span></span>

<span data-ttu-id="1c964-621">Kestrel `http://localhost:5000` nasłuchuje `https://localhost:5001` i (jeśli dostępny jest domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="1c964-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="1c964-622">*Zastępowanie certyfikatu domyślnego z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="1c964-623">`CreateDefaultBuilder`domyślnie `Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="1c964-624">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="1c964-625">Skonfiguruj wiele punktów końcowych, w tym adresy URL i certyfikaty do użycia, z pliku na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="1c964-626">W poniższym *przykładzie appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c964-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="1c964-627">Ustaw **AllowInvalid,** aby `true` zezwolić na używanie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="1c964-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="1c964-628">Każdy punkt końcowy HTTPS, który nie określa certyfikatu **(HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowanego w obszarze **Domyślne** **certyfikaty** > lub certyfikat dewelopera.</span><span class="sxs-lookup"><span data-stu-id="1c964-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="1c964-629">Alternatywą dla **używania ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="1c964-630">Na przykład certyfikat**domyślny** **certyfikat certyfikatów** > można określić jako:</span><span class="sxs-lookup"><span data-stu-id="1c964-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="1c964-631">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="1c964-631">Schema notes:</span></span>

* <span data-ttu-id="1c964-632">Nazwy punktów końcowych są bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="1c964-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="1c964-633">Na przykład `HTTPS` `Https` i są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="1c964-634">Parametr `Url` jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1c964-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="1c964-635">Format dla tego parametru jest taki `Urls` sam jak parametr konfiguracji najwyższego poziomu, z tą różnicą, że jest on ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="1c964-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="1c964-636">Te punkty końcowe zastępują te `Urls` zdefiniowane w konfiguracji najwyższego poziomu, a nie do ich dodawania.</span><span class="sxs-lookup"><span data-stu-id="1c964-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="1c964-637">Punkty końcowe zdefiniowane `Listen` w kodzie za pośrednictwem kumulują się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="1c964-638">Sekcja `Certificate` jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="1c964-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="1c964-639">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="1c964-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="1c964-640">Jeśli nie są dostępne żadne ustawienia domyślne, serwer zgłasza wyjątek i nie można go uruchomić.</span><span class="sxs-lookup"><span data-stu-id="1c964-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="1c964-641">Sekcja `Certificate` obsługuje **Path**&ndash;zarówno**certyfikaty Path Password, jak** i **Subject**&ndash;**Store.**</span><span class="sxs-lookup"><span data-stu-id="1c964-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="1c964-642">Dowolna liczba punktów końcowych może być zdefiniowana w ten sposób, o ile nie powodują konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="1c964-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="1c964-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` metodę, `.Endpoint(string name, listenOptions => { })` która może służyć do uzupełniania ustawień skonfigurowanego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="1c964-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="1c964-644">`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do dalszej iteracji na istniejącym ładowarce, takim jak ten dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>program .</span><span class="sxs-lookup"><span data-stu-id="1c964-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="1c964-645">Sekcja konfiguracji dla każdego punktu końcowego jest `Endpoint` dostępna na opcje w metodzie, dzięki czemu ustawienia niestandardowe mogą być odczytywane.</span><span class="sxs-lookup"><span data-stu-id="1c964-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="1c964-646">Wiele konfiguracji może być `options.Configure(context.Configuration.GetSection("{SECTION}"))` ładowanych przez ponowne wywołanie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="1c964-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="1c964-647">Używana jest tylko ostatnia `Load` konfiguracja, chyba że jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="1c964-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="1c964-648">Metapakiet nie jest `Load` wywoływany, więc jego domyślna sekcja konfiguracji może zostać zastąpiona.</span><span class="sxs-lookup"><span data-stu-id="1c964-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="1c964-649">`KestrelConfigurationLoader`dubluje `Listen` rodzinę interfejsów `KestrelServerOptions` `Endpoint` API z jako przeciążenia, więc kod i config punktów końcowych mogą być skonfigurowane w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="1c964-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="1c964-650">Te przeciążenia nie używają nazw i zużywają tylko ustawienia domyślne z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="1c964-651">*Zmienianie wartości domyślnych w kodzie*</span><span class="sxs-lookup"><span data-stu-id="1c964-651">*Change the defaults in code*</span></span>

<span data-ttu-id="1c964-652">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany `ListenOptions` `HttpsConnectionAdapterOptions`ustawień domyślnych dla i , w tym zastąpienie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="1c964-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="1c964-653">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="1c964-654">*Obsługa pustułki dla SNI*</span><span class="sxs-lookup"><span data-stu-id="1c964-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="1c964-655">[Wskazanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do obsługi wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="1c964-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="1c964-656">Aby usługa SNI działała, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania protokołu TLS, aby serwer mógł dostarczyć poprawny certyfikat.</span><span class="sxs-lookup"><span data-stu-id="1c964-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="1c964-657">Klient używa dostarczonego certyfikatu do szyfrowanej komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="1c964-658">Kestrel obsługuje SNI `ServerCertificateSelector` za pomocą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="1c964-659">Wywołanie zwrotne jest wywoływane raz na połączenie, aby umożliwić aplikacji sprawdzenie nazwy hosta i wybranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="1c964-660">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="1c964-660">SNI support requires:</span></span>

* <span data-ttu-id="1c964-661">Uruchamianie w `netcoreapp2.1` ramach docelowej lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="1c964-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="1c964-662">Włączone `net461` lub późniejsze wywołanie zwrotne `name` jest `null`wywoływane, ale jest zawsze .</span><span class="sxs-lookup"><span data-stu-id="1c964-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="1c964-663">Jest `name` również, `null` jeśli klient nie udostępnia parametru nazwy hosta w uzgadnianiu TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="1c964-664">Wszystkie strony internetowe działają w tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="1c964-665">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="1c964-666">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="1c964-666">Connection logging</span></span>

<span data-ttu-id="1c964-667">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> emitowania dzienników poziomu debugowania dla komunikacji na poziomie bajtów w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="1c964-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="1c964-668">Rejestrowanie połączeń jest przydatne do rozwiązywania problemów w komunikacji niskiego poziomu, takich jak podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="1c964-669">Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony przed , zaszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="1c964-670">Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony po , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="1c964-671">Powiązanie z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="1c964-671">Bind to a TCP socket</span></span>

<span data-ttu-id="1c964-672">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X.509:</span><span class="sxs-lookup"><span data-stu-id="1c964-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="1c964-673">W przykładzie konfiguruje protokół <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS dla punktu końcowego za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="1c964-674">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="1c964-675">Powiązanie z gniazdem Unixa</span><span class="sxs-lookup"><span data-stu-id="1c964-675">Bind to a Unix socket</span></span>

<span data-ttu-id="1c964-676">Słuchaj na gnieździe Unix z <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lepszej wydajności z Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1c964-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="1c964-677">W pliku nginx confiuguration `server`  >  `location`  >  `proxy_pass` ustaw `http://unix:/tmp/{KESTREL SOCKET}:/;`wpis na .</span><span class="sxs-lookup"><span data-stu-id="1c964-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="1c964-678">`{KESTREL SOCKET}`jest nazwą gniazda dostarczonego <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (na `kestrel-test.sock` przykład w poprzednim przykładzie).</span><span class="sxs-lookup"><span data-stu-id="1c964-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="1c964-679">Upewnij się, że gniazdo jest zapisywalne przez `chmod go+w /tmp/kestrel-test.sock`Nginx (na przykład).</span><span class="sxs-lookup"><span data-stu-id="1c964-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="1c964-680">Port 0</span><span class="sxs-lookup"><span data-stu-id="1c964-680">Port 0</span></span>

<span data-ttu-id="1c964-681">Po określeniu numeru `0` portu Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="1c964-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="1c964-682">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie związany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="1c964-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="1c964-683">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, do którego można uzyskać kontakt z aplikacją:</span><span class="sxs-lookup"><span data-stu-id="1c964-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="1c964-684">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="1c964-684">Limitations</span></span>

<span data-ttu-id="1c964-685">Konfigurowanie punktów końcowych przy następujących podejściach:</span><span class="sxs-lookup"><span data-stu-id="1c964-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="1c964-686">`--urls`argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="1c964-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="1c964-687">`urls`klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="1c964-687">`urls` host configuration key</span></span>
* <span data-ttu-id="1c964-688">`ASPNETCORE_URLS`zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="1c964-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="1c964-689">Te metody są przydatne do tworzenia kodu pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="1c964-690">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="1c964-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="1c964-691">Protokołu HTTPS nie można używać z tymi podejściami, chyba że domyślny certyfikat jest `KestrelServerOptions` podany w konfiguracji punktu końcowego HTTPS (na przykład przy użyciu konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="1c964-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="1c964-692">Gdy zarówno `Listen` `UseUrls` i podejścia są używane `Listen` jednocześnie, punkty końcowe zastępują punkty `UseUrls` końcowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="1c964-693">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="1c964-693">IIS endpoint configuration</span></span>

<span data-ttu-id="1c964-694">Podczas korzystania z usługi IIS powiązania adresów URL dla powiązań `Listen` zastępowania usługi IIS są ustawiane przez jedną lub `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="1c964-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="1c964-695">Aby uzyskać więcej informacji, zobacz [ASP.NET temat modułu podstawowego.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="1c964-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="1c964-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="1c964-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="1c964-697">Właściwość `Protocols` ustanawia protokoły HTTP`HttpProtocols`( ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="1c964-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="1c964-698">Przypisz wartość `Protocols` do właściwości `HttpProtocols` z wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="1c964-699">`HttpProtocols`wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="1c964-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="1c964-700">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="1c964-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="1c964-701">Tylko http/1.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-701">HTTP/1.1 only.</span></span> <span data-ttu-id="1c964-702">Może być używany z tls lub bez.</span><span class="sxs-lookup"><span data-stu-id="1c964-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="1c964-703">Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-703">HTTP/2 only.</span></span> <span data-ttu-id="1c964-704">Może być używany bez protokołu TLS tylko wtedy, gdy klient obsługuje [tryb wcześniejszej wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="1c964-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="1c964-705">HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c964-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="1c964-706">Protokół HTTP/2 wymaga połączenia TLS i negocjacji [protokołu warstwy aplikacji (ALPN);](https://tools.ietf.org/html/rfc7301#section-3) w przeciwnym razie połączenie jest domyślnie dozwolone na HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="1c964-707">Domyślnym protokołem jest HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1c964-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="1c964-708">Ograniczenia TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="1c964-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="1c964-709">TLS w wersji 1.2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="1c964-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="1c964-710">Renegocjacja wyłączona</span><span class="sxs-lookup"><span data-stu-id="1c964-710">Renegotiation disabled</span></span>
* <span data-ttu-id="1c964-711">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="1c964-711">Compression disabled</span></span>
* <span data-ttu-id="1c964-712">Minimalne rozmiary efemerycznej wymiany kluczy:</span><span class="sxs-lookup"><span data-stu-id="1c964-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="1c964-713">Krzywa eliptyczna Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minimum</span><span class="sxs-lookup"><span data-stu-id="1c964-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="1c964-714">Pole skończone Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="1c964-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="1c964-715">Pakiet szyfrowania nie znajduje się na czarnej liście</span><span class="sxs-lookup"><span data-stu-id="1c964-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="1c964-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` z krzywą &rbrack; eliptyczną P-256 jest domyślnie obsługiwana. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="1c964-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="1c964-717">Poniższy przykład zezwala na połączenia HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="1c964-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="1c964-718">Połączenia są zabezpieczone tls z dostarczonym certyfikatem:</span><span class="sxs-lookup"><span data-stu-id="1c964-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="1c964-719">Opcjonalnie utwórz `IConnectionAdapter` implementację do filtrowania uzgadniania TLS na podstawie połączenia dla określonych szyfrów:</span><span class="sxs-lookup"><span data-stu-id="1c964-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="1c964-720">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="1c964-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>domyślnie `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="1c964-722">W poniższym przykładzie *appsettings.json* dla wszystkich punktów końcowych Kestrel ustanawia się domyślny protokół połączenia (HTTP/1.1 i HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="1c964-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="1c964-723">Poniższy przykład pliku konfiguracji ustanawia protokół połączenia dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="1c964-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="1c964-724">Protokoły określone w kodzie zastępują wartości ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="1c964-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="1c964-725">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="1c964-725">Transport configuration</span></span>

<span data-ttu-id="1c964-726">Wraz z wydaniem ASP.NET Core 2.1 domyślny transport Kestrel nie jest już oparty na Libuv, ale zamiast tego opiera się na zarządzanych gniazdach.</span><span class="sxs-lookup"><span data-stu-id="1c964-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="1c964-727">Jest to przełomowa zmiana dla ASP.NET aplikacje Core 2.0 uaktualniania <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> do 2.1, które wywołują i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="1c964-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="1c964-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="1c964-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="1c964-729">Aplikacja Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="1c964-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="1c964-730">W przypadku projektów, które wymagają użycia libuv:</span><span class="sxs-lookup"><span data-stu-id="1c964-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="1c964-731">Dodaj zależność pakietu [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1c964-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="1c964-732">Zadzwoń <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="1c964-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="1c964-733">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="1c964-733">URL prefixes</span></span>

<span data-ttu-id="1c964-734">W `UseUrls`przypadku `--urls` korzystania z `urls` argumentu wiersza `ASPNETCORE_URLS` polecenia, klucza konfiguracji hosta lub zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w dowolnym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="1c964-735">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="1c964-736">Pustułka nie obsługuje protokołu HTTPS podczas `UseUrls`konfigurowania powiązań adresów URL przy użyciu pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="1c964-737">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="1c964-738">`0.0.0.0`jest specjalnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="1c964-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="1c964-739">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="1c964-740">`[::]`jest odpowiednikiem IPv6 IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="1c964-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="1c964-741">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="1c964-742">Nazwy hostów `*` `+`, i , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="1c964-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="1c964-743">Wszystko, co nie jest `localhost` rozpoznawane jako prawidłowy adres IP lub wiąże się ze wszystkimi adresami IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="1c964-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="1c964-744">Aby powiązać różne nazwy hostów z różnymi aplikacjami ASP.NET Core na tym samym porcie, użyj [protokołu HTTP.sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="1c964-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="1c964-745">Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1c964-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="1c964-746">Nazwa `localhost` hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="1c964-747">Po `localhost` określeniu Kestrel próbuje powiązać interfejsy sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="1c964-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="1c964-748">Jeśli żądany port jest używany przez inną usługę w interfejsie sprzężenia zwrotnego, kestrel nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="1c964-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="1c964-749">Jeśli którykolwiek z interfejsów sprzężenia zwrotnego jest niedostępny z jakiegokolwiek innego powodu (najczęściej dlatego, że IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="1c964-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="1c964-750">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="1c964-750">Host filtering</span></span>

<span data-ttu-id="1c964-751">Gdy Kestrel obsługuje konfigurację opartą `http://example.com:5000`na prefiksach, takich jak Kestrel, w dużej mierze ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="1c964-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="1c964-752">Host `localhost` jest specjalnym przypadkiem używanym do wiązania adresów sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="1c964-753">Każdy host inny niż jawny adres IP wiąże się ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="1c964-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="1c964-754">`Host`nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="1c964-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="1c964-755">Aby obejść ten problem, użyj oprogramowania pośredniczącego filtrowania hostów.</span><span class="sxs-lookup"><span data-stu-id="1c964-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="1c964-756">Oprogramowanie pośredniczące filtrowania hostów jest dostarczane przez pakiet [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub 2.2).</span><span class="sxs-lookup"><span data-stu-id="1c964-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="1c964-757">Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>jest dodawane przez , który wywołuje: <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*></span><span class="sxs-lookup"><span data-stu-id="1c964-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="1c964-758">Oprogramowanie pośredniczące filtrowania hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="1c964-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="1c964-759">Aby włączyć oprogramowanie pośredniczące, `AllowedHosts` zdefiniuj klucz w *appsettings.json*/*appsettings.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="1c964-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="1c964-760">Wartość jest listą nazw hostów rozdzielanych średnikami bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="1c964-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="1c964-761">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1c964-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="1c964-762">[Oprogramowanie pośredniczące nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) również ma <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="1c964-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="1c964-763">Oprogramowanie pośredniczące nagłówków przesyłanych dalej i oprogramowanie pośredniczące filtrowania hostów mają podobną funkcjonalność w różnych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="1c964-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="1c964-764">Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego `Host` Nagłówków przesyłanych dalej jest właściwe, gdy nagłówek nie jest zachowywany podczas przekazywania żądań za pomocą serwera odwrotnego serwera proxy lub modułu równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="1c964-765">Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego filtrowania hostów jest właściwe, gdy `Host` Kestrel jest używany jako serwer brzegowy skierowany do publicznej wiadomości lub gdy nagłówek jest bezpośrednio przekazywaniem dalej.</span><span class="sxs-lookup"><span data-stu-id="1c964-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="1c964-766">Aby uzyskać więcej informacji na temat <xref:host-and-deploy/proxy-load-balancer>oprogramowania pośredniczącego nagłówków przesyłanych dalej, zobacz .</span><span class="sxs-lookup"><span data-stu-id="1c964-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1c964-767">Pustułka to wieloplatformowy [serwer www dla ASP.NET Core.](xref:fundamentals/servers/index)</span><span class="sxs-lookup"><span data-stu-id="1c964-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="1c964-768">Pustułka to serwer www, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="1c964-769">Pustułka obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="1c964-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="1c964-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1c964-770">HTTPS</span></span>
* <span data-ttu-id="1c964-771">Nieprzezroczyste uaktualnienie używane do włączania [websockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="1c964-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="1c964-772">Gniazda Unix o wysokiej wydajności za Nginx</span><span class="sxs-lookup"><span data-stu-id="1c964-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="1c964-773">Pustułka jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez program .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c964-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="1c964-774">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c964-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="1c964-775">Kiedy używać Kestrel z odwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="1c964-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="1c964-776">Pustułka może być używana samodzielnie lub z *odwrotnym serwerem proxy,* takim jak [Internetowe Usługi Informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1c964-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="1c964-777">Serwer odwrotnego serwera proxy odbiera żądania HTTP z sieci i przekazuje je do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="1c964-778">Pustułka używana jako serwer internetowy edge (web-facing):</span><span class="sxs-lookup"><span data-stu-id="1c964-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="1c964-780">Pustułka używana w konfiguracji odwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="1c964-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="1c964-782">Konfiguracja z odwrotnym serwerem proxy lub bez niego jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="1c964-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="1c964-783">Pustułka używana jako serwer brzegowy bez odwrotnego serwera proxy nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="1c964-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="1c964-784">Gdy Kestrel jest skonfigurowany do nasłuchiwać na porcie, Kestrel obsługuje `Host` cały ruch dla tego portu, niezależnie od nagłówków żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="1c964-785">Odwrotny serwer proxy, który może udostępniać porty, ma możliwość przesyłania dalej żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="1c964-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="1c964-786">Nawet jeśli serwer odwrotnego serwera proxy nie jest wymagany, dobrym wyborem może być użycie odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="1c964-787">Odwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="1c964-787">A reverse proxy:</span></span>

* <span data-ttu-id="1c964-788">Można ograniczyć narażony obszar powierzchni publicznej aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="1c964-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="1c964-789">Zapewnij dodatkową warstwę konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="1c964-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="1c964-790">Może lepiej zintegrować się z istniejącą infrastrukturą.</span><span class="sxs-lookup"><span data-stu-id="1c964-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="1c964-791">Uprość równoważenie obciążenia i bezpieczną komunikację (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c964-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="1c964-792">Tylko serwer odwrotnego serwera proxy wymaga certyfikatu X.509, a ten serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1c964-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="1c964-793">Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1c964-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="1c964-794">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c964-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="1c964-795">Pakiet [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1c964-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="1c964-796">ASP.NET szablony projektów Core domyślnie używają Pustułki.</span><span class="sxs-lookup"><span data-stu-id="1c964-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="1c964-797">W *Program.cs*, wywoła kod <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>szablonu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , który wywołuje za kulisami.</span><span class="sxs-lookup"><span data-stu-id="1c964-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="1c964-798">Aby zapewnić dodatkową `CreateDefaultBuilder`konfigurację <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>po wywołaniu , zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="1c964-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="1c964-799">Aby uzyskać `CreateDefaultBuilder` więcej informacji na temat i tworzenia hosta, zobacz *sekcję Konfigurowanie hosta* w programie <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="1c964-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="1c964-800">Opcje pustułki</span><span class="sxs-lookup"><span data-stu-id="1c964-800">Kestrel options</span></span>

<span data-ttu-id="1c964-801">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w wdrożeniach internetowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="1c964-802">Ustaw ograniczenia właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="1c964-803">Właściwość `Limits` zawiera wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="1c964-804">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obszaru nazw:</span><span class="sxs-lookup"><span data-stu-id="1c964-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="1c964-805">Opcje pustułki, które są skonfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1c964-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="1c964-806">Na przykład dostawca konfiguracji plików może załadować konfigurację kestrelu z *pliku appsettings.json* lub *appsettings.{ Środowisko}.json* plik:</span><span class="sxs-lookup"><span data-stu-id="1c964-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="1c964-807">Użyj **jednego** z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="1c964-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="1c964-808">Skonfiguruj `Startup.ConfigureServices`Pustułkę w :</span><span class="sxs-lookup"><span data-stu-id="1c964-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="1c964-809">Wstrzyknąć `IConfiguration` `Startup` wystąpienie do klasy.</span><span class="sxs-lookup"><span data-stu-id="1c964-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="1c964-810">W poniższym przykładzie przyjęto założenie, że `Configuration` wstrzyknięta konfiguracja jest przypisana do właściwości.</span><span class="sxs-lookup"><span data-stu-id="1c964-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="1c964-811">W `Startup.ConfigureServices`programie `Kestrel` załaduj sekcję konfiguracji do konfiguracji Kestrela:</span><span class="sxs-lookup"><span data-stu-id="1c964-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="1c964-812">Konfigurowanie Pustułki podczas tworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="1c964-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="1c964-813">W *Program.cs*załaduj `Kestrel` sekcję konfiguracji do konfiguracji Kestrela:</span><span class="sxs-lookup"><span data-stu-id="1c964-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="1c964-814">Oba powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="1c964-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="1c964-815">Limit czasu utrzymywanie aktywności przy życiu</span><span class="sxs-lookup"><span data-stu-id="1c964-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="1c964-816">Pobiera lub ustawia [limit czasu keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="1c964-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="1c964-817">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="1c964-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="1c964-818">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="1c964-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="1c964-819">Maksymalną liczbę równoczesnych otwartych połączeń TCP można ustawić dla całej aplikacji za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1c964-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="1c964-820">Istnieje oddzielny limit dla połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu WebSockets).</span><span class="sxs-lookup"><span data-stu-id="1c964-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="1c964-821">Po uaktualnieniu połączenia nie jest wliczane `MaxConcurrentConnections` do limitu.</span><span class="sxs-lookup"><span data-stu-id="1c964-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="1c964-822">Maksymalna liczba połączeń jest domyślnie nieograniczona (zerowa).</span><span class="sxs-lookup"><span data-stu-id="1c964-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="1c964-823">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="1c964-824">Domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="1c964-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="1c964-825">Zalecane podejście do zastąpienia limitu w ASP.NET Core MVC aplikacji jest <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> użycie atrybutu w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="1c964-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="1c964-826">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji na każde żądanie:</span><span class="sxs-lookup"><span data-stu-id="1c964-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="1c964-827">Zastąp ustawienie dla określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="1c964-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="1c964-828">Wyjątek jest zgłaszany, jeśli aplikacja konfiguruje limit żądania po aplikacji zaczął odczytywać żądanie.</span><span class="sxs-lookup"><span data-stu-id="1c964-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="1c964-829">Istnieje `IsReadOnly` właściwość, która wskazuje, `MaxRequestBodySize` czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="1c964-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="1c964-830">Gdy aplikacja jest prowadzona [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [Core Module](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączona, ponieważ iIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="1c964-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="1c964-831">Minimalna szybkość danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="1c964-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="1c964-832">Pustułka sprawdza co sekundę, czy dane są przychodzące z określoną szybkością w bajtach/sekundę.</span><span class="sxs-lookup"><span data-stu-id="1c964-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="1c964-833">Jeśli szybkość spadnie poniżej minimum, upłynie limit czasu połączenia. Okres prolongaty to czas, który Kestrel daje klientowi na zwiększenie szybkości wysyłania do minimum; stawka nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="1c964-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="1c964-834">Okres prolongaty pomaga uniknąć porzucania połączeń, które początkowo wysyłają dane z powolnym tempem ze względu na spowolnienie tcp.</span><span class="sxs-lookup"><span data-stu-id="1c964-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="1c964-835">Domyślna minimalna stawka wynosi 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="1c964-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="1c964-836">Minimalna stawka ma również zastosowanie do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1c964-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="1c964-837">Kod, aby ustawić limit żądania i limit odpowiedzi `RequestBody` jest `Response` taka sama, z wyjątkiem posiadania lub w nazwy właściwości i interfejsu.</span><span class="sxs-lookup"><span data-stu-id="1c964-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="1c964-838">Oto przykład, który pokazuje, jak skonfigurować minimalne szybkości transmisji danych w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="1c964-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="1c964-839">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="1c964-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="1c964-840">Pobiera lub ustawia maksymalną ilość czasu serwer spędza odbieranie nagłówków żądań.</span><span class="sxs-lookup"><span data-stu-id="1c964-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="1c964-841">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="1c964-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="1c964-842">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="1c964-842">Synchronous I/O</span></span>

<span data-ttu-id="1c964-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>określa, czy synchroniczne we/wy są dozwolone dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1c964-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="1c964-844">Wartością domyślną jest `true`.</span><span class="sxs-lookup"><span data-stu-id="1c964-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="1c964-845">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do głodu puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="1c964-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="1c964-846">Włącz `AllowSynchronousIO` tylko podczas korzystania z biblioteki, która nie obsługuje asynchronicznych we/wy.</span><span class="sxs-lookup"><span data-stu-id="1c964-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="1c964-847">Poniższy przykład wyłącza synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="1c964-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="1c964-848">Aby uzyskać informacje o innych opcjach i ograniczeniach kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="1c964-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="1c964-849">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="1c964-849">Endpoint configuration</span></span>

<span data-ttu-id="1c964-850">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="1c964-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="1c964-851">`https://localhost:5001`(gdy certyfikat rozwoju lokalnego jest obecny)</span><span class="sxs-lookup"><span data-stu-id="1c964-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="1c964-852">Określ adresy URL za pomocą:</span><span class="sxs-lookup"><span data-stu-id="1c964-852">Specify URLs using the:</span></span>

* <span data-ttu-id="1c964-853">`ASPNETCORE_URLS`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="1c964-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="1c964-854">`--urls`argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="1c964-855">`urls`klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="1c964-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="1c964-856">`UseUrls`metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="1c964-857">Wartość podana przy użyciu tych metod może być co najmniej jeden punkt końcowy HTTP i HTTPS (HTTPS, jeśli dostępny jest domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="1c964-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="1c964-858">Skonfiguruj wartość jako listę oddzieloną średnikami `"Urls": "http://localhost:8000;http://localhost:8001"`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="1c964-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="1c964-859">Aby uzyskać więcej informacji na temat tych metod, zobacz [Adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastępowanie konfiguracji](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="1c964-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="1c964-860">Tworzony jest certyfikat dewelopera:</span><span class="sxs-lookup"><span data-stu-id="1c964-860">A development certificate is created:</span></span>

* <span data-ttu-id="1c964-861">Po zainstalowaniu [pliku .NET Core SDK.](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="1c964-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="1c964-862">[Narzędzie dev-certs](xref:aspnetcore-2.1#https) służy do tworzenia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="1c964-863">Niektóre przeglądarki wymagają udzielenia jawnego uprawnienia do ufania certyfikatowi lokalnego rozwoju.</span><span class="sxs-lookup"><span data-stu-id="1c964-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="1c964-864">Szablony projektów domyślnie konfigurują aplikacje do uruchamiania na https i obejmują [przekierowanie HTTPS i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1c964-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="1c964-865"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody, aby skonfigurować prefiksy adresów URL i porty dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="1c964-866">`UseUrls`argument `--urls` wiersza polecenia, `urls` klucz konfiguracji hosta i zmienna `ASPNETCORE_URLS` środowiskowa również działają, ale mają ograniczenia odnotowane w dalszej części tej sekcji (domyślny certyfikat musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c964-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="1c964-867">`KestrelServerOptions`Konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="1c964-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="1c964-868">ConfigureEndpointDefaults(>)\<</span><span class="sxs-lookup"><span data-stu-id="1c964-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="1c964-869">Określa konfigurację `Action` uruchamianą dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1c964-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="1c964-870">Wywołanie `ConfigureEndpointDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.</span><span class="sxs-lookup"><span data-stu-id="1c964-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="1c964-871">Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="1c964-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="1c964-872">Konfigurowanie>httpsdefaults(akcja\<httpsconnectionadapteroptions)</span><span class="sxs-lookup"><span data-stu-id="1c964-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="1c964-873">Określa konfigurację `Action` uruchamianą dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c964-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="1c964-874">Wywołanie `ConfigureHttpsDefaults` wiele razy `Action`zastępuje wcześniejsze `Action` s z ostatnim określonym.</span><span class="sxs-lookup"><span data-stu-id="1c964-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="1c964-875">Punkty końcowe utworzone <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> przez <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> wywołanie **przed** wywołaniem nie będą miały zastosowanych wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="1c964-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="1c964-876">Konfigurowanie(Konfiguracja IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="1c964-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="1c964-877">Tworzy moduł ładujący konfiguracji do konfigurowania <xref:Microsoft.Extensions.Configuration.IConfiguration> Kestrel, który przyjmuje jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="1c964-878">Konfiguracja musi być w zakresie do sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="1c964-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="1c964-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="1c964-880">Skonfiguruj Kestrel do używania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c964-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="1c964-881">`ListenOptions.UseHttps`Rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="1c964-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="1c964-882">`UseHttps`&ndash; Skonfiguruj Kestrel do używania protokołu HTTPS z certyfikatem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="1c964-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="1c964-883">Zgłasza wyjątek, jeśli nie skonfigurowano certyfikatu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="1c964-884">`ListenOptions.UseHttps`Parametry:</span><span class="sxs-lookup"><span data-stu-id="1c964-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="1c964-885">`filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1c964-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="1c964-886">`password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X.509.</span><span class="sxs-lookup"><span data-stu-id="1c964-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="1c964-887">`configureOptions`jest `Action` skonfigurowaniem `HttpsConnectionAdapterOptions`pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="1c964-888">Zwraca `ListenOptions`wartość .</span><span class="sxs-lookup"><span data-stu-id="1c964-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="1c964-889">`storeName`to magazyn certyfikatów, z którego ma być załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="1c964-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="1c964-890">`subject`jest nazwą podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="1c964-891">`allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="1c964-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="1c964-892">`location`to lokalizacja magazynu, z wyładowania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="1c964-893">`serverCertificate`to certyfikat X.509.</span><span class="sxs-lookup"><span data-stu-id="1c964-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="1c964-894">W produkcji protokół HTTPS musi być jawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="1c964-895">Należy co najmniej dostarczyć certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="1c964-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="1c964-896">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="1c964-896">Supported configurations described next:</span></span>

* <span data-ttu-id="1c964-897">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="1c964-897">No configuration</span></span>
* <span data-ttu-id="1c964-898">Zastępowanie certyfikatu domyślnego z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="1c964-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="1c964-899">Zmienianie wartości domyślnych w kodzie</span><span class="sxs-lookup"><span data-stu-id="1c964-899">Change the defaults in code</span></span>

<span data-ttu-id="1c964-900">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-900">*No configuration*</span></span>

<span data-ttu-id="1c964-901">Kestrel `http://localhost:5000` nasłuchuje `https://localhost:5001` i (jeśli dostępny jest domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="1c964-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="1c964-902">*Zastępowanie certyfikatu domyślnego z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="1c964-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="1c964-903">`CreateDefaultBuilder`domyślnie `Configure(context.Configuration.GetSection("Kestrel"))` załadować konfigurację Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="1c964-904">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="1c964-905">Skonfiguruj wiele punktów końcowych, w tym adresy URL i certyfikaty do użycia, z pliku na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="1c964-906">W poniższym *przykładzie appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1c964-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="1c964-907">Ustaw **AllowInvalid,** aby `true` zezwolić na używanie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="1c964-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="1c964-908">Każdy punkt końcowy HTTPS, który nie określa certyfikatu **(HttpsDefaultCert** w poniższym przykładzie) powraca do certyfikatu zdefiniowanego w obszarze **Domyślne** **certyfikaty** > lub certyfikat dewelopera.</span><span class="sxs-lookup"><span data-stu-id="1c964-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="1c964-909">Alternatywą dla **używania ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="1c964-910">Na przykład certyfikat**domyślny** **certyfikat certyfikatów** > można określić jako:</span><span class="sxs-lookup"><span data-stu-id="1c964-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="1c964-911">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="1c964-911">Schema notes:</span></span>

* <span data-ttu-id="1c964-912">Nazwy punktów końcowych są bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="1c964-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="1c964-913">Na przykład `HTTPS` `Https` i są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="1c964-914">Parametr `Url` jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1c964-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="1c964-915">Format dla tego parametru jest taki `Urls` sam jak parametr konfiguracji najwyższego poziomu, z tą różnicą, że jest on ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="1c964-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="1c964-916">Te punkty końcowe zastępują te `Urls` zdefiniowane w konfiguracji najwyższego poziomu, a nie do ich dodawania.</span><span class="sxs-lookup"><span data-stu-id="1c964-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="1c964-917">Punkty końcowe zdefiniowane `Listen` w kodzie za pośrednictwem kumulują się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="1c964-918">Sekcja `Certificate` jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="1c964-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="1c964-919">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="1c964-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="1c964-920">Jeśli nie są dostępne żadne ustawienia domyślne, serwer zgłasza wyjątek i nie można go uruchomić.</span><span class="sxs-lookup"><span data-stu-id="1c964-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="1c964-921">Sekcja `Certificate` obsługuje **Path**&ndash;zarówno**certyfikaty Path Password, jak** i **Subject**&ndash;**Store.**</span><span class="sxs-lookup"><span data-stu-id="1c964-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="1c964-922">Dowolna liczba punktów końcowych może być zdefiniowana w ten sposób, o ile nie powodują konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="1c964-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="1c964-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` metodę, `.Endpoint(string name, listenOptions => { })` która może służyć do uzupełniania ustawień skonfigurowanego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="1c964-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="1c964-924">`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do dalszej iteracji na istniejącym ładowarce, takim jak ten dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>program .</span><span class="sxs-lookup"><span data-stu-id="1c964-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="1c964-925">Sekcja konfiguracji dla każdego punktu końcowego jest `Endpoint` dostępna na opcje w metodzie, dzięki czemu ustawienia niestandardowe mogą być odczytywane.</span><span class="sxs-lookup"><span data-stu-id="1c964-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="1c964-926">Wiele konfiguracji może być `options.Configure(context.Configuration.GetSection("{SECTION}"))` ładowanych przez ponowne wywołanie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="1c964-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="1c964-927">Używana jest tylko ostatnia `Load` konfiguracja, chyba że jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="1c964-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="1c964-928">Metapakiet nie jest `Load` wywoływany, więc jego domyślna sekcja konfiguracji może zostać zastąpiona.</span><span class="sxs-lookup"><span data-stu-id="1c964-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="1c964-929">`KestrelConfigurationLoader`dubluje `Listen` rodzinę interfejsów `KestrelServerOptions` `Endpoint` API z jako przeciążenia, więc kod i config punktów końcowych mogą być skonfigurowane w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="1c964-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="1c964-930">Te przeciążenia nie używają nazw i zużywają tylko ustawienia domyślne z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1c964-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="1c964-931">*Zmienianie wartości domyślnych w kodzie*</span><span class="sxs-lookup"><span data-stu-id="1c964-931">*Change the defaults in code*</span></span>

<span data-ttu-id="1c964-932">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany `ListenOptions` `HttpsConnectionAdapterOptions`ustawień domyślnych dla i , w tym zastąpienie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="1c964-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="1c964-933">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="1c964-934">*Obsługa pustułki dla SNI*</span><span class="sxs-lookup"><span data-stu-id="1c964-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="1c964-935">[Wskazanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do obsługi wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="1c964-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="1c964-936">Aby usługa SNI działała, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania protokołu TLS, aby serwer mógł dostarczyć poprawny certyfikat.</span><span class="sxs-lookup"><span data-stu-id="1c964-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="1c964-937">Klient używa dostarczonego certyfikatu do szyfrowanej komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="1c964-938">Kestrel obsługuje SNI `ServerCertificateSelector` za pomocą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="1c964-939">Wywołanie zwrotne jest wywoływane raz na połączenie, aby umożliwić aplikacji sprawdzenie nazwy hosta i wybranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1c964-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="1c964-940">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="1c964-940">SNI support requires:</span></span>

* <span data-ttu-id="1c964-941">Uruchamianie w `netcoreapp2.1` ramach docelowej lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="1c964-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="1c964-942">Włączone `net461` lub późniejsze wywołanie zwrotne `name` jest `null`wywoływane, ale jest zawsze .</span><span class="sxs-lookup"><span data-stu-id="1c964-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="1c964-943">Jest `name` również, `null` jeśli klient nie udostępnia parametru nazwy hosta w uzgadnianiu TLS.</span><span class="sxs-lookup"><span data-stu-id="1c964-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="1c964-944">Wszystkie strony internetowe działają w tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="1c964-945">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="1c964-946">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="1c964-946">Connection logging</span></span>

<span data-ttu-id="1c964-947">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> emitowania dzienników poziomu debugowania dla komunikacji na poziomie bajtów w połączeniu.</span><span class="sxs-lookup"><span data-stu-id="1c964-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="1c964-948">Rejestrowanie połączeń jest przydatne do rozwiązywania problemów w komunikacji niskiego poziomu, takich jak podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="1c964-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="1c964-949">Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony przed , zaszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="1c964-950">Jeśli `UseConnectionLogging` zostanie `UseHttps`umieszczony po , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="1c964-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="1c964-951">Powiązanie z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="1c964-951">Bind to a TCP socket</span></span>

<span data-ttu-id="1c964-952">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X.509:</span><span class="sxs-lookup"><span data-stu-id="1c964-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="1c964-953">W przykładzie konfiguruje protokół <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS dla punktu końcowego za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="1c964-954">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="1c964-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="1c964-955">Powiązanie z gniazdem Unixa</span><span class="sxs-lookup"><span data-stu-id="1c964-955">Bind to a Unix socket</span></span>

<span data-ttu-id="1c964-956">Słuchaj na gnieździe Unix z <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> lepszej wydajności z Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1c964-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="1c964-957">W pliku nginx confiuguration `server`  >  `location`  >  `proxy_pass` ustaw `http://unix:/tmp/{KESTREL SOCKET}:/;`wpis na .</span><span class="sxs-lookup"><span data-stu-id="1c964-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="1c964-958">`{KESTREL SOCKET}`jest nazwą gniazda dostarczonego <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (na `kestrel-test.sock` przykład w poprzednim przykładzie).</span><span class="sxs-lookup"><span data-stu-id="1c964-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="1c964-959">Upewnij się, że gniazdo jest zapisywalne przez `chmod go+w /tmp/kestrel-test.sock`Nginx (na przykład).</span><span class="sxs-lookup"><span data-stu-id="1c964-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="1c964-960">Port 0</span><span class="sxs-lookup"><span data-stu-id="1c964-960">Port 0</span></span>

<span data-ttu-id="1c964-961">Po określeniu numeru `0` portu Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="1c964-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="1c964-962">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie związany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="1c964-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="1c964-963">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, do którego można uzyskać kontakt z aplikacją:</span><span class="sxs-lookup"><span data-stu-id="1c964-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="1c964-964">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="1c964-964">Limitations</span></span>

<span data-ttu-id="1c964-965">Konfigurowanie punktów końcowych przy następujących podejściach:</span><span class="sxs-lookup"><span data-stu-id="1c964-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="1c964-966">`--urls`argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="1c964-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="1c964-967">`urls`klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="1c964-967">`urls` host configuration key</span></span>
* <span data-ttu-id="1c964-968">`ASPNETCORE_URLS`zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="1c964-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="1c964-969">Te metody są przydatne do tworzenia kodu pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c964-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="1c964-970">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="1c964-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="1c964-971">Protokołu HTTPS nie można używać z tymi podejściami, chyba że domyślny certyfikat jest `KestrelServerOptions` podany w konfiguracji punktu końcowego HTTPS (na przykład przy użyciu konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="1c964-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="1c964-972">Gdy zarówno `Listen` `UseUrls` i podejścia są używane `Listen` jednocześnie, punkty końcowe zastępują punkty `UseUrls` końcowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="1c964-973">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="1c964-973">IIS endpoint configuration</span></span>

<span data-ttu-id="1c964-974">Podczas korzystania z usługi IIS powiązania adresów URL dla powiązań `Listen` zastępowania usługi IIS są ustawiane przez jedną lub `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="1c964-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="1c964-975">Aby uzyskać więcej informacji, zobacz [ASP.NET temat modułu podstawowego.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="1c964-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="1c964-976">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="1c964-976">Transport configuration</span></span>

<span data-ttu-id="1c964-977">Wraz z wydaniem ASP.NET Core 2.1 domyślny transport Kestrel nie jest już oparty na Libuv, ale zamiast tego opiera się na zarządzanych gniazdach.</span><span class="sxs-lookup"><span data-stu-id="1c964-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="1c964-978">Jest to przełomowa zmiana dla ASP.NET aplikacje Core 2.0 uaktualniania <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> do 2.1, które wywołują i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="1c964-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="1c964-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="1c964-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="1c964-980">Aplikacja Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="1c964-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="1c964-981">W przypadku projektów, które wymagają użycia libuv:</span><span class="sxs-lookup"><span data-stu-id="1c964-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="1c964-982">Dodaj zależność pakietu [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1c964-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="1c964-983">Zadzwoń <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="1c964-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="1c964-984">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="1c964-984">URL prefixes</span></span>

<span data-ttu-id="1c964-985">W `UseUrls`przypadku `--urls` korzystania z `urls` argumentu wiersza `ASPNETCORE_URLS` polecenia, klucza konfiguracji hosta lub zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w dowolnym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="1c964-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="1c964-986">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="1c964-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="1c964-987">Pustułka nie obsługuje protokołu HTTPS podczas `UseUrls`konfigurowania powiązań adresów URL przy użyciu pliku .</span><span class="sxs-lookup"><span data-stu-id="1c964-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="1c964-988">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="1c964-989">`0.0.0.0`jest specjalnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="1c964-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="1c964-990">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="1c964-991">`[::]`jest odpowiednikiem IPv6 IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="1c964-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="1c964-992">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="1c964-993">Nazwy hostów `*` `+`, i , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="1c964-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="1c964-994">Wszystko, co nie jest `localhost` rozpoznawane jako prawidłowy adres IP lub wiąże się ze wszystkimi adresami IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="1c964-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="1c964-995">Aby powiązać różne nazwy hostów z różnymi aplikacjami ASP.NET Core na tym samym porcie, użyj [protokołu HTTP.sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="1c964-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="1c964-996">Hosting w odwrotnej konfiguracji serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1c964-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="1c964-997">Nazwa `localhost` hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="1c964-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="1c964-998">Po `localhost` określeniu Kestrel próbuje powiązać interfejsy sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="1c964-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="1c964-999">Jeśli żądany port jest używany przez inną usługę w interfejsie sprzężenia zwrotnego, kestrel nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="1c964-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="1c964-1000">Jeśli którykolwiek z interfejsów sprzężenia zwrotnego jest niedostępny z jakiegokolwiek innego powodu (najczęściej dlatego, że IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="1c964-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="1c964-1001">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="1c964-1001">Host filtering</span></span>

<span data-ttu-id="1c964-1002">Gdy Kestrel obsługuje konfigurację opartą `http://example.com:5000`na prefiksach, takich jak Kestrel, w dużej mierze ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="1c964-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="1c964-1003">Host `localhost` jest specjalnym przypadkiem używanym do wiązania adresów sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c964-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="1c964-1004">Każdy host inny niż jawny adres IP wiąże się ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="1c964-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="1c964-1005">`Host`nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="1c964-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="1c964-1006">Aby obejść ten problem, użyj oprogramowania pośredniczącego filtrowania hostów.</span><span class="sxs-lookup"><span data-stu-id="1c964-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="1c964-1007">Oprogramowanie pośredniczące filtrowania hostów jest dostarczane przez pakiet [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub 2.2).</span><span class="sxs-lookup"><span data-stu-id="1c964-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="1c964-1008">Oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>jest dodawane przez , który wywołuje: <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*></span><span class="sxs-lookup"><span data-stu-id="1c964-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="1c964-1009">Oprogramowanie pośredniczące filtrowania hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="1c964-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="1c964-1010">Aby włączyć oprogramowanie pośredniczące, `AllowedHosts` zdefiniuj klucz w *appsettings.json*/*appsettings.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="1c964-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="1c964-1011">Wartość jest listą nazw hostów rozdzielanych średnikami bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="1c964-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="1c964-1012">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1c964-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="1c964-1013">[Oprogramowanie pośredniczące nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) również ma <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="1c964-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="1c964-1014">Oprogramowanie pośredniczące nagłówków przesyłanych dalej i oprogramowanie pośredniczące filtrowania hostów mają podobną funkcjonalność w różnych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="1c964-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="1c964-1015">Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego `Host` Nagłówków przesyłanych dalej jest właściwe, gdy nagłówek nie jest zachowywany podczas przekazywania żądań za pomocą serwera odwrotnego serwera proxy lub modułu równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="1c964-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="1c964-1016">Ustawienie `AllowedHosts` za pomocą oprogramowania pośredniczącego filtrowania hostów jest właściwe, gdy `Host` Kestrel jest używany jako serwer brzegowy skierowany do publicznej wiadomości lub gdy nagłówek jest bezpośrednio przekazywaniem dalej.</span><span class="sxs-lookup"><span data-stu-id="1c964-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="1c964-1017">Aby uzyskać więcej informacji na temat <xref:host-and-deploy/proxy-load-balancer>oprogramowania pośredniczącego nagłówków przesyłanych dalej, zobacz .</span><span class="sxs-lookup"><span data-stu-id="1c964-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1c964-1018">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1c964-1018">Additional resources</span></span>

* <span data-ttu-id="1c964-1019">W przypadku korzystania z gniazd UNIX w systemie Linux gniazdo nie jest automatycznie usuwane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1c964-1019">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="1c964-1020">Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="1c964-1020">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="1c964-1021">RFC 7230: Składnia i routing komunikatów (sekcja 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="1c964-1021">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
