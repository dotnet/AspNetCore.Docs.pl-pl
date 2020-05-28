---
<span data-ttu-id="c07f4-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-102">'Blazor'</span></span>
- <span data-ttu-id="c07f4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-103">'Identity'</span></span>
- <span data-ttu-id="c07f4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-105">'Razor'</span></span>
- <span data-ttu-id="c07f4-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="c07f4-107">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="c07f4-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="c07f4-108">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="c07f4-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c07f4-109">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="c07f4-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="c07f4-110">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c07f4-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="c07f4-111">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="c07f4-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="c07f4-112">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="c07f4-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="c07f4-113">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="c07f4-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="c07f4-114">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="c07f4-114">Forwarded headers</span></span>

<span data-ttu-id="c07f4-115">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="c07f4-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="c07f4-116">Header</span><span class="sxs-lookup"><span data-stu-id="c07f4-116">Header</span></span> | <span data-ttu-id="c07f4-117">Opis</span><span class="sxs-lookup"><span data-stu-id="c07f4-117">Description</span></span> |
| ---
<span data-ttu-id="c07f4-118">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-119">'Blazor'</span></span>
- <span data-ttu-id="c07f4-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-120">'Identity'</span></span>
- <span data-ttu-id="c07f4-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-122">'Razor'</span></span>
- <span data-ttu-id="c07f4-123">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-123">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-124">--- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-125">'Blazor'</span></span>
- <span data-ttu-id="c07f4-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-126">'Identity'</span></span>
- <span data-ttu-id="c07f4-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-128">'Razor'</span></span>
- <span data-ttu-id="c07f4-129">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-130">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-131">'Blazor'</span></span>
- <span data-ttu-id="c07f4-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-132">'Identity'</span></span>
- <span data-ttu-id="c07f4-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-134">'Razor'</span></span>
- <span data-ttu-id="c07f4-135">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-136">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-137">'Blazor'</span></span>
- <span data-ttu-id="c07f4-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-138">'Identity'</span></span>
- <span data-ttu-id="c07f4-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-140">'Razor'</span></span>
- <span data-ttu-id="c07f4-141">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-141">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-142">------ | | X-forwardd-dla | Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="c07f4-143">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="c07f4-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="c07f4-144">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="c07f4-145">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="c07f4-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="c07f4-146">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="c07f4-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="c07f4-147">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="c07f4-148">| | X-Forwarded-proto | Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c07f4-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="c07f4-149">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="c07f4-150">| | X-Forward-Host | Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="c07f4-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="c07f4-151">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="c07f4-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="c07f4-152">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="c07f4-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="c07f4-153">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="c07f4-154">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="c07f4-154">The middleware updates:</span></span>

* <span data-ttu-id="c07f4-155">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="c07f4-156">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="c07f4-157">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="c07f4-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="c07f4-158">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="c07f4-159">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="c07f4-160">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="c07f4-161">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="c07f4-161">The default settings are:</span></span>

* <span data-ttu-id="c07f4-162">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="c07f4-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="c07f4-163">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="c07f4-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="c07f4-164">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="c07f4-165">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="c07f4-166">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="c07f4-167">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="c07f4-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="c07f4-168">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="c07f4-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="c07f4-169">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c07f4-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="c07f4-170">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c07f4-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="c07f4-171">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="c07f4-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="c07f4-172">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c07f4-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="c07f4-173">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="c07f4-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c07f4-174">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="c07f4-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c07f4-175">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="c07f4-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="c07f4-176">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="c07f4-177">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="c07f4-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="c07f4-178">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c07f4-179">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="c07f4-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="c07f4-180">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="c07f4-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="c07f4-181"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="c07f4-182">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="c07f4-182">Nginx configuration</span></span>

<span data-ttu-id="c07f4-183">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="c07f4-184">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="c07f4-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="c07f4-185">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="c07f4-185">Apache configuration</span></span>

