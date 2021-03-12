---
title: Skonfiguruj opcje ASP.NET Core serwera sieci Web Kestrel
author: rick-anderson
description: Dowiedz się więcej na temat konfigurowania opcji Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 48b4af2dfc925c4444c2bd0e43d04f2f0f3ddd17
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587011"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="6626e-103">Skonfiguruj opcje ASP.NET Core serwera sieci Web Kestrel</span><span class="sxs-lookup"><span data-stu-id="6626e-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="6626e-104">Serwer sieci Web Kestrel ma opcje konfiguracji ograniczeń, które są szczególnie przydatne w przypadku wdrożeń mających dostęp do Internetu.</span><span class="sxs-lookup"><span data-stu-id="6626e-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6626e-105">Aby zapewnić dodatkową konfigurację po wywołaniu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> , użyj <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A> :</span><span class="sxs-lookup"><span data-stu-id="6626e-105">To provide additional configuration after calling <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>, use <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>:</span></span>

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

<span data-ttu-id="6626e-106">Ustaw ograniczenia <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> właściwości <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> klasy.</span><span class="sxs-lookup"><span data-stu-id="6626e-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6626e-107">`Limits`Właściwość przechowuje wystąpienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> klasy.</span><span class="sxs-lookup"><span data-stu-id="6626e-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6626e-108">W poniższych przykładach użyto <xref:Microsoft.AspNetCore.Server.Kestrel.Core> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="6626e-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6626e-109">W przykładach przedstawionych w dalszej części tego artykułu opcje Kestrel są konfigurowane w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="6626e-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="6626e-110">Opcje Kestrel można również ustawić za pomocą [dostawcy konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6626e-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6626e-111">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) może załadować konfigurację Kestrel z `appsettings.json` `appsettings.{Environment}.json` pliku lub:</span><span class="sxs-lookup"><span data-stu-id="6626e-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an `appsettings.json` or `appsettings.{Environment}.json` file:</span></span>

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
> <span data-ttu-id="6626e-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[konfigurację punktu końcowego](xref:fundamentals/servers/kestrel/endpoints) można konfigurować z poziomu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="6626e-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="6626e-113">Pozostała konfiguracja Kestrel musi być skonfigurowana w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="6626e-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="6626e-114">Skorzystaj z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="6626e-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6626e-115">Skonfiguruj Kestrel w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6626e-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6626e-116">Wstrzyknąć wystąpienie `IConfiguration` do `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="6626e-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6626e-117">W poniższym przykładzie przyjęto założenie, że wprowadzona konfiguracja jest przypisana do `Configuration` właściwości.</span><span class="sxs-lookup"><span data-stu-id="6626e-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6626e-118">W programie `Startup.ConfigureServices` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6626e-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6626e-119">Skonfiguruj Kestrel podczas kompilowania hosta:</span><span class="sxs-lookup"><span data-stu-id="6626e-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6626e-120">W programie `Program.cs` Załaduj `Kestrel` sekcję Konfiguracja do konfiguracji programu Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6626e-120">In `Program.cs`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6626e-121">Obie powyższe podejścia współpracują z dowolnym [dostawcą konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6626e-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="6626e-122">Ogólne limity</span><span class="sxs-lookup"><span data-stu-id="6626e-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6626e-123">Limit czasu utrzymywania aktywności</span><span class="sxs-lookup"><span data-stu-id="6626e-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6626e-124">Pobiera lub ustawia [limit czasu utrzymywania aktywności](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6626e-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6626e-125">Wartość domyślna to 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="6626e-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6626e-126">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="6626e-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6626e-127">Maksymalna liczba współbieżnych otwartych połączeń TCP można ustawić dla całej aplikacji przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6626e-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6626e-128">Istnieje oddzielny limit połączeń, które zostały uaktualnione z protokołu HTTP lub HTTPS do innego protokołu (na przykład w żądaniu usługi WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6626e-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6626e-129">Po uaktualnieniu połączenia nie jest ono wliczane do `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="6626e-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6626e-130">Maksymalna liczba połączeń jest domyślnie nieograniczona (null).</span><span class="sxs-lookup"><span data-stu-id="6626e-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6626e-131">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="6626e-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6626e-132">Domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6626e-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6626e-133">Zalecanym podejściem do zastąpienia limitu w aplikacji ASP.NET Core MVC jest użycie <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atrybutu dla metody akcji:</span><span class="sxs-lookup"><span data-stu-id="6626e-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6626e-134">Poniższy przykład pokazuje, jak skonfigurować ograniczenie dla aplikacji w każdym żądaniu:</span><span class="sxs-lookup"><span data-stu-id="6626e-134">The following example shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6626e-135">Zastąp ustawienie określonego żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="6626e-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6626e-136">Wyjątek jest generowany, jeśli aplikacja skonfiguruje limit żądania po rozpoczęciu uruchamiania aplikacji w celu odczytania żądania.</span><span class="sxs-lookup"><span data-stu-id="6626e-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6626e-137">Istnieje `IsReadOnly` Właściwość, która wskazuje `MaxRequestBodySize` , czy właściwość jest w stanie tylko do odczytu, co oznacza, że jest zbyt późno, aby skonfigurować limit.</span><span class="sxs-lookup"><span data-stu-id="6626e-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6626e-138">Gdy aplikacja jest uruchamiana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), limit rozmiaru treści żądania Kestrel jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="6626e-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="6626e-139">Program IIS już ustawia limit.</span><span class="sxs-lookup"><span data-stu-id="6626e-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6626e-140">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="6626e-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6626e-141">Kestrel sprawdza co sekundę, jeśli dane są odbierane z określoną szybkością w bajtach na sekundę.</span><span class="sxs-lookup"><span data-stu-id="6626e-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6626e-142">Jeśli szybkość spadnie poniżej wartości minimalnej, upłynął limit czasu połączenia. Okres prolongaty to czas, jaki Kestrel pozwala klientowi zwiększyć jego częstotliwość wysyłania do minimum.</span><span class="sxs-lookup"><span data-stu-id="6626e-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="6626e-143">Ta częstotliwość nie jest sprawdzana w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="6626e-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="6626e-144">Okres prolongaty pozwala uniknąć porzucania połączeń, które początkowo wysyłają dane z niską szybkością z powodu spowolnienia TCP.</span><span class="sxs-lookup"><span data-stu-id="6626e-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="6626e-145">Domyślna stawka minimalna to 240 bajtów na sekundę z 5-sekundowym okresem prolongaty.</span><span class="sxs-lookup"><span data-stu-id="6626e-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="6626e-146">Minimalna stawka dotyczy także odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6626e-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6626e-147">Kod określający limit żądań i limit odpowiedzi jest taki sam, z wyjątkiem `RequestBody` `Response` właściwości i nazwy interfejsów.</span><span class="sxs-lookup"><span data-stu-id="6626e-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6626e-148">Oto przykład pokazujący sposób konfigurowania minimalnych stawek danych w programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="6626e-148">Here's an example that shows how to configure the minimum data rates in `Program.cs`:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="6626e-149">Przesłoń minimalne limity szybkości dla żądania w oprogramowaniu pośredniczącym:</span><span class="sxs-lookup"><span data-stu-id="6626e-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6626e-150"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odwołania w poprzednim przykładzie nie są obecne w <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> przypadku żądań HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6626e-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> for HTTP/2 requests.</span></span> <span data-ttu-id="6626e-151">Modyfikowanie limitów szybkości dla każdego żądania nie jest ogólnie obsługiwane w przypadku protokołu HTTP/2 z powodu obsługi żądania multipleksowania żądań.</span><span class="sxs-lookup"><span data-stu-id="6626e-151">Modifying rate limits on a per-request basis isn't generally supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6626e-152">Jednak <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> nadal występuje `HttpContext.Features` dla żądań HTTP/2, ponieważ limit liczby odczytów nadal można *wyłączyć całkowicie* dla każdego żądania przez ustawienie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> `null` nawet dla żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6626e-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="6626e-153">Próba odczytania `IHttpMinRequestBodyDataRateFeature.MinDataRate` lub próby ustawienia jej na wartość inną niż `null` spowoduje <xref:System.NotSupportedException> zgłoszenie żądania HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6626e-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a <xref:System.NotSupportedException> being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="6626e-154">Limity szybkości dla całego serwera skonfigurowane za pośrednictwem <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> nadal mają zastosowanie do połączeń HTTP/1. x i http/2.</span><span class="sxs-lookup"><span data-stu-id="6626e-154">Server-wide rate limits configured via <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6626e-155">Limit czasu nagłówków żądań</span><span class="sxs-lookup"><span data-stu-id="6626e-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6626e-156">Pobiera lub ustawia maksymalny czas, przez jaki serwer spędza nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="6626e-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6626e-157">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="6626e-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="6626e-158">Limity protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6626e-158">HTTP/2 limits</span></span>

