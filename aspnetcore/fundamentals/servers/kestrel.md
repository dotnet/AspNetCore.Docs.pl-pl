---
<span data-ttu-id="2de44-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-102">'Blazor'</span></span>
- <span data-ttu-id="2de44-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-103">'Identity'</span></span>
- <span data-ttu-id="2de44-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-105">'Razor'</span></span>
- <span data-ttu-id="2de44-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-106">'SignalR' uid:</span></span> 

---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="2de44-107">Implementacja serwera sieci Web Kestrel w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2de44-107">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="2de44-108">Autorzy [Dykstra](https://github.com/tdykstra), [Krzysztof Ross](https://github.com/Tratcher)i [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="2de44-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2de44-109">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2de44-110">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de44-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2de44-111">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="2de44-111">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2de44-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2de44-112">HTTPS</span></span>
* <span data-ttu-id="2de44-113">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="2de44-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2de44-114">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="2de44-114">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2de44-115">HTTP/2 (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="2de44-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2de44-116">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2de44-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2de44-117">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de44-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2de44-118">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2de44-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2de44-119">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2de44-119">HTTP/2 support</span></span>

<span data-ttu-id="2de44-120">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="2de44-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2de44-121">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="2de44-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="2de44-122">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2de44-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2de44-123">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="2de44-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2de44-124">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2de44-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2de44-125">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="2de44-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2de44-126">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="2de44-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2de44-127">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2de44-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2de44-128">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2de44-129">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="2de44-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2de44-130">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="2de44-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2de44-131">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="2de44-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2de44-132">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="2de44-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2de44-133">Więcej informacji na temat konfiguracji można znaleźć w sekcjach [Kestrel Options](#kestrel-options) and [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="2de44-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2de44-134">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="2de44-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2de44-135">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="2de44-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2de44-136">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2de44-137">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="2de44-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2de44-139">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="2de44-139">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2de44-141">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="2de44-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2de44-142">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="2de44-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2de44-143">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="2de44-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2de44-144">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="2de44-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2de44-145">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2de44-146">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="2de44-146">A reverse proxy:</span></span>

* <span data-ttu-id="2de44-147">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="2de44-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2de44-148">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="2de44-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2de44-149">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="2de44-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2de44-150">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2de44-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2de44-151">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2de44-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2de44-152">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2de44-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2de44-153">Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2de44-153">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2de44-154">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2de44-155">W *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> Metoda wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="2de44-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="2de44-156">Więcej informacji na temat tworzenia hosta znajduje się w sekcji *Konfigurowanie ustawień hosta* i *domyślnego konstruktora* w temacie <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2de44-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="2de44-157">Aby zapewnić dodatkową konfigurację po wywołaniu `ConfigureWebHostDefaults` , użyj `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2de44-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2de44-158">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="2de44-158">Kestrel options</span></span>