<span data-ttu-id="c07f4-186">`X-Forwarded-For`jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="c07f4-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="c07f4-187">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="c07f4-188">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="c07f4-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="c07f4-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="c07f4-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="c07f4-190">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="c07f4-190">The following example changes the default values:</span></span>

* <span data-ttu-id="c07f4-191">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="c07f4-192">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="c07f4-193">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="c07f4-194">Opcja</span><span class="sxs-lookup"><span data-stu-id="c07f4-194">Option</span></span> | <span data-ttu-id="c07f4-195">Opis</span><span class="sxs-lookup"><span data-stu-id="c07f4-195">Description</span></span> |
| ---
<span data-ttu-id="c07f4-196">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-197">'Blazor'</span></span>
- <span data-ttu-id="c07f4-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-198">'Identity'</span></span>
- <span data-ttu-id="c07f4-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-200">'Razor'</span></span>
- <span data-ttu-id="c07f4-201">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-201">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-202">--- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-203">'Blazor'</span></span>
- <span data-ttu-id="c07f4-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-204">'Identity'</span></span>
- <span data-ttu-id="c07f4-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-206">'Razor'</span></span>
- <span data-ttu-id="c07f4-207">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-208">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-209">'Blazor'</span></span>
- <span data-ttu-id="c07f4-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-210">'Identity'</span></span>
- <span data-ttu-id="c07f4-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-212">'Razor'</span></span>
- <span data-ttu-id="c07f4-213">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-214">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-215">'Blazor'</span></span>
- <span data-ttu-id="c07f4-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-216">'Identity'</span></span>
- <span data-ttu-id="c07f4-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-218">'Razor'</span></span>
- <span data-ttu-id="c07f4-219">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-219">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="c07f4-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="c07f4-221">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="c07f4-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="c07f4-222">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="c07f4-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="c07f4-223">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="c07f4-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="c07f4-224">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="c07f4-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="c07f4-225">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="c07f4-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="c07f4-226">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="c07f4-227">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="c07f4-228">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="c07f4-229">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="c07f4-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="c07f4-230">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="c07f4-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="c07f4-231">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="c07f4-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="c07f4-233">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c07f4-234">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="c07f4-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="c07f4-236">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="c07f4-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="c07f4-237">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="c07f4-238">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="c07f4-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="c07f4-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="c07f4-240">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c07f4-241">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="c07f4-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="c07f4-243">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c07f4-244">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="c07f4-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="c07f4-246">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="c07f4-247">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="c07f4-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="c07f4-248">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="c07f4-249">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="c07f4-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="c07f4-250">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-250">The default is `1`.</span></span> <span data-ttu-id="c07f4-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="c07f4-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="c07f4-252">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="c07f4-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="c07f4-253">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="c07f4-254">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c07f4-255">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="c07f4-256">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="c07f4-257">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="c07f4-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="c07f4-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="c07f4-259">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="c07f4-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="c07f4-260">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="c07f4-261">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c07f4-262">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="c07f4-263">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="c07f4-264">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="c07f4-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="c07f4-266">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="c07f4-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="c07f4-268">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="c07f4-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="c07f4-270">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="c07f4-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="c07f4-272">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="c07f4-273">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="c07f4-274">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="c07f4-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="c07f4-275">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="c07f4-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="c07f4-276">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="c07f4-277">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="c07f4-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="c07f4-278">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="c07f4-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="c07f4-279">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="c07f4-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="c07f4-280">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="c07f4-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="c07f4-281">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="c07f4-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="c07f4-282">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="c07f4-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="c07f4-283">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="c07f4-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="c07f4-284">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="c07f4-285">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="c07f4-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="c07f4-286">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="c07f4-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="c07f4-287">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="c07f4-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="c07f4-288">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="c07f4-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="c07f4-289">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="c07f4-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="c07f4-290">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="c07f4-291">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c07f4-292">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="c07f4-293">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="c07f4-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="c07f4-294">Adres IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="c07f4-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="c07f4-295">Przekonwertowany adres IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="c07f4-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="c07f4-296">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="c07f4-296">Converted prefix length: 104</span></span>