<span data-ttu-id="6626e-159">Limity w tej sekcji są ustawione na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6626e-159">The limits in this section are set on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType>.</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6626e-160">Maksymalna liczba strumieni na połączenie</span><span class="sxs-lookup"><span data-stu-id="6626e-160">Maximum streams per connection</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>

<span data-ttu-id="6626e-161">Ogranicza liczbę współbieżnych strumieni żądań na połączenie HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6626e-161">Limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6626e-162">Odrzucone strumienie są nadmiarowe.</span><span class="sxs-lookup"><span data-stu-id="6626e-162">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="6626e-163">Wartość domyślna to 100.</span><span class="sxs-lookup"><span data-stu-id="6626e-163">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6626e-164">Rozmiar tabeli nagłówka</span><span class="sxs-lookup"><span data-stu-id="6626e-164">Header table size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.HeaderTableSize>

<span data-ttu-id="6626e-165">Dekoder HPACK dekompresuje nagłówki HTTP dla połączeń HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6626e-165">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6626e-166">`HeaderTableSize` ogranicza rozmiar tabeli kompresji nagłówka używanej przez dekoder HPACK.</span><span class="sxs-lookup"><span data-stu-id="6626e-166">`HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6626e-167">Wartość jest podawana w oktetach i musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="6626e-167">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="6626e-168">Wartość domyślna to 4096.</span><span class="sxs-lookup"><span data-stu-id="6626e-168">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6626e-169">Maksymalny rozmiar ramki</span><span class="sxs-lookup"><span data-stu-id="6626e-169">Maximum frame size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxFrameSize>

<span data-ttu-id="6626e-170">Wskazuje maksymalny dozwolony rozmiar ładunku ramki połączenia HTTP/2 odebranego lub wysłanego przez serwer.</span><span class="sxs-lookup"><span data-stu-id="6626e-170">Indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="6626e-171">Wartość jest podawana w oktetach i musi należeć do przedziału od 2 ^ 14 (16 384) do 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="6626e-171">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="6626e-172">Wartość domyślna to 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="6626e-172">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6626e-173">Maksymalny rozmiar nagłówka żądania</span><span class="sxs-lookup"><span data-stu-id="6626e-173">Maximum request header size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxRequestHeaderFieldSize>

<span data-ttu-id="6626e-174">Wskazuje maksymalny dozwolony rozmiar w oktetach wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="6626e-174">Indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6626e-175">Ten limit dotyczy zarówno nazwy, jak i wartości w skompresowanych i nieskompresowanych reprezentacji.</span><span class="sxs-lookup"><span data-stu-id="6626e-175">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6626e-176">Wartość musi być większa od zera (0).</span><span class="sxs-lookup"><span data-stu-id="6626e-176">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="6626e-177">Wartość domyślna to 8 192.</span><span class="sxs-lookup"><span data-stu-id="6626e-177">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6626e-178">Początkowy rozmiar okna połączenia</span><span class="sxs-lookup"><span data-stu-id="6626e-178">Initial connection window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialConnectionWindowSize>

<span data-ttu-id="6626e-179">Wskazuje maksymalne dane dotyczące treści żądania w bajtach, które są agregowane w buforach serwera w ramach wszystkich żądań (strumieni) na połączenie.</span><span class="sxs-lookup"><span data-stu-id="6626e-179">Indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6626e-180">Żądania są również ograniczone przez `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="6626e-180">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6626e-181">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6626e-181">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="6626e-182">Wartość domyślna to 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="6626e-182">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6626e-183">Rozmiar początkowego okna strumienia</span><span class="sxs-lookup"><span data-stu-id="6626e-183">Initial stream window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialStreamWindowSize>