<span data-ttu-id="2de44-159">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="2de44-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2de44-160">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2de44-161">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2de44-162">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="2de44-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2de44-163">W przykładach przedstawionych w dalszej części tego artykułu opcje Kestrel są konfigurowane w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="2de44-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="2de44-164">Opcje Kestrel można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2de44-165">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację Kestrel z pliku *appSettings. JSON* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="2de44-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="2de44-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfigurację punktu końcowego](#endpoint-configuration) można konfigurować z poziomu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="2de44-167">Pozostała konfiguracja Kestrel musi być skonfigurowana w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="2de44-167">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="2de44-168">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2de44-168">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2de44-169">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2de44-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2de44-170">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2de44-171">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2de44-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2de44-172">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2de44-173">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="2de44-173">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2de44-174">W programie *program.cs*Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2de44-175">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2de44-176">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="2de44-176">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2de44-177">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="2de44-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2de44-178">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="2de44-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2de44-179">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="2de44-179">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2de44-180">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2de44-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2de44-181">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2de44-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2de44-182">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="2de44-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2de44-183">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="2de44-183">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2de44-184">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-184">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2de44-185">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2de44-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2de44-186">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="2de44-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2de44-187">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="2de44-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2de44-188">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="2de44-188">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2de44-189">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2de44-190">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="2de44-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2de44-191">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="2de44-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2de44-192">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-192">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2de44-193">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="2de44-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2de44-194">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="2de44-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2de44-195">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="2de44-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2de44-196">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="2de44-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2de44-197">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2de44-198">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="2de44-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2de44-199">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2de44-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="2de44-200">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="2de44-200">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2de44-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odwołania w poprzednim przykładzie nie występują w `HttpContext.Features` żądaniach HTTP/2, ponieważ Modyfikowanie limitów szybkości dla poszczególnych żądań jest ogólnie nieobsługiwane w przypadku protokołu HTTP/2 z powodu obsługi żądania multipleksowania żądań.</span><span class="sxs-lookup"><span data-stu-id="2de44-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2de44-202">Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> nadal występuje `HttpContext.Features` dla żądań HTTP/2, ponieważ limit liczby odczytów nadal można *wyłączyć całkowicie* dla każdego żądania przez ustawienie `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` nawet dla żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="2de44-203">Próba odczytania `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby ustawienia jej na wartość inną niż `null` spowoduje `NotSupportedException` zgłoszenie żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="2de44-204">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2de44-205">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="2de44-205">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2de44-206">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2de44-207">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="2de44-207">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2de44-208">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="2de44-208">Maximum streams per connection</span></span>

<span data-ttu-id="2de44-209">`Http2.MaxStreamsPerConnection`ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2de44-210">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-210">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="2de44-211">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="2de44-211">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2de44-212">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="2de44-212">Header table size</span></span>

<span data-ttu-id="2de44-213">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2de44-214">`Http2.HeaderTableSize`ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="2de44-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2de44-215">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="2de44-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="2de44-216">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="2de44-216">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2de44-217">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="2de44-217">Maximum frame size</span></span>

<span data-ttu-id="2de44-218">`Http2.MaxFrameSize`wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer.</span><span class="sxs-lookup"><span data-stu-id="2de44-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="2de44-219">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="2de44-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="2de44-220">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="2de44-220">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2de44-221">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-221">Maximum request header size</span></span>

<span data-ttu-id="2de44-222">`Http2.MaxRequestHeaderFieldSize`wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2de44-223">Ten limit dotyczy zarówno nazwy, jak i wartości w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2de44-224">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="2de44-224">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="2de44-225">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="2de44-225">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2de44-226">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="2de44-226">Initial connection window size</span></span>

<span data-ttu-id="2de44-227">`Http2.InitialConnectionWindowSize`wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="2de44-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2de44-228">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2de44-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2de44-229">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2de44-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="2de44-230">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="2de44-230">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2de44-231">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="2de44-231">Initial stream window size</span></span>

<span data-ttu-id="2de44-232">`Http2.InitialStreamWindowSize`wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="2de44-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2de44-233">Żądania są również ograniczone przez `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2de44-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="2de44-234">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2de44-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="2de44-235">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="2de44-235">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2de44-236">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="2de44-236">Synchronous I/O</span></span>

<span data-ttu-id="2de44-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2de44-238">Wartość domyślna to `false`.</span><span class="sxs-lookup"><span data-stu-id="2de44-238">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="2de44-239">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="2de44-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2de44-240">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="2de44-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2de44-241">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="2de44-241">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2de44-242">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="2de44-242">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2de44-243">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="2de44-243">Endpoint configuration</span></span>

<span data-ttu-id="2de44-244">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="2de44-244">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2de44-245">`https://localhost:5001`(gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="2de44-245">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2de44-246">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="2de44-246">Specify URLs using the:</span></span>

* <span data-ttu-id="2de44-247">`ASPNETCORE_URLS`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="2de44-247">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2de44-248">`--urls`argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-248">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2de44-249">`urls`klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-249">`urls` host configuration key.</span></span>
* <span data-ttu-id="2de44-250">`UseUrls`Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-250">`UseUrls` extension method.</span></span>

<span data-ttu-id="2de44-251">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="2de44-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2de44-252">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2de44-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2de44-253">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2de44-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2de44-254">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="2de44-254">A development certificate is created:</span></span>

* <span data-ttu-id="2de44-255">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="2de44-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2de44-256">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="2de44-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2de44-257">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="2de44-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2de44-258">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2de44-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2de44-259">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2de44-260">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2de44-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2de44-261">`KestrelServerOptions`skonfigurować</span><span class="sxs-lookup"><span data-stu-id="2de44-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2de44-262">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2de44-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2de44-263">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2de44-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2de44-264">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="2de44-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2de44-265">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2de44-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2de44-266">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2de44-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2de44-267">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2de44-268">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="2de44-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2de44-269">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2de44-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2de44-270">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2de44-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="2de44-271">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2de44-272">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-272">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2de44-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2de44-273">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2de44-274">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-274">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2de44-275">`ListenOptions.UseHttps`rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="2de44-275">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2de44-276">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="2de44-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2de44-277">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="2de44-277">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2de44-278">`ListenOptions.UseHttps`wejściowe</span><span class="sxs-lookup"><span data-stu-id="2de44-278">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2de44-279">`filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2de44-280">`password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="2de44-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2de44-281">`configureOptions`jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2de44-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2de44-282">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2de44-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2de44-283">`storeName`to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-283">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2de44-284">`subject`to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2de44-284">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2de44-285">`allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="2de44-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2de44-286">`location`jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-286">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2de44-287">`serverCertificate`jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="2de44-287">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2de44-288">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-288">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2de44-289">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="2de44-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2de44-290">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="2de44-290">Supported configurations described next:</span></span>

* <span data-ttu-id="2de44-291">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2de44-291">No configuration</span></span>
* <span data-ttu-id="2de44-292">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2de44-292">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2de44-293">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="2de44-293">Change the defaults in code</span></span>

<span data-ttu-id="2de44-294">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-294">*No configuration*</span></span>

<span data-ttu-id="2de44-295">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="2de44-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2de44-296">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-296">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2de44-297">`CreateDefaultBuilder`wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2de44-298">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2de44-299">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2de44-300">W poniższym przykładzie pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2de44-300">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2de44-301">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="2de44-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2de44-302">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="2de44-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2de44-303">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2de44-304">Certyfikat domyślny można na **przykład**  >  **Default** określić jako:</span><span class="sxs-lookup"><span data-stu-id="2de44-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2de44-305">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="2de44-305">Schema notes:</span></span>

* <span data-ttu-id="2de44-306">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="2de44-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2de44-307">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2de44-308">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2de44-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2de44-309">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="2de44-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2de44-310">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="2de44-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2de44-311">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2de44-312">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="2de44-312">The `Certificate` section is optional.</span></span> <span data-ttu-id="2de44-313">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="2de44-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2de44-314">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="2de44-314">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2de44-315">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash; **Password** , jak i certyfikaty magazynu **podmiotu** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="2de44-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2de44-316">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="2de44-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2de44-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="2de44-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2de44-318">`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2de44-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2de44-319">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2de44-320">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="2de44-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2de44-321">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="2de44-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2de44-322">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="2de44-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2de44-323">`KestrelConfigurationLoader`odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="2de44-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2de44-324">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2de44-325">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="2de44-325">*Change the defaults in code*</span></span>

<span data-ttu-id="2de44-326">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="2de44-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2de44-327">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2de44-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2de44-328">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="2de44-328">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2de44-329">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="2de44-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2de44-330">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2de44-331">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2de44-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2de44-332">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2de44-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2de44-333">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2de44-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2de44-334">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="2de44-334">SNI support requires:</span></span>

* <span data-ttu-id="2de44-335">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="2de44-335">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2de44-336">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="2de44-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2de44-337">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2de44-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2de44-338">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2de44-339">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2de44-340">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="2de44-340">Connection logging</span></span>

<span data-ttu-id="2de44-341">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2de44-342">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2de44-343">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="2de44-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2de44-344">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="2de44-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2de44-345">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="2de44-345">Bind to a TCP socket</span></span>

<span data-ttu-id="2de44-346"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="2de44-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="2de44-347">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2de44-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2de44-348">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2de44-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2de44-349">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="2de44-349">Bind to a Unix socket</span></span>

<span data-ttu-id="2de44-350">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2de44-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2de44-351">W pliku konfiguracji Nginx Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2de44-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2de44-352">`{KESTREL SOCKET}`jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="2de44-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2de44-353">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2de44-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="2de44-354">Port 0</span><span class="sxs-lookup"><span data-stu-id="2de44-354">Port 0</span></span>

<span data-ttu-id="2de44-355">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="2de44-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2de44-356">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="2de44-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2de44-357">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2de44-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2de44-358">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="2de44-358">Limitations</span></span>

<span data-ttu-id="2de44-359">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2de44-359">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2de44-360">`--urls`argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="2de44-360">`--urls` command-line argument</span></span>
* <span data-ttu-id="2de44-361">`urls`klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="2de44-361">`urls` host configuration key</span></span>
* <span data-ttu-id="2de44-362">`ASPNETCORE_URLS`Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="2de44-362">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2de44-363">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-363">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2de44-364">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="2de44-364">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2de44-365">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="2de44-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2de44-366">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2de44-367">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="2de44-367">IIS endpoint configuration</span></span>

<span data-ttu-id="2de44-368">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2de44-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2de44-369">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="2de44-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2de44-370">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="2de44-370">ListenOptions.Protocols</span></span>

<span data-ttu-id="2de44-371">`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="2de44-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2de44-372">Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2de44-373">`HttpProtocols`wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="2de44-373">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2de44-374">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="2de44-374">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="2de44-375">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-376">'Blazor'</span></span>
- <span data-ttu-id="2de44-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-377">'Identity'</span></span>
- <span data-ttu-id="2de44-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-379">'Razor'</span></span>
- <span data-ttu-id="2de44-380">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-381">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-382">'Blazor'</span></span>
- <span data-ttu-id="2de44-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-383">'Identity'</span></span>
- <span data-ttu-id="2de44-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-385">'Razor'</span></span>
- <span data-ttu-id="2de44-386">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-387">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-388">'Blazor'</span></span>
- <span data-ttu-id="2de44-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-389">'Identity'</span></span>
- <span data-ttu-id="2de44-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-391">'Razor'</span></span>
- <span data-ttu-id="2de44-392">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-393">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-394">'Blazor'</span></span>
- <span data-ttu-id="2de44-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-395">'Identity'</span></span>
- <span data-ttu-id="2de44-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-397">'Razor'</span></span>
- <span data-ttu-id="2de44-398">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-399">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-400">'Blazor'</span></span>
- <span data-ttu-id="2de44-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-401">'Identity'</span></span>
- <span data-ttu-id="2de44-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-403">'Razor'</span></span>
- <span data-ttu-id="2de44-404">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-405">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-406">'Blazor'</span></span>
- <span data-ttu-id="2de44-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-407">'Identity'</span></span>
- <span data-ttu-id="2de44-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-409">'Razor'</span></span>
- <span data-ttu-id="2de44-410">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-411">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-412">'Blazor'</span></span>
- <span data-ttu-id="2de44-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-413">'Identity'</span></span>
- <span data-ttu-id="2de44-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-415">'Razor'</span></span>
- <span data-ttu-id="2de44-416">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-417">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-418">'Blazor'</span></span>
- <span data-ttu-id="2de44-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-419">'Identity'</span></span>
- <span data-ttu-id="2de44-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-421">'Razor'</span></span>
- <span data-ttu-id="2de44-422">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-423">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-424">'Blazor'</span></span>
- <span data-ttu-id="2de44-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-425">'Identity'</span></span>
- <span data-ttu-id="2de44-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-427">'Razor'</span></span>
- <span data-ttu-id="2de44-428">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-429">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-430">'Blazor'</span></span>
- <span data-ttu-id="2de44-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-431">'Identity'</span></span>
- <span data-ttu-id="2de44-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-433">'Razor'</span></span>
- <span data-ttu-id="2de44-434">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-435">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-436">'Blazor'</span></span>
- <span data-ttu-id="2de44-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-437">'Identity'</span></span>
- <span data-ttu-id="2de44-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-439">'Razor'</span></span>
- <span data-ttu-id="2de44-440">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-440">'SignalR' uid:</span></span> 

<span data-ttu-id="2de44-441">------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-442">'Blazor'</span></span>
- <span data-ttu-id="2de44-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-443">'Identity'</span></span>
- <span data-ttu-id="2de44-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-445">'Razor'</span></span>
- <span data-ttu-id="2de44-446">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-447">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-448">'Blazor'</span></span>
- <span data-ttu-id="2de44-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-449">'Identity'</span></span>
- <span data-ttu-id="2de44-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-451">'Razor'</span></span>
- <span data-ttu-id="2de44-452">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-453">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-454">'Blazor'</span></span>
- <span data-ttu-id="2de44-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-455">'Identity'</span></span>
- <span data-ttu-id="2de44-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-457">'Razor'</span></span>
- <span data-ttu-id="2de44-458">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-459">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-460">'Blazor'</span></span>
- <span data-ttu-id="2de44-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-461">'Identity'</span></span>
- <span data-ttu-id="2de44-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-463">'Razor'</span></span>
- <span data-ttu-id="2de44-464">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-465">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-466">'Blazor'</span></span>
- <span data-ttu-id="2de44-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-467">'Identity'</span></span>
- <span data-ttu-id="2de44-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-469">'Razor'</span></span>
- <span data-ttu-id="2de44-470">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-471">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-472">'Blazor'</span></span>
- <span data-ttu-id="2de44-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-473">'Identity'</span></span>
- <span data-ttu-id="2de44-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-475">'Razor'</span></span>
- <span data-ttu-id="2de44-476">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-477">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-478">'Blazor'</span></span>
- <span data-ttu-id="2de44-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-479">'Identity'</span></span>
- <span data-ttu-id="2de44-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-481">'Razor'</span></span>
- <span data-ttu-id="2de44-482">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-483">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-484">'Blazor'</span></span>
- <span data-ttu-id="2de44-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-485">'Identity'</span></span>
- <span data-ttu-id="2de44-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-487">'Razor'</span></span>
- <span data-ttu-id="2de44-488">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-489">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-490">'Blazor'</span></span>
- <span data-ttu-id="2de44-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-491">'Identity'</span></span>
- <span data-ttu-id="2de44-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-493">'Razor'</span></span>
- <span data-ttu-id="2de44-494">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-495">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-496">'Blazor'</span></span>
- <span data-ttu-id="2de44-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-497">'Identity'</span></span>
- <span data-ttu-id="2de44-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-499">'Razor'</span></span>
- <span data-ttu-id="2de44-500">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-501">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-502">'Blazor'</span></span>
- <span data-ttu-id="2de44-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-503">'Identity'</span></span>
- <span data-ttu-id="2de44-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-505">'Razor'</span></span>
- <span data-ttu-id="2de44-506">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-507">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-508">'Blazor'</span></span>
- <span data-ttu-id="2de44-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-509">'Identity'</span></span>
- <span data-ttu-id="2de44-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-511">'Razor'</span></span>
- <span data-ttu-id="2de44-512">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-512">'SignalR' uid:</span></span> 

<span data-ttu-id="2de44-513">--------------- | | `Http1`                    | Tylko HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="2de44-514">Może być używany z protokołem TLS lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="2de44-514">Can be used with or without TLS.</span></span> <span data-ttu-id="2de44-515">| | `Http2`                    | Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-515">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="2de44-516">Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="2de44-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="2de44-517">| | `Http1AndHttp2`            | HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2de44-518">Protokół HTTP/2 wymaga od klienta wyboru protokołu HTTP/2 w uzgadnianiu [negocjowania protokołu TLS aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2de44-519">Wartość domyślna `ListenOptions.Protocols` dla każdego punktu końcowego to `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="2de44-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="2de44-520">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="2de44-520">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2de44-521">TLS w wersji 1,2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="2de44-521">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2de44-522">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="2de44-522">Renegotiation disabled</span></span>
* <span data-ttu-id="2de44-523">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="2de44-523">Compression disabled</span></span>
* <span data-ttu-id="2de44-524">Minimalne rozmiary tymczasowych kluczy wymiany:</span><span class="sxs-lookup"><span data-stu-id="2de44-524">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2de44-525">Krzywa eliptyczna Diffie'ego-Hellmana (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bitów minimum</span><span class="sxs-lookup"><span data-stu-id="2de44-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2de44-526">Ograniczone pole Diffie-Hellmana (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="2de44-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2de44-527">Mechanizm szyfrowania nie został zabroniony</span><span class="sxs-lookup"><span data-stu-id="2de44-527">Cipher suite not blacklisted</span></span>

<span data-ttu-id="2de44-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="2de44-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2de44-529">Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="2de44-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2de44-530">Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="2de44-530">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="2de44-531">Używaj oprogramowania pośredniczącego do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów, jeśli jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="2de44-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="2de44-532">Poniższy przykład zgłasza <xref:System.NotSupportedException> dowolny algorytm szyfrowania, który nie jest obsługiwany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="2de44-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="2de44-533">Alternatywnie można zdefiniować i porównać [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) z listą akceptowalnych mechanizmów szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="2de44-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="2de44-534">Nie jest używane szyfrowanie z algorytmem szyfrowania [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="2de44-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="2de44-535">Filtrowanie połączeń można również skonfigurować za pomocą wyrażenia <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="2de44-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="2de44-536">W systemie Linux <xref:System.Net.Security.CipherSuitesPolicy> można używać do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń:</span><span class="sxs-lookup"><span data-stu-id="2de44-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="2de44-537">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-537">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2de44-538">`CreateDefaultBuilder`wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2de44-539">W poniższym przykładzie pliku *appSettings. JSON* jako domyślny protokół połączenia dla wszystkich punktów końcowych:</span><span class="sxs-lookup"><span data-stu-id="2de44-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="2de44-540">Poniższy przykład *appSettings. JSON* ustanawia protokół połączeń HTTP/1.1 dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="2de44-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2de44-541">Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="2de44-541">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2de44-542">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="2de44-542">Transport configuration</span></span>

<span data-ttu-id="2de44-543">Dla projektów, które wymagają użycia Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="2de44-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="2de44-544">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2de44-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="2de44-545">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2de44-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2de44-546">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="2de44-546">URL prefixes</span></span>

<span data-ttu-id="2de44-547">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2de44-548">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2de44-549">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2de44-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2de44-550">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-550">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2de44-551">`0.0.0.0`jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="2de44-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2de44-552">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-552">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2de44-553">`[::]`jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2de44-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2de44-554">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-554">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2de44-555">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="2de44-555">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2de44-556">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2de44-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2de44-557">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [protokołu HTTP. sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="2de44-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2de44-558">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2de44-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2de44-559">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-559">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2de44-560">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2de44-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2de44-561">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="2de44-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2de44-562">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="2de44-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2de44-563">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="2de44-563">Host filtering</span></span>

<span data-ttu-id="2de44-564">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2de44-565">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2de44-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2de44-566">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="2de44-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2de44-567">`Host`nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="2de44-567">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2de44-568">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-568">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2de44-569">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest niejawnie dostarczany dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="2de44-570">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="2de44-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2de44-571">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="2de44-571">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2de44-572">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w pliku *appSettings. JSON* / *. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="2de44-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2de44-573">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="2de44-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2de44-574">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2de44-574">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2de44-575">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="2de44-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2de44-576">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="2de44-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2de44-577">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2de44-578">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="2de44-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2de44-579">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="2de44-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2de44-580">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2de44-581">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de44-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2de44-582">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="2de44-582">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2de44-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2de44-583">HTTPS</span></span>
* <span data-ttu-id="2de44-584">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="2de44-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2de44-585">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="2de44-585">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2de44-586">HTTP/2 (z wyjątkiem macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="2de44-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2de44-587">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2de44-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2de44-588">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de44-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2de44-589">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2de44-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2de44-590">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2de44-590">HTTP/2 support</span></span>

<span data-ttu-id="2de44-591">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest dostępny dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="2de44-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2de44-592">System operacyjny&dagger;</span><span class="sxs-lookup"><span data-stu-id="2de44-592">Operating system&dagger;</span></span>
  * <span data-ttu-id="2de44-593">Windows Server 2016/Windows 10 lub nowszy&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2de44-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2de44-594">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="2de44-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2de44-595">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2de44-595">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2de44-596">Połączenie [negocjowania protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="2de44-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2de44-597">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="2de44-597">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2de44-598">&dagger;Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2de44-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2de44-599">&Dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2de44-600">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="2de44-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2de44-601">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="2de44-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2de44-602">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="2de44-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2de44-603">Protokół HTTP/2 jest domyślnie wyłączony.</span><span class="sxs-lookup"><span data-stu-id="2de44-603">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2de44-604">Więcej informacji na temat konfiguracji można znaleźć w sekcjach [Kestrel Options](#kestrel-options) and [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="2de44-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2de44-605">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="2de44-605">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2de44-606">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="2de44-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2de44-607">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2de44-608">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="2de44-608">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2de44-610">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="2de44-610">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2de44-612">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="2de44-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2de44-613">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="2de44-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2de44-614">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="2de44-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2de44-615">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="2de44-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2de44-616">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2de44-617">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="2de44-617">A reverse proxy:</span></span>

* <span data-ttu-id="2de44-618">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="2de44-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2de44-619">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="2de44-619">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2de44-620">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="2de44-620">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2de44-621">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2de44-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2de44-622">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2de44-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2de44-623">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2de44-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2de44-624">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2de44-624">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2de44-625">Pakiet [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2de44-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2de44-626">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-626">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2de44-627">W *program.cs*, kod szablonu wywołuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , który wywołuje się w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> tle.</span><span class="sxs-lookup"><span data-stu-id="2de44-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="2de44-628">Więcej informacji na temat `CreateDefaultBuilder` i kompilowanie hosta znajduje się w sekcji *Konfigurowanie hosta* w temacie <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2de44-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="2de44-629">Aby zapewnić dodatkową konfigurację po wywołaniu `CreateDefaultBuilder` , użyj `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2de44-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2de44-630">Jeśli aplikacja nie wywoła połączenia `CreateDefaultBuilder` w celu skonfigurowania hosta, wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **przed** wywołaniem `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2de44-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2de44-631">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="2de44-631">Kestrel options</span></span>

<span data-ttu-id="2de44-632">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="2de44-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2de44-633">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2de44-634">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2de44-635">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="2de44-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2de44-636">Opcje Kestrel, które są konfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2de44-637">Na przykład dostawca konfiguracji plików może załadować konfigurację Kestrel z pliku *appSettings. JSON* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="2de44-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2de44-638">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2de44-638">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2de44-639">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2de44-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2de44-640">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2de44-641">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2de44-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2de44-642">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2de44-643">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="2de44-643">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2de44-644">W programie *program.cs*Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2de44-645">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2de44-646">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="2de44-646">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2de44-647">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="2de44-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2de44-648">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="2de44-648">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2de44-649">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="2de44-649">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2de44-650">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2de44-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2de44-651">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2de44-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2de44-652">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="2de44-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2de44-653">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="2de44-653">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2de44-654">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-654">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2de44-655">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2de44-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2de44-656">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="2de44-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2de44-657">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="2de44-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2de44-658">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="2de44-658">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2de44-659">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2de44-660">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="2de44-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2de44-661">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="2de44-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2de44-662">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-662">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2de44-663">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="2de44-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2de44-664">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="2de44-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2de44-665">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="2de44-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2de44-666">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="2de44-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2de44-667">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2de44-668">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="2de44-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2de44-669">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2de44-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="2de44-670">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="2de44-670">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2de44-671">Funkcja rate, do której odwołuje się poprzednia próba, nie jest dostępna w `HttpContext.Features` przypadku żądań HTTP/2, ponieważ Modyfikowanie limitów szybkości dla poszczególnych żądań nie jest obsługiwane w przypadku protokołu HTTP/2 ze względu na obsługę multipleksera żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2de44-672">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem `KestrelServerOptions.Limits` nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2de44-673">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="2de44-673">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2de44-674">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2de44-675">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="2de44-675">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2de44-676">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="2de44-676">Maximum streams per connection</span></span>

<span data-ttu-id="2de44-677">`Http2.MaxStreamsPerConnection`ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2de44-678">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-678">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="2de44-679">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="2de44-679">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2de44-680">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="2de44-680">Header table size</span></span>

<span data-ttu-id="2de44-681">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2de44-682">`Http2.HeaderTableSize`ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="2de44-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2de44-683">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="2de44-683">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="2de44-684">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="2de44-684">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2de44-685">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="2de44-685">Maximum frame size</span></span>

<span data-ttu-id="2de44-686">`Http2.MaxFrameSize`wskazuje maksymalny rozmiar ładunku ramki połączenia HTTP/2 do odebrania.</span><span class="sxs-lookup"><span data-stu-id="2de44-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="2de44-687">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="2de44-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="2de44-688">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="2de44-688">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2de44-689">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-689">Maximum request header size</span></span>

<span data-ttu-id="2de44-690">`Http2.MaxRequestHeaderFieldSize`wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2de44-691">Ten limit dotyczy zarówno nazwy, jak i wartości razem w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2de44-692">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="2de44-692">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="2de44-693">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="2de44-693">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2de44-694">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="2de44-694">Initial connection window size</span></span>

<span data-ttu-id="2de44-695">`Http2.InitialConnectionWindowSize`wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="2de44-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2de44-696">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2de44-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2de44-697">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2de44-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="2de44-698">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="2de44-698">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2de44-699">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="2de44-699">Initial stream window size</span></span>

<span data-ttu-id="2de44-700">`Http2.InitialStreamWindowSize`wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="2de44-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2de44-701">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2de44-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2de44-702">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2de44-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="2de44-703">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="2de44-703">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2de44-704">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="2de44-704">Synchronous I/O</span></span>

<span data-ttu-id="2de44-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2de44-706">Wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="2de44-706">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2de44-707">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="2de44-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2de44-708">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="2de44-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2de44-709">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="2de44-709">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2de44-710">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="2de44-710">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2de44-711">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="2de44-711">Endpoint configuration</span></span>

<span data-ttu-id="2de44-712">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="2de44-712">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2de44-713">`https://localhost:5001`(gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="2de44-713">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2de44-714">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="2de44-714">Specify URLs using the:</span></span>

* <span data-ttu-id="2de44-715">`ASPNETCORE_URLS`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="2de44-715">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2de44-716">`--urls`argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-716">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2de44-717">`urls`klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-717">`urls` host configuration key.</span></span>
* <span data-ttu-id="2de44-718">`UseUrls`Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-718">`UseUrls` extension method.</span></span>

<span data-ttu-id="2de44-719">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="2de44-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2de44-720">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2de44-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2de44-721">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2de44-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2de44-722">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="2de44-722">A development certificate is created:</span></span>

* <span data-ttu-id="2de44-723">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="2de44-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2de44-724">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="2de44-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2de44-725">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="2de44-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2de44-726">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2de44-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2de44-727">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2de44-728">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2de44-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2de44-729">`KestrelServerOptions`skonfigurować</span><span class="sxs-lookup"><span data-stu-id="2de44-729">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2de44-730">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2de44-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2de44-731">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2de44-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2de44-732">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="2de44-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2de44-733">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2de44-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2de44-734">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2de44-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2de44-735">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2de44-736">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="2de44-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2de44-737">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2de44-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="2de44-738">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2de44-738">Configure(IConfiguration)</span></span>

<span data-ttu-id="2de44-739">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2de44-740">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2de44-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2de44-741">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2de44-742">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-742">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2de44-743">`ListenOptions.UseHttps`rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="2de44-743">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2de44-744">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="2de44-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2de44-745">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="2de44-745">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2de44-746">`ListenOptions.UseHttps`wejściowe</span><span class="sxs-lookup"><span data-stu-id="2de44-746">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2de44-747">`filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2de44-748">`password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="2de44-748">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2de44-749">`configureOptions`jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2de44-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2de44-750">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2de44-750">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2de44-751">`storeName`to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-751">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2de44-752">`subject`to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2de44-752">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2de44-753">`allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="2de44-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2de44-754">`location`jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-754">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2de44-755">`serverCertificate`jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="2de44-755">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2de44-756">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2de44-757">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="2de44-757">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2de44-758">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="2de44-758">Supported configurations described next:</span></span>

* <span data-ttu-id="2de44-759">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2de44-759">No configuration</span></span>
* <span data-ttu-id="2de44-760">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2de44-760">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2de44-761">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="2de44-761">Change the defaults in code</span></span>

<span data-ttu-id="2de44-762">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-762">*No configuration*</span></span>

<span data-ttu-id="2de44-763">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="2de44-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2de44-764">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-764">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2de44-765">`CreateDefaultBuilder`wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2de44-766">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2de44-767">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2de44-768">W poniższym przykładzie pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2de44-768">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2de44-769">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="2de44-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2de44-770">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="2de44-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2de44-771">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2de44-772">Certyfikat domyślny można na **przykład**  >  **Default** określić jako:</span><span class="sxs-lookup"><span data-stu-id="2de44-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2de44-773">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="2de44-773">Schema notes:</span></span>

* <span data-ttu-id="2de44-774">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="2de44-774">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2de44-775">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-775">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2de44-776">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2de44-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2de44-777">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="2de44-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2de44-778">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="2de44-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2de44-779">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-779">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2de44-780">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="2de44-780">The `Certificate` section is optional.</span></span> <span data-ttu-id="2de44-781">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="2de44-781">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2de44-782">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="2de44-782">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2de44-783">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash; **Password** , jak i certyfikaty magazynu **podmiotu** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="2de44-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2de44-784">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="2de44-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2de44-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="2de44-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2de44-786">`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2de44-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2de44-787">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2de44-788">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="2de44-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2de44-789">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="2de44-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2de44-790">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="2de44-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2de44-791">`KestrelConfigurationLoader`odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="2de44-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2de44-792">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-792">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2de44-793">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="2de44-793">*Change the defaults in code*</span></span>

<span data-ttu-id="2de44-794">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="2de44-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2de44-795">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2de44-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2de44-796">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="2de44-796">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2de44-797">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="2de44-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2de44-798">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2de44-799">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2de44-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2de44-800">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2de44-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2de44-801">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2de44-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2de44-802">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="2de44-802">SNI support requires:</span></span>

* <span data-ttu-id="2de44-803">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="2de44-803">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2de44-804">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="2de44-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2de44-805">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2de44-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2de44-806">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-806">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2de44-807">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2de44-808">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="2de44-808">Connection logging</span></span>

<span data-ttu-id="2de44-809">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2de44-810">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2de44-811">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="2de44-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2de44-812">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="2de44-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2de44-813">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="2de44-813">Bind to a TCP socket</span></span>

<span data-ttu-id="2de44-814"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="2de44-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="2de44-815">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2de44-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2de44-816">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2de44-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2de44-817">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="2de44-817">Bind to a Unix socket</span></span>

<span data-ttu-id="2de44-818">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2de44-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2de44-819">W pliku Nginx confiuguration Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2de44-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2de44-820">`{KESTREL SOCKET}`jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="2de44-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2de44-821">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2de44-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2de44-822">Port 0</span><span class="sxs-lookup"><span data-stu-id="2de44-822">Port 0</span></span>

<span data-ttu-id="2de44-823">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="2de44-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2de44-824">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="2de44-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2de44-825">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2de44-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2de44-826">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="2de44-826">Limitations</span></span>

<span data-ttu-id="2de44-827">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2de44-827">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2de44-828">`--urls`argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="2de44-828">`--urls` command-line argument</span></span>
* <span data-ttu-id="2de44-829">`urls`klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="2de44-829">`urls` host configuration key</span></span>
* <span data-ttu-id="2de44-830">`ASPNETCORE_URLS`Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="2de44-830">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2de44-831">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-831">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2de44-832">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="2de44-832">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2de44-833">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="2de44-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2de44-834">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2de44-835">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="2de44-835">IIS endpoint configuration</span></span>

<span data-ttu-id="2de44-836">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2de44-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2de44-837">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="2de44-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2de44-838">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="2de44-838">ListenOptions.Protocols</span></span>

<span data-ttu-id="2de44-839">`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera.</span><span class="sxs-lookup"><span data-stu-id="2de44-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2de44-840">Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2de44-841">`HttpProtocols`wartość wyliczenia</span><span class="sxs-lookup"><span data-stu-id="2de44-841">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2de44-842">Dozwolony protokół połączenia</span><span class="sxs-lookup"><span data-stu-id="2de44-842">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="2de44-843">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-844">'Blazor'</span></span>
- <span data-ttu-id="2de44-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-845">'Identity'</span></span>
- <span data-ttu-id="2de44-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-847">'Razor'</span></span>
- <span data-ttu-id="2de44-848">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-849">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-850">'Blazor'</span></span>
- <span data-ttu-id="2de44-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-851">'Identity'</span></span>
- <span data-ttu-id="2de44-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-853">'Razor'</span></span>
- <span data-ttu-id="2de44-854">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-855">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-856">'Blazor'</span></span>
- <span data-ttu-id="2de44-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-857">'Identity'</span></span>
- <span data-ttu-id="2de44-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-859">'Razor'</span></span>
- <span data-ttu-id="2de44-860">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-861">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-862">'Blazor'</span></span>
- <span data-ttu-id="2de44-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-863">'Identity'</span></span>
- <span data-ttu-id="2de44-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-865">'Razor'</span></span>
- <span data-ttu-id="2de44-866">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-867">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-868">'Blazor'</span></span>
- <span data-ttu-id="2de44-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-869">'Identity'</span></span>
- <span data-ttu-id="2de44-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-871">'Razor'</span></span>
- <span data-ttu-id="2de44-872">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-873">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-874">'Blazor'</span></span>
- <span data-ttu-id="2de44-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-875">'Identity'</span></span>
- <span data-ttu-id="2de44-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-877">'Razor'</span></span>
- <span data-ttu-id="2de44-878">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-879">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-880">'Blazor'</span></span>
- <span data-ttu-id="2de44-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-881">'Identity'</span></span>
- <span data-ttu-id="2de44-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-883">'Razor'</span></span>
- <span data-ttu-id="2de44-884">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-885">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-886">'Blazor'</span></span>
- <span data-ttu-id="2de44-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-887">'Identity'</span></span>
- <span data-ttu-id="2de44-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-889">'Razor'</span></span>
- <span data-ttu-id="2de44-890">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-891">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-892">'Blazor'</span></span>
- <span data-ttu-id="2de44-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-893">'Identity'</span></span>
- <span data-ttu-id="2de44-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-895">'Razor'</span></span>
- <span data-ttu-id="2de44-896">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-897">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-898">'Blazor'</span></span>
- <span data-ttu-id="2de44-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-899">'Identity'</span></span>
- <span data-ttu-id="2de44-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-901">'Razor'</span></span>
- <span data-ttu-id="2de44-902">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-903">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-904">'Blazor'</span></span>
- <span data-ttu-id="2de44-905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-905">'Identity'</span></span>
- <span data-ttu-id="2de44-906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-906">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-907">'Razor'</span></span>
- <span data-ttu-id="2de44-908">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-908">'SignalR' uid:</span></span> 

<span data-ttu-id="2de44-909">------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-910">'Blazor'</span></span>
- <span data-ttu-id="2de44-911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-911">'Identity'</span></span>
- <span data-ttu-id="2de44-912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-912">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-913">'Razor'</span></span>
- <span data-ttu-id="2de44-914">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-915">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-916">'Blazor'</span></span>
- <span data-ttu-id="2de44-917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-917">'Identity'</span></span>
- <span data-ttu-id="2de44-918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-918">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-919">'Razor'</span></span>
- <span data-ttu-id="2de44-920">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-921">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-922">'Blazor'</span></span>
- <span data-ttu-id="2de44-923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-923">'Identity'</span></span>
- <span data-ttu-id="2de44-924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-924">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-925">'Razor'</span></span>
- <span data-ttu-id="2de44-926">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-927">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-928">'Blazor'</span></span>
- <span data-ttu-id="2de44-929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-929">'Identity'</span></span>
- <span data-ttu-id="2de44-930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-930">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-931">'Razor'</span></span>
- <span data-ttu-id="2de44-932">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-933">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-934">'Blazor'</span></span>
- <span data-ttu-id="2de44-935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-935">'Identity'</span></span>
- <span data-ttu-id="2de44-936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-936">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-937">'Razor'</span></span>
- <span data-ttu-id="2de44-938">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-939">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-940">'Blazor'</span></span>
- <span data-ttu-id="2de44-941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-941">'Identity'</span></span>
- <span data-ttu-id="2de44-942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-942">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-943">'Razor'</span></span>
- <span data-ttu-id="2de44-944">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-945">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-946">'Blazor'</span></span>
- <span data-ttu-id="2de44-947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-947">'Identity'</span></span>
- <span data-ttu-id="2de44-948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-948">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-949">'Razor'</span></span>
- <span data-ttu-id="2de44-950">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-951">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-952">'Blazor'</span></span>
- <span data-ttu-id="2de44-953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-953">'Identity'</span></span>
- <span data-ttu-id="2de44-954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-954">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-955">'Razor'</span></span>
- <span data-ttu-id="2de44-956">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-957">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-958">'Blazor'</span></span>
- <span data-ttu-id="2de44-959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-959">'Identity'</span></span>
- <span data-ttu-id="2de44-960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-960">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-961">'Razor'</span></span>
- <span data-ttu-id="2de44-962">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-963">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-964">'Blazor'</span></span>
- <span data-ttu-id="2de44-965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-965">'Identity'</span></span>
- <span data-ttu-id="2de44-966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-966">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-967">'Razor'</span></span>
- <span data-ttu-id="2de44-968">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-969">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-970">'Blazor'</span></span>
- <span data-ttu-id="2de44-971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-971">'Identity'</span></span>
- <span data-ttu-id="2de44-972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-972">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-973">'Razor'</span></span>
- <span data-ttu-id="2de44-974">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2de44-975">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2de44-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2de44-976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2de44-976">'Blazor'</span></span>
- <span data-ttu-id="2de44-977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2de44-977">'Identity'</span></span>
- <span data-ttu-id="2de44-978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2de44-978">'Let's Encrypt'</span></span>
- <span data-ttu-id="2de44-979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2de44-979">'Razor'</span></span>
- <span data-ttu-id="2de44-980">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2de44-980">'SignalR' uid:</span></span> 

<span data-ttu-id="2de44-981">--------------- | | `Http1`                    | Tylko HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="2de44-982">Może być używany z protokołem TLS lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="2de44-982">Can be used with or without TLS.</span></span> <span data-ttu-id="2de44-983">| | `Http2`                    | Tylko HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-983">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="2de44-984">Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="2de44-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="2de44-985">| | `Http1AndHttp2`            | HTTP/1.1 i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2de44-986">Protokół HTTP/2 wymaga połączenia protokołów TLS i [warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2de44-987">Domyślnym protokołem jest HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-987">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="2de44-988">Ograniczenia protokołu TLS dla protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="2de44-988">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2de44-989">TLS w wersji 1,2 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="2de44-989">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2de44-990">Ponowne negocjowanie wyłączone</span><span class="sxs-lookup"><span data-stu-id="2de44-990">Renegotiation disabled</span></span>
* <span data-ttu-id="2de44-991">Kompresja wyłączona</span><span class="sxs-lookup"><span data-stu-id="2de44-991">Compression disabled</span></span>
* <span data-ttu-id="2de44-992">Minimalne rozmiary tymczasowych kluczy wymiany:</span><span class="sxs-lookup"><span data-stu-id="2de44-992">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2de44-993">Krzywa eliptyczna Diffie'ego-Hellmana (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bitów minimum</span><span class="sxs-lookup"><span data-stu-id="2de44-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2de44-994">Ograniczone pole Diffie-Hellmana (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów</span><span class="sxs-lookup"><span data-stu-id="2de44-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2de44-995">Mechanizm szyfrowania nie został zabroniony</span><span class="sxs-lookup"><span data-stu-id="2de44-995">Cipher suite not blacklisted</span></span>

<span data-ttu-id="2de44-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="2de44-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2de44-997">Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000.</span><span class="sxs-lookup"><span data-stu-id="2de44-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2de44-998">Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="2de44-998">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="2de44-999">Opcjonalnie można utworzyć `IConnectionAdapter` implementację do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów:</span><span class="sxs-lookup"><span data-stu-id="2de44-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="2de44-1000">*Ustawianie protokołu z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-1000">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2de44-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2de44-1002">W poniższym przykładzie pliku *appSettings. JSON* jest ustanawiany domyślny protokół połączeń (http/1.1 i http/2) dla wszystkich punktów końcowych Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="2de44-1003">Poniższy przykład pliku konfiguracji ustanawia protokół połączenia dla określonego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="2de44-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2de44-1004">Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="2de44-1004">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2de44-1005">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="2de44-1005">Transport configuration</span></span>

<span data-ttu-id="2de44-1006">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="2de44-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2de44-1007">Jest to istotna zmiana dla aplikacji ASP.NET Core 2,0 uaktualniana do 2,1, które wywołują <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="2de44-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2de44-1008">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="2de44-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2de44-1009">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="2de44-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2de44-1010">Dla projektów, które wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="2de44-1010">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2de44-1011">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2de44-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2de44-1012">Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2de44-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2de44-1013">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="2de44-1013">URL prefixes</span></span>

<span data-ttu-id="2de44-1014">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2de44-1015">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2de44-1016">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2de44-1017">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1017">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2de44-1018">`0.0.0.0`jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="2de44-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2de44-1019">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1019">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2de44-1020">`[::]`jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2de44-1021">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1021">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2de44-1022">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="2de44-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2de44-1023">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2de44-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2de44-1024">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [protokołu HTTP. sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="2de44-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2de44-1025">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2de44-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2de44-1026">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1026">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2de44-1027">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2de44-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2de44-1028">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="2de44-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2de44-1029">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="2de44-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2de44-1030">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="2de44-1030">Host filtering</span></span>

<span data-ttu-id="2de44-1031">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2de44-1032">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2de44-1033">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="2de44-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2de44-1034">`Host`nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="2de44-1034">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2de44-1035">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-1035">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2de44-1036">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest zawarty w pakiecie [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub 2,2).</span><span class="sxs-lookup"><span data-stu-id="2de44-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2de44-1037">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="2de44-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2de44-1038">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="2de44-1038">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2de44-1039">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w pliku *appSettings. JSON* / *. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="2de44-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2de44-1040">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="2de44-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2de44-1041">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2de44-1041">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2de44-1042">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="2de44-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2de44-1043">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2de44-1044">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2de44-1045">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="2de44-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2de44-1046">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="2de44-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="2de44-1047">Kestrel to Międzyplatformowy [serwer sieci Web dla ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2de44-1048">Kestrel to serwer sieci Web, który jest domyślnie uwzględniony w szablonach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de44-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2de44-1049">Kestrel obsługuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="2de44-1049">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2de44-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2de44-1050">HTTPS</span></span>
* <span data-ttu-id="2de44-1051">Nieprzezroczyste uaktualnienie używane do włączania obsługi obiektów [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="2de44-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2de44-1052">Gniazda systemu UNIX w celu zapewnienia wysokiej wydajności w tle Nginx</span><span class="sxs-lookup"><span data-stu-id="2de44-1052">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="2de44-1053">Kestrel jest obsługiwana na wszystkich platformach i wersjach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de44-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2de44-1054">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2de44-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2de44-1055">Kiedy używać Kestrel z zwrotnym serwerem proxy</span><span class="sxs-lookup"><span data-stu-id="2de44-1055">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2de44-1056">Kestrel może być używana przez siebie lub z *odwrotnym serwerem proxy*, takich jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="2de44-1056">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2de44-1057">Odwrotny serwer proxy odbiera żądania HTTP z sieci i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1057">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2de44-1058">Kestrel używany jako serwer sieci Web z krawędzią (dostępną z Internetu):</span><span class="sxs-lookup"><span data-stu-id="2de44-1058">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2de44-1060">Kestrel używany w konfiguracji zwrotnego serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="2de44-1060">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2de44-1062">Konfiguracja, z serwerem proxy odwrotnych lub bez niego, jest obsługiwaną konfiguracją hostingu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1062">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2de44-1063">Kestrel używany jako serwer graniczny bez serwera proxy odwrotnego nie obsługuje udostępniania tego samego adresu IP i portu między wieloma procesami.</span><span class="sxs-lookup"><span data-stu-id="2de44-1063">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2de44-1064">Gdy Kestrel jest skonfigurowany do nasłuchiwania na porcie, Kestrel obsługuje cały ruch dla tego portu bez względu na nagłówki żądań `Host` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1064">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2de44-1065">Zwrotny serwer proxy, który może udostępniać porty, ma możliwość przekazywania żądań do Kestrel na unikatowym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="2de44-1065">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2de44-1066">Nawet jeśli odwrotny serwer proxy nie jest wymagany, może to być dobry wybór przy użyciu odwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1066">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2de44-1067">Zwrotny serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="2de44-1067">A reverse proxy:</span></span>

* <span data-ttu-id="2de44-1068">Może ograniczać uwidoczniony obszar publicznej powierzchni aplikacji, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="2de44-1068">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2de44-1069">Zapewnienie dodatkowej warstwy konfiguracji i obrony.</span><span class="sxs-lookup"><span data-stu-id="2de44-1069">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2de44-1070">Integracja z istniejącą infrastrukturą może być lepsza.</span><span class="sxs-lookup"><span data-stu-id="2de44-1070">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2de44-1071">Uprość Równoważenie obciążenia i konfigurację komunikacji zabezpieczonej (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2de44-1071">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2de44-1072">Tylko serwer zwrotny proxy wymaga certyfikatu X. 509, a serwer może komunikować się z serwerami aplikacji w sieci wewnętrznej przy użyciu zwykłego protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2de44-1072">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2de44-1073">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2de44-1073">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2de44-1074">Jak używać Kestrel w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2de44-1074">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2de44-1075">Pakiet [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2de44-1075">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2de44-1076">Szablony projektów ASP.NET Core domyślnie używają Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1076">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2de44-1077">W *program.cs*, kod szablonu wywołuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , który wywołuje się w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> tle.</span><span class="sxs-lookup"><span data-stu-id="2de44-1077">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="2de44-1078">Aby zapewnić dodatkową konfigurację po wywołaniu `CreateDefaultBuilder` , wywołaj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="2de44-1078">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2de44-1079">Więcej informacji na temat `CreateDefaultBuilder` i kompilowanie hosta znajduje się w sekcji *Konfigurowanie hosta* w temacie <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2de44-1079">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="2de44-1080">Opcje Kestrel</span><span class="sxs-lookup"><span data-stu-id="2de44-1080">Kestrel options</span></span>

<span data-ttu-id="2de44-1081">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1081">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2de44-1082">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1082">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2de44-1083">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1083">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2de44-1084">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="2de44-1084">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2de44-1085">Opcje Kestrel, które są konfigurowane w kodzie C# w poniższych przykładach, można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-1085">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2de44-1086">Na przykład dostawca konfiguracji plików może załadować konfigurację Kestrel z pliku *appSettings. JSON* lub *appSettings. { Environment} plik JSON* :</span><span class="sxs-lookup"><span data-stu-id="2de44-1086">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2de44-1087">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2de44-1087">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2de44-1088">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2de44-1088">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2de44-1089">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1089">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2de44-1090">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2de44-1090">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2de44-1091">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-1091">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2de44-1092">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="2de44-1092">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2de44-1093">W programie *program.cs*Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2de44-1093">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2de44-1094">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2de44-1094">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2de44-1095">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="2de44-1095">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2de44-1096">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="2de44-1096">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2de44-1097">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="2de44-1097">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="2de44-1098">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="2de44-1098">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2de44-1099">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2de44-1099">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="2de44-1100">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2de44-1100">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2de44-1101">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1101">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="2de44-1102">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="2de44-1102">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2de44-1103">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-1103">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2de44-1104">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2de44-1104">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2de44-1105">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="2de44-1105">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2de44-1106">Oto przykład, który pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="2de44-1106">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="2de44-1107">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="2de44-1107">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2de44-1108">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1108">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2de44-1109">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="2de44-1109">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2de44-1110">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony, ponieważ program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="2de44-1110">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2de44-1111">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="2de44-1111">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2de44-1112">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="2de44-1112">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2de44-1113">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, przez który Kestrel nadaje klientowi zwiększenie jego szybkości wysyłania do minimum; Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="2de44-1113">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2de44-1114">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością.</span><span class="sxs-lookup"><span data-stu-id="2de44-1114">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2de44-1115">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="2de44-1115">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2de44-1116">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-1116">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2de44-1117">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1117">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2de44-1118">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2de44-1118">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="2de44-1119">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="2de44-1119">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2de44-1120">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1120">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2de44-1121">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="2de44-1121">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="2de44-1122">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="2de44-1122">Synchronous I/O</span></span>

<span data-ttu-id="2de44-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2de44-1124">Wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1124">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2de44-1125">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="2de44-1125">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2de44-1126">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1126">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2de44-1127">Poniższy przykład wyłącza synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="2de44-1127">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="2de44-1128">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="2de44-1128">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2de44-1129">Konfiguracja punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="2de44-1129">Endpoint configuration</span></span>

<span data-ttu-id="2de44-1130">Domyślnie ASP.NET Core wiąże się z:</span><span class="sxs-lookup"><span data-stu-id="2de44-1130">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2de44-1131">`https://localhost:5001`(gdy obecny jest lokalny certyfikat programistyczny)</span><span class="sxs-lookup"><span data-stu-id="2de44-1131">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2de44-1132">Określ adresy URL przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="2de44-1132">Specify URLs using the:</span></span>

* <span data-ttu-id="2de44-1133">`ASPNETCORE_URLS`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="2de44-1133">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2de44-1134">`--urls`argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1134">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2de44-1135">`urls`klucz konfiguracji hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-1135">`urls` host configuration key.</span></span>
* <span data-ttu-id="2de44-1136">`UseUrls`Metoda rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1136">`UseUrls` extension method.</span></span>

<span data-ttu-id="2de44-1137">Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat).</span><span class="sxs-lookup"><span data-stu-id="2de44-1137">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2de44-1138">Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2de44-1138">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2de44-1139">Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2de44-1139">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2de44-1140">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="2de44-1140">A development certificate is created:</span></span>

* <span data-ttu-id="2de44-1141">Po zainstalowaniu [zestaw .NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="2de44-1141">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2de44-1142">Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .</span><span class="sxs-lookup"><span data-stu-id="2de44-1142">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2de44-1143">Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1143">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2de44-1144">Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2de44-1144">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2de44-1145">Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1145">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2de44-1146">`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2de44-1146">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2de44-1147">`KestrelServerOptions`skonfigurować</span><span class="sxs-lookup"><span data-stu-id="2de44-1147">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2de44-1148">ConfigureEndpointDefaults (Akcja \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2de44-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2de44-1149">Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1149">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2de44-1150">Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="2de44-1150">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2de44-1151">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2de44-1151">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2de44-1152">ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2de44-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2de44-1153">Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-1153">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2de44-1154">Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.</span><span class="sxs-lookup"><span data-stu-id="2de44-1154">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2de44-1155">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2de44-1155">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2de44-1156">Konfiguruj (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2de44-1156">Configure(IConfiguration)</span></span>

<span data-ttu-id="2de44-1157">Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-1157">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2de44-1158">Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1158">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2de44-1159">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2de44-1159">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2de44-1160">Skonfiguruj Kestrel do korzystania z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-1160">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2de44-1161">`ListenOptions.UseHttps`rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="2de44-1161">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2de44-1162">`UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="2de44-1162">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2de44-1163">Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="2de44-1163">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2de44-1164">`ListenOptions.UseHttps`wejściowe</span><span class="sxs-lookup"><span data-stu-id="2de44-1164">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2de44-1165">`filename`jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-1165">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2de44-1166">`password`to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.</span><span class="sxs-lookup"><span data-stu-id="2de44-1166">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2de44-1167">`configureOptions`jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1167">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2de44-1168">Zwraca wartość `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1168">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2de44-1169">`storeName`to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-1169">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2de44-1170">`subject`to nazwa podmiotu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1170">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2de44-1171">`allowInvalid`wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="2de44-1171">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2de44-1172">`location`jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-1172">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2de44-1173">`serverCertificate`jest certyfikatem X. 509.</span><span class="sxs-lookup"><span data-stu-id="2de44-1173">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2de44-1174">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2de44-1174">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2de44-1175">Należy podać co najmniej certyfikat domyślny.</span><span class="sxs-lookup"><span data-stu-id="2de44-1175">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2de44-1176">Obsługiwane konfiguracje opisane dalej:</span><span class="sxs-lookup"><span data-stu-id="2de44-1176">Supported configurations described next:</span></span>

* <span data-ttu-id="2de44-1177">Brak konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2de44-1177">No configuration</span></span>
* <span data-ttu-id="2de44-1178">Zastąp domyślny certyfikat z konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2de44-1178">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2de44-1179">Zmień wartości domyślne w kodzie</span><span class="sxs-lookup"><span data-stu-id="2de44-1179">Change the defaults in code</span></span>

<span data-ttu-id="2de44-1180">*Brak konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-1180">*No configuration*</span></span>

<span data-ttu-id="2de44-1181">Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).</span><span class="sxs-lookup"><span data-stu-id="2de44-1181">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2de44-1182">*Zastąp domyślny certyfikat z konfiguracji*</span><span class="sxs-lookup"><span data-stu-id="2de44-1182">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2de44-1183">`CreateDefaultBuilder`wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1183">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2de44-1184">Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1184">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2de44-1185">Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1185">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2de44-1186">W poniższym przykładzie pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2de44-1186">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2de44-1187">Ustaw **AllowInvalid** na, `true` Aby zezwolić na korzystanie z nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).</span><span class="sxs-lookup"><span data-stu-id="2de44-1187">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2de44-1188">Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (**HttpsDefaultCert** w poniższym przykładzie) powracają do certyfikatu zdefiniowanego w obszarze **Certyfikaty** > **domyślne** lub certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="2de44-1188">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2de44-1189">Alternatywą dla korzystania z **ścieżki** i **hasła** dla dowolnego węzła certyfikatu jest określenie certyfikatu przy użyciu pól magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1189">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2de44-1190">Certyfikat domyślny można na **przykład**  >  **Default** określić jako:</span><span class="sxs-lookup"><span data-stu-id="2de44-1190">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2de44-1191">Uwagi dotyczące schematu:</span><span class="sxs-lookup"><span data-stu-id="2de44-1191">Schema notes:</span></span>

* <span data-ttu-id="2de44-1192">W nazwach punktów końcowych nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="2de44-1192">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2de44-1193">Na przykład `HTTPS` i `Https` są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-1193">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2de44-1194">`Url`Parametr jest wymagany dla każdego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1194">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2de44-1195">Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.</span><span class="sxs-lookup"><span data-stu-id="2de44-1195">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2de44-1196">Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich.</span><span class="sxs-lookup"><span data-stu-id="2de44-1196">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2de44-1197">Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-1197">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2de44-1198">`Certificate`Sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="2de44-1198">The `Certificate` section is optional.</span></span> <span data-ttu-id="2de44-1199">Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane we wcześniejszych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="2de44-1199">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2de44-1200">Jeśli żadne wartości domyślne nie są dostępne, serwer zgłasza wyjątek i nie może się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="2de44-1200">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2de44-1201">`Certificate`Sekcja obsługuje zarówno hasło **ścieżki** &ndash; **Password** , jak i certyfikaty magazynu **podmiotu** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="2de44-1201">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2de44-1202">W ten sposób można zdefiniować dowolną liczbę punktów końcowych, dopóki nie spowoduje to konfliktów portów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1202">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2de44-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))`zwraca `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="2de44-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2de44-1204">`KestrelServerOptions.ConfigurationLoader`można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2de44-1204">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2de44-1205">Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-1205">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2de44-1206">Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją.</span><span class="sxs-lookup"><span data-stu-id="2de44-1206">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2de44-1207">Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="2de44-1207">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2de44-1208">Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="2de44-1208">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2de44-1209">`KestrelConfigurationLoader`odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1209">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2de44-1210">Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2de44-1210">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2de44-1211">*Zmień wartości domyślne w kodzie*</span><span class="sxs-lookup"><span data-stu-id="2de44-1211">*Change the defaults in code*</span></span>

<span data-ttu-id="2de44-1212">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1212">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2de44-1213">`ConfigureEndpointDefaults`i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2de44-1213">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2de44-1214">*Obsługa usługi Kestrel dla SNI*</span><span class="sxs-lookup"><span data-stu-id="2de44-1214">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2de44-1215">[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie.</span><span class="sxs-lookup"><span data-stu-id="2de44-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2de44-1216">Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2de44-1216">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2de44-1217">Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2de44-1217">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2de44-1218">Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1218">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2de44-1219">Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1219">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2de44-1220">Obsługa SNI wymaga:</span><span class="sxs-lookup"><span data-stu-id="2de44-1220">SNI support requires:</span></span>

* <span data-ttu-id="2de44-1221">Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="2de44-1221">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2de44-1222">W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1222">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2de44-1223">`name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2de44-1223">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2de44-1224">Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1224">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2de44-1225">Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1225">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2de44-1226">Rejestrowanie połączeń</span><span class="sxs-lookup"><span data-stu-id="2de44-1226">Connection logging</span></span>

<span data-ttu-id="2de44-1227">Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1227">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2de44-1228">Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1228">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2de44-1229">Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="2de44-1229">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2de44-1230">Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="2de44-1230">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2de44-1231">Powiąż z gniazdem TCP</span><span class="sxs-lookup"><span data-stu-id="2de44-1231">Bind to a TCP socket</span></span>

<span data-ttu-id="2de44-1232"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="2de44-1232">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="2de44-1233">Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2de44-1233">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2de44-1234">Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="2de44-1234">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2de44-1235">Powiąż z gniazdem systemu UNIX</span><span class="sxs-lookup"><span data-stu-id="2de44-1235">Bind to a Unix socket</span></span>

<span data-ttu-id="2de44-1236">Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="2de44-1236">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="2de44-1237">W pliku Nginx confiuguration Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1237">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2de44-1238">`{KESTREL SOCKET}`jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> przykład `kestrel-test.sock` w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="2de44-1238">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2de44-1239">Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2de44-1239">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2de44-1240">Port 0</span><span class="sxs-lookup"><span data-stu-id="2de44-1240">Port 0</span></span>

<span data-ttu-id="2de44-1241">Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem.</span><span class="sxs-lookup"><span data-stu-id="2de44-1241">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2de44-1242">W poniższym przykładzie pokazano, jak określić, który port Kestrel faktycznie powiązany w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="2de44-1242">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2de44-1243">Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2de44-1243">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2de44-1244">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="2de44-1244">Limitations</span></span>

<span data-ttu-id="2de44-1245">Skonfiguruj punkty końcowe przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="2de44-1245">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2de44-1246">`--urls`argument wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="2de44-1246">`--urls` command-line argument</span></span>
* <span data-ttu-id="2de44-1247">`urls`klucz konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="2de44-1247">`urls` host configuration key</span></span>
* <span data-ttu-id="2de44-1248">`ASPNETCORE_URLS`Zmienna środowiskowa</span><span class="sxs-lookup"><span data-stu-id="2de44-1248">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2de44-1249">Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de44-1249">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2de44-1250">Należy jednak pamiętać o następujących ograniczeniach:</span><span class="sxs-lookup"><span data-stu-id="2de44-1250">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2de44-1251">Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład przy użyciu `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym temacie).</span><span class="sxs-lookup"><span data-stu-id="2de44-1251">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2de44-1252">Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-1252">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2de44-1253">Konfiguracja punktu końcowego usług IIS</span><span class="sxs-lookup"><span data-stu-id="2de44-1253">IIS endpoint configuration</span></span>

<span data-ttu-id="2de44-1254">W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1254">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2de44-1255">Aby uzyskać więcej informacji, zobacz temat [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="2de44-1255">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2de44-1256">Konfiguracja transportu</span><span class="sxs-lookup"><span data-stu-id="2de44-1256">Transport configuration</span></span>

<span data-ttu-id="2de44-1257">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="2de44-1257">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2de44-1258">Jest to istotna zmiana dla aplikacji ASP.NET Core 2,0 uaktualniana do 2,1, które wywołują <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> i zależą od jednego z następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="2de44-1258">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2de44-1259">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (bezpośrednie odwołanie do pakietu)</span><span class="sxs-lookup"><span data-stu-id="2de44-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2de44-1260">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="2de44-1260">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2de44-1261">Dla projektów, które wymagają użycia Libuv:</span><span class="sxs-lookup"><span data-stu-id="2de44-1261">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2de44-1262">Dodaj zależność dla pakietu [Microsoft. AspNetCore. Server. Kestrel. transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2de44-1262">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2de44-1263">Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2de44-1263">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2de44-1264">Prefiksy adresów URL</span><span class="sxs-lookup"><span data-stu-id="2de44-1264">URL prefixes</span></span>

<span data-ttu-id="2de44-1265">W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1265">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2de44-1266">Tylko prefiksy adresów URL HTTP są prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="2de44-1266">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2de44-1267">Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1267">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2de44-1268">Adres IPv4 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1268">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2de44-1269">`0.0.0.0`jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.</span><span class="sxs-lookup"><span data-stu-id="2de44-1269">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2de44-1270">Adres IPv6 z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1270">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2de44-1271">`[::]`jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2de44-1271">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2de44-1272">Nazwa hosta z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1272">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2de44-1273">Nazwy hostów, `*` i `+` , nie są specjalne.</span><span class="sxs-lookup"><span data-stu-id="2de44-1273">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2de44-1274">Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2de44-1274">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2de44-1275">Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [protokołu HTTP. sys](xref:fundamentals/servers/httpsys) lub odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="2de44-1275">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2de44-1276">Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2de44-1276">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2de44-1277">`localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu</span><span class="sxs-lookup"><span data-stu-id="2de44-1277">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2de44-1278">Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2de44-1278">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2de44-1279">Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="2de44-1279">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2de44-1280">Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="2de44-1280">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2de44-1281">Filtrowanie hostów</span><span class="sxs-lookup"><span data-stu-id="2de44-1281">Host filtering</span></span>

<span data-ttu-id="2de44-1282">Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-1282">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2de44-1283">Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1283">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2de44-1284">Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP.</span><span class="sxs-lookup"><span data-stu-id="2de44-1284">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2de44-1285">`Host`nagłówki nie są sprawdzane.</span><span class="sxs-lookup"><span data-stu-id="2de44-1285">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2de44-1286">Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="2de44-1286">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2de44-1287">Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest zawarty w pakiecie [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub 2,2).</span><span class="sxs-lookup"><span data-stu-id="2de44-1287">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2de44-1288">Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> następujące wywołania:</span><span class="sxs-lookup"><span data-stu-id="2de44-1288">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2de44-1289">Programowe filtrowanie hosta jest domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="2de44-1289">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2de44-1290">Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w pliku *appSettings. JSON* / *. \<EnvironmentName> kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="2de44-1290">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2de44-1291">Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:</span><span class="sxs-lookup"><span data-stu-id="2de44-1291">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2de44-1292">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2de44-1292">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2de44-1293">[Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję.</span><span class="sxs-lookup"><span data-stu-id="2de44-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2de44-1294">Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="2de44-1294">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2de44-1295">Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1295">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2de44-1296">Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.</span><span class="sxs-lookup"><span data-stu-id="2de44-1296">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2de44-1297">Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="2de44-1297">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="2de44-1298">Opróżnianie żądań HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="2de44-1298">HTTP/1.1 request draining</span></span>

<span data-ttu-id="2de44-1299">Otwieranie połączeń HTTP jest czasochłonne.</span><span class="sxs-lookup"><span data-stu-id="2de44-1299">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="2de44-1300">W przypadku protokołu HTTPS jest to również czasochłonne zasoby.</span><span class="sxs-lookup"><span data-stu-id="2de44-1300">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="2de44-1301">W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2de44-1301">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="2de44-1302">Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1302">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="2de44-1303">Aplikacja nie zawsze zużywa treści żądania, na przykład żądania, w `POST` których serwer zwraca przekierowanie lub odpowiedź 404.</span><span class="sxs-lookup"><span data-stu-id="2de44-1303">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="2de44-1304">W `POST` przypadku przekierowania — przypadek:</span><span class="sxs-lookup"><span data-stu-id="2de44-1304">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="2de44-1305">Klient mógł już wysłać część `POST` danych.</span><span class="sxs-lookup"><span data-stu-id="2de44-1305">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="2de44-1306">Serwer zapisuje odpowiedź 301.</span><span class="sxs-lookup"><span data-stu-id="2de44-1306">The server writes the 301 response.</span></span>
* <span data-ttu-id="2de44-1307">Nie można użyć połączenia dla nowego żądania, dopóki `POST` dane z poprzedniej treści żądania nie zostały w pełni odczytane.</span><span class="sxs-lookup"><span data-stu-id="2de44-1307">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="2de44-1308">Kestrel próbuje opróżnić treść żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1308">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="2de44-1309">Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1309">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="2de44-1310">Proces opróżniania polega na tym, że tradoff między zezwoleniem na ponowne użycie połączenia i czasem potrzebny do opróżnienia pozostałych danych:</span><span class="sxs-lookup"><span data-stu-id="2de44-1310">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="2de44-1311">Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1311">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="2de44-1312">Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="2de44-1312">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="2de44-1313">Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu.</span><span class="sxs-lookup"><span data-stu-id="2de44-1313">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="2de44-1314">Na przykład klienci mogą mieć restrykcyjne limity danych, więc ograniczenie przekazywania danych może być priorytetem.</span><span class="sxs-lookup"><span data-stu-id="2de44-1314">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="2de44-1315">W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="2de44-1315">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="2de44-1316">Istnieją zastrzeżenia dotyczące wywoływania `Abort` :</span><span class="sxs-lookup"><span data-stu-id="2de44-1316">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="2de44-1317">Tworzenie nowych połączeń może być powolne i kosztowne.</span><span class="sxs-lookup"><span data-stu-id="2de44-1317">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="2de44-1318">Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1318">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="2de44-1319">Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.</span><span class="sxs-lookup"><span data-stu-id="2de44-1319">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="2de44-1320">Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany.</span><span class="sxs-lookup"><span data-stu-id="2de44-1320">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="2de44-1321">Na przykład Wywołaj, `Abort` czy złośliwi klienci próbują uzyskać `POST` dane, czy w kodzie klienta występuje usterka powodująca duże lub liczne żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1321">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="2de44-1322">Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="2de44-1322">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="2de44-1323">Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2de44-1323">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="2de44-1324">Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1324">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="2de44-1325">Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia.</span><span class="sxs-lookup"><span data-stu-id="2de44-1325">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="2de44-1326">Nie ma zastosowania pięć sekund limitu czasu opróżniania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1326">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="2de44-1327">Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2de44-1327">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="2de44-1328">Dodatkowe ramki danych treści żądania są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="2de44-1328">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="2de44-1329">Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="2de44-1329">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="2de44-1330">Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.</span><span class="sxs-lookup"><span data-stu-id="2de44-1330">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2de44-1331">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2de44-1331">Additional resources</span></span>

* <span data-ttu-id="2de44-1332">W przypadku korzystania z gniazd systemu UNIX w systemie Linux gniazdo nie jest automatycznie usuwane podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2de44-1332">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="2de44-1333">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/14134)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="2de44-1333">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="2de44-1334">RFC 7230: Składnia i Routing komunikatu (sekcja 5,4: Host)</span><span class="sxs-lookup"><span data-stu-id="2de44-1334">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