<span data-ttu-id="c07f4-297">Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="c07f4-298">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="c07f4-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="c07f4-299">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="c07f4-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="c07f4-300">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="c07f4-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="c07f4-301">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="c07f4-302">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="c07f4-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="c07f4-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="c07f4-304">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="c07f4-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="c07f4-305">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="c07f4-305">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="c07f4-306">Przekazywanie certyfikatów</span><span class="sxs-lookup"><span data-stu-id="c07f4-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="c07f4-307">Azure</span><span class="sxs-lookup"><span data-stu-id="c07f4-307">Azure</span></span>

<span data-ttu-id="c07f4-308">Aby skonfigurować Azure App Service na potrzeby przekazywania certyfikatów, zobacz [Konfigurowanie uwierzytelniania wzajemnego TLS dla Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="c07f4-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="c07f4-309">Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c07f4-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="c07f4-310">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="c07f4-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="c07f4-311">Skonfiguruj oprogramowanie pośredniczące do przekazywania certyfikatów, aby określić nazwę nagłówka używaną przez platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="c07f4-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="c07f4-312">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="c07f4-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="c07f4-313">Inne serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="c07f4-313">Other web proxies</span></span>

<span data-ttu-id="c07f4-314">Jeśli używany jest serwer proxy, który nie jest programem IIS lub Azure App Service routingu żądań aplikacji (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu otrzymanego w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="c07f4-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="c07f4-315">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="c07f4-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="c07f4-316">Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej certyfikatów, aby określić nazwę nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="c07f4-317">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="c07f4-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="c07f4-318">Jeśli serwer proxy nie może zakodować certyfikatu Base64 (podobnie jak w przypadku Nginx), ustaw `HeaderConverter` opcję.</span><span class="sxs-lookup"><span data-stu-id="c07f4-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="c07f4-319">Rozważmy następujący przykład w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c07f4-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="c07f4-320">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="c07f4-320">Troubleshoot</span></span>

<span data-ttu-id="c07f4-321">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="c07f4-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c07f4-322">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="c07f4-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="c07f4-323">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="c07f4-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="c07f4-324">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c07f4-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="c07f4-325">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c07f4-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="c07f4-326">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="c07f4-327">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="c07f4-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="c07f4-328">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="c07f4-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="c07f4-329">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="c07f4-330">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="c07f4-331">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="c07f4-332">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="c07f4-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="c07f4-333">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="c07f4-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="c07f4-334">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="c07f4-335">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="c07f4-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="c07f4-336">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="c07f4-337">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c07f4-338">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="c07f4-339">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="c07f4-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="c07f4-340">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="c07f4-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c07f4-341">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c07f4-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="c07f4-342">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="c07f4-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c07f4-343">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="c07f4-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="c07f4-344">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c07f4-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="c07f4-345">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="c07f4-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="c07f4-346">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="c07f4-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="c07f4-347">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="c07f4-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="c07f4-348">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="c07f4-348">Forwarded headers</span></span>

<span data-ttu-id="c07f4-349">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="c07f4-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="c07f4-350">Header</span><span class="sxs-lookup"><span data-stu-id="c07f4-350">Header</span></span> | <span data-ttu-id="c07f4-351">Opis</span><span class="sxs-lookup"><span data-stu-id="c07f4-351">Description</span></span> |
| ---
<span data-ttu-id="c07f4-352">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-353">'Blazor'</span></span>
- <span data-ttu-id="c07f4-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-354">'Identity'</span></span>
- <span data-ttu-id="c07f4-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-356">'Razor'</span></span>
- <span data-ttu-id="c07f4-357">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-357">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-358">--- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-359">'Blazor'</span></span>
- <span data-ttu-id="c07f4-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-360">'Identity'</span></span>
- <span data-ttu-id="c07f4-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-362">'Razor'</span></span>
- <span data-ttu-id="c07f4-363">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-364">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-365">'Blazor'</span></span>
- <span data-ttu-id="c07f4-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-366">'Identity'</span></span>
- <span data-ttu-id="c07f4-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-368">'Razor'</span></span>
- <span data-ttu-id="c07f4-369">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-370">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-371">'Blazor'</span></span>
- <span data-ttu-id="c07f4-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-372">'Identity'</span></span>
- <span data-ttu-id="c07f4-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-374">'Razor'</span></span>
- <span data-ttu-id="c07f4-375">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-375">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-376">------ | | X-forwardd-dla | Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="c07f4-377">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="c07f4-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="c07f4-378">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="c07f4-379">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="c07f4-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="c07f4-380">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="c07f4-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="c07f4-381">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="c07f4-382">| | X-Forwarded-proto | Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c07f4-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="c07f4-383">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="c07f4-384">| | X-Forward-Host | Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="c07f4-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="c07f4-385">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="c07f4-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="c07f4-386">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="c07f4-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="c07f4-387">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="c07f4-388">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="c07f4-388">The middleware updates:</span></span>

* <span data-ttu-id="c07f4-389">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="c07f4-390">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="c07f4-391">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="c07f4-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="c07f4-392">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="c07f4-393">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="c07f4-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="c07f4-394">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="c07f4-395">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="c07f4-395">The default settings are:</span></span>

* <span data-ttu-id="c07f4-396">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="c07f4-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="c07f4-397">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="c07f4-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="c07f4-398">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="c07f4-399">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="c07f4-400">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="c07f4-401">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="c07f4-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="c07f4-402">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="c07f4-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="c07f4-403">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c07f4-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="c07f4-404">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c07f4-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="c07f4-405">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="c07f4-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="c07f4-406">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c07f4-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="c07f4-407">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="c07f4-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c07f4-408">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="c07f4-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c07f4-409">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="c07f4-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="c07f4-410">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="c07f4-411">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="c07f4-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="c07f4-412">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c07f4-413">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="c07f4-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="c07f4-414">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="c07f4-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="c07f4-415"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="c07f4-416">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="c07f4-416">Nginx configuration</span></span>

<span data-ttu-id="c07f4-417">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="c07f4-418">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="c07f4-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="c07f4-419">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="c07f4-419">Apache configuration</span></span>

<span data-ttu-id="c07f4-420">`X-Forwarded-For`jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="c07f4-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="c07f4-421">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="c07f4-422">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="c07f4-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="c07f4-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="c07f4-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="c07f4-424">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="c07f4-424">The following example changes the default values:</span></span>

* <span data-ttu-id="c07f4-425">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="c07f4-426">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="c07f4-427">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="c07f4-428">Opcja</span><span class="sxs-lookup"><span data-stu-id="c07f4-428">Option</span></span> | <span data-ttu-id="c07f4-429">Opis</span><span class="sxs-lookup"><span data-stu-id="c07f4-429">Description</span></span> |
| ---
<span data-ttu-id="c07f4-430">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-431">'Blazor'</span></span>
- <span data-ttu-id="c07f4-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-432">'Identity'</span></span>
- <span data-ttu-id="c07f4-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-434">'Razor'</span></span>
- <span data-ttu-id="c07f4-435">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-435">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-436">--- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-437">'Blazor'</span></span>
- <span data-ttu-id="c07f4-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-438">'Identity'</span></span>
- <span data-ttu-id="c07f4-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-440">'Razor'</span></span>
- <span data-ttu-id="c07f4-441">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-442">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-443">'Blazor'</span></span>
- <span data-ttu-id="c07f4-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-444">'Identity'</span></span>
- <span data-ttu-id="c07f4-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-446">'Razor'</span></span>
- <span data-ttu-id="c07f4-447">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c07f4-448">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c07f4-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c07f4-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-449">'Blazor'</span></span>
- <span data-ttu-id="c07f4-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c07f4-450">'Identity'</span></span>
- <span data-ttu-id="c07f4-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c07f4-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="c07f4-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c07f4-452">'Razor'</span></span>
- <span data-ttu-id="c07f4-453">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c07f4-453">'SignalR' uid:</span></span> 

<span data-ttu-id="c07f4-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="c07f4-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="c07f4-455">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="c07f4-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="c07f4-456">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="c07f4-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="c07f4-457">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="c07f4-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="c07f4-458">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="c07f4-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="c07f4-459">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="c07f4-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="c07f4-460">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="c07f4-461">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="c07f4-462">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="c07f4-463">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="c07f4-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="c07f4-464">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="c07f4-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="c07f4-465">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="c07f4-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="c07f4-467">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c07f4-468">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="c07f4-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="c07f4-470">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="c07f4-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="c07f4-471">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="c07f4-472">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="c07f4-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="c07f4-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="c07f4-474">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c07f4-475">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="c07f4-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="c07f4-477">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c07f4-478">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="c07f4-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="c07f4-480">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="c07f4-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="c07f4-481">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="c07f4-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="c07f4-482">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="c07f4-483">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="c07f4-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="c07f4-484">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-484">The default is `1`.</span></span> <span data-ttu-id="c07f4-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="c07f4-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="c07f4-486">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="c07f4-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="c07f4-487">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="c07f4-488">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c07f4-489">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="c07f4-490">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="c07f4-491">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="c07f4-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="c07f4-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="c07f4-493">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="c07f4-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="c07f4-494">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="c07f4-495">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c07f4-496">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="c07f4-497">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="c07f4-498">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="c07f4-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="c07f4-500">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="c07f4-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="c07f4-502">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="c07f4-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="c07f4-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="c07f4-504">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="c07f4-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="c07f4-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="c07f4-506">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="c07f4-507">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="c07f4-508">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="c07f4-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="c07f4-509">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="c07f4-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="c07f4-510">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c07f4-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="c07f4-511">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="c07f4-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="c07f4-512">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="c07f4-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="c07f4-513">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="c07f4-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="c07f4-514">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="c07f4-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="c07f4-515">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="c07f4-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="c07f4-516">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="c07f4-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="c07f4-517">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="c07f4-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="c07f4-518">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="c07f4-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="c07f4-519">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="c07f4-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="c07f4-520">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="c07f4-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="c07f4-521">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="c07f4-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="c07f4-522">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="c07f4-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="c07f4-523">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="c07f4-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="c07f4-524">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="c07f4-525">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c07f4-526">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="c07f4-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="c07f4-527">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="c07f4-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="c07f4-528">Adres IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="c07f4-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="c07f4-529">Przekonwertowany adres IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="c07f4-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="c07f4-530">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="c07f4-530">Converted prefix length: 104</span></span>

<span data-ttu-id="c07f4-531">Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="c07f4-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="c07f4-532">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="c07f4-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="c07f4-533">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="c07f4-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="c07f4-534">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="c07f4-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="c07f4-535">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="c07f4-536">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="c07f4-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="c07f4-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="c07f4-538">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="c07f4-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="c07f4-539">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="c07f4-539">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="c07f4-540">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="c07f4-540">Troubleshoot</span></span>

<span data-ttu-id="c07f4-541">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="c07f4-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c07f4-542">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="c07f4-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="c07f4-543">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="c07f4-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="c07f4-544">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c07f4-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="c07f4-545">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c07f4-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="c07f4-546">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="c07f4-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="c07f4-547">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="c07f4-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="c07f4-548">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="c07f4-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="c07f4-549">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="c07f4-550">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="c07f4-551">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="c07f4-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="c07f4-552">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="c07f4-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="c07f4-553">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="c07f4-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="c07f4-554">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="c07f4-555">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="c07f4-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="c07f4-556">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="c07f4-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="c07f4-557">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c07f4-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c07f4-558">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="c07f4-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="c07f4-559">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="c07f4-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="c07f4-560">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="c07f4-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c07f4-561">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c07f4-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="c07f4-562">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="c07f4-562">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