<span data-ttu-id="6626e-184">Wskazuje maksymalne dane dotyczące treści żądania w bajtach bufory serwera w ramach żądania (Stream).</span><span class="sxs-lookup"><span data-stu-id="6626e-184">Indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6626e-185">Żądania są również ograniczone przez [`InitialConnectionWindowSize`](#initial-connection-window-size) .</span><span class="sxs-lookup"><span data-stu-id="6626e-185">Requests are also limited by [`InitialConnectionWindowSize`](#initial-connection-window-size).</span></span> <span data-ttu-id="6626e-186">Wartość musi być większa lub równa 65 535 i mniejsza niż 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6626e-186">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="6626e-187">Wartość domyślna to 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="6626e-187">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="6626e-188">Konfiguracja polecenia ping Keep Alive protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6626e-188">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="6626e-189">Kestrel można skonfigurować do wysyłania poleceń ping protokołu HTTP/2 do podłączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="6626e-189">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="6626e-190">Polecenia ping protokołu HTTP/2 współpracują z wieloma celami:</span><span class="sxs-lookup"><span data-stu-id="6626e-190">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="6626e-191">Utrzymywanie bezczynnych połączeń.</span><span class="sxs-lookup"><span data-stu-id="6626e-191">Keep idle connections alive.</span></span> <span data-ttu-id="6626e-192">Niektórzy klienci i serwery proxy zamykają połączenia, które są w stanie bezczynności.</span><span class="sxs-lookup"><span data-stu-id="6626e-192">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="6626e-193">Polecenia ping protokołu HTTP/2 są uznawane za działania w ramach połączenia i uniemożliwiają zamknięcie połączenia w trybie bezczynności.</span><span class="sxs-lookup"><span data-stu-id="6626e-193">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="6626e-194">Zamknij połączenia w złej kondycji.</span><span class="sxs-lookup"><span data-stu-id="6626e-194">Close unhealthy connections.</span></span> <span data-ttu-id="6626e-195">Połączenia, w których klient nie odpowiada na polecenie "Keep Alive" w skonfigurowanym czasie, są zamykane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="6626e-195">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="6626e-196">Dostępne są dwie opcje konfiguracji związane z poleceniami "Keep Alive" protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="6626e-196">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="6626e-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay><xref:System.TimeSpan>umożliwia skonfigurowanie interwału ping.</span><span class="sxs-lookup"><span data-stu-id="6626e-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> is a <xref:System.TimeSpan> that configures the ping interval.</span></span> <span data-ttu-id="6626e-198">Serwer wysyła polecenie ping do klienta, jeśli nie otrzyma żadnej ramki przez ten okres.</span><span class="sxs-lookup"><span data-stu-id="6626e-198">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="6626e-199">Polecenia ping utrzymywania aktywności są wyłączone, gdy ta opcja jest ustawiona na <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6626e-199">Keep alive pings are disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6626e-200">Wartość domyślna to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6626e-200">The default value is <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="6626e-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout><xref:System.TimeSpan>umożliwia skonfigurowanie limitu czasu polecenia ping.</span><span class="sxs-lookup"><span data-stu-id="6626e-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> is a <xref:System.TimeSpan> that configures the ping timeout.</span></span> <span data-ttu-id="6626e-202">Jeśli serwer nie odbiera żadnych ramek, takich jak odpowiedzi ping, podczas tego limitu czasu połączenie jest zamknięte.</span><span class="sxs-lookup"><span data-stu-id="6626e-202">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="6626e-203">Limit czasu utrzymywania aktywności jest wyłączony, gdy ta opcja jest ustawiona na <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6626e-203">Keep alive timeout is disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6626e-204">Wartość domyślna to 20 sekund.</span><span class="sxs-lookup"><span data-stu-id="6626e-204">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingDelay = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="6626e-205">Inne opcje</span><span class="sxs-lookup"><span data-stu-id="6626e-205">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="6626e-206">Synchroniczne operacje we/wy</span><span class="sxs-lookup"><span data-stu-id="6626e-206">Synchronous I/O</span></span>

<span data-ttu-id="6626e-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Określa, czy synchroniczna operacja we/wy jest dozwolona dla żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6626e-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6626e-208">Wartość domyślna to `false`.</span><span class="sxs-lookup"><span data-stu-id="6626e-208">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="6626e-209">Duża liczba blokowania synchronicznych operacji we/wy może prowadzić do zablokowania puli wątków, co sprawia, że aplikacja nie odpowiada.</span><span class="sxs-lookup"><span data-stu-id="6626e-209">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6626e-210">Włączaj tylko `AllowSynchronousIO` w przypadku korzystania z biblioteki, która nie obsługuje asynchronicznych operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="6626e-210">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6626e-211">Poniższy przykład umożliwia synchroniczne we/wy:</span><span class="sxs-lookup"><span data-stu-id="6626e-211">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6626e-212">Aby uzyskać informacje o innych opcjach i ograniczeniach Kestrel, zobacz:</span><span class="sxs-lookup"><span data-stu-id="6626e-212">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
