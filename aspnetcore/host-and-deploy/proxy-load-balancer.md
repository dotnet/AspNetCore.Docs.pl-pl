---
title: Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia
author: rick-anderson
description: Dowiedz się więcej o konfigurowaniu aplikacji hostowanych za serwerami proxy i usługami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e329b8604b820818167a563b3a21f01f2ab2a6ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776386"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="e3502-103">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="e3502-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="e3502-104">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e3502-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e3502-105">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="e3502-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="e3502-106">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e3502-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="e3502-107">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="e3502-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="e3502-108">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="e3502-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="e3502-109">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="e3502-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="e3502-110">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="e3502-110">Forwarded headers</span></span>

<span data-ttu-id="e3502-111">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3502-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="e3502-112">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="e3502-112">Header</span></span> | <span data-ttu-id="e3502-113">Opis</span><span class="sxs-lookup"><span data-stu-id="e3502-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="e3502-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="e3502-114">X-Forwarded-For</span></span> | <span data-ttu-id="e3502-115">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="e3502-116">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="e3502-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="e3502-117">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="e3502-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="e3502-118">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="e3502-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="e3502-119">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="e3502-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="e3502-120">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="e3502-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="e3502-121">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="e3502-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="e3502-122">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e3502-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="e3502-123">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="e3502-124">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="e3502-124">X-Forwarded-Host</span></span> | <span data-ttu-id="e3502-125">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="e3502-125">The original value of the Host header field.</span></span> <span data-ttu-id="e3502-126">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="e3502-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="e3502-127">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="e3502-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="e3502-128">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="e3502-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="e3502-129">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e3502-129">The middleware updates:</span></span>

* <span data-ttu-id="e3502-130">Element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; ustawiany przy `X-Forwarded-For` użyciu wartości header.</span><span class="sxs-lookup"><span data-stu-id="e3502-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="e3502-131">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania `RemoteIpAddress`pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e3502-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="e3502-132">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e3502-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="e3502-133">Element [HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; został ustawiony `X-Forwarded-Proto` przy użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e3502-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="e3502-134">Element [HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; ustawiony przy `X-Forwarded-Host` użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e3502-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="e3502-135">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="e3502-136">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="e3502-136">The default settings are:</span></span>

* <span data-ttu-id="e3502-137">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="e3502-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="e3502-138">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="e3502-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="e3502-139">Przekazane nagłówki mają nazwę `X-Forwarded-For` i. `X-Forwarded-Proto`</span><span class="sxs-lookup"><span data-stu-id="e3502-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="e3502-140">Nie wszystkie urządzenia sieciowe Dodaj nagłówki `X-Forwarded-For` i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="e3502-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="e3502-141">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="e3502-142">Jeśli urządzenie używa innych nazw nagłówka `X-Forwarded-For` niż i `X-Forwarded-Proto`, ustaw opcje <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> i <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="e3502-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="e3502-143">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="e3502-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="e3502-144">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3502-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="e3502-145">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3502-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="e3502-146">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="e3502-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="e3502-147">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków `X-Forwarded-Proto` i i jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="e3502-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="e3502-148">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e3502-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e3502-149">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="e3502-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e3502-150">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="e3502-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="e3502-151">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>za pomocą.</span><span class="sxs-lookup"><span data-stu-id="e3502-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="e3502-152">Po włączeniu oprogramowania pośredniczącego, jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e3502-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="e3502-153">Skonfiguruj oprogramowanie pośredniczące w <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> programie `X-Forwarded-For` , aby przekazywać nagłówki i `X-Forwarded-Proto` w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e3502-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e3502-154">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e3502-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="e3502-155">Jeśli wartość <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie jest określona `Startup.ConfigureServices` w lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e3502-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="e3502-156"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="e3502-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="e3502-157">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="e3502-157">Nginx configuration</span></span>

<span data-ttu-id="e3502-158">Aby przesłać dalej `X-Forwarded-For` nagłówki `X-Forwarded-Proto` i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="e3502-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="e3502-159">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="e3502-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="e3502-160">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="e3502-160">Apache configuration</span></span>

<span data-ttu-id="e3502-161">`X-Forwarded-For`jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="e3502-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="e3502-162">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz. <xref:host-and-deploy/linux-apache#configure-apache></span><span class="sxs-lookup"><span data-stu-id="e3502-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="e3502-163">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="e3502-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="e3502-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="e3502-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="e3502-165">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="e3502-165">The following example changes the default values:</span></span>

* <span data-ttu-id="e3502-166">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2`.</span><span class="sxs-lookup"><span data-stu-id="e3502-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="e3502-167">Dodaj znany adres serwera proxy `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="e3502-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="e3502-168">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na. `X-Forwarded-For-My-Custom-Header-Name`</span><span class="sxs-lookup"><span data-stu-id="e3502-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="e3502-169">Opcja</span><span class="sxs-lookup"><span data-stu-id="e3502-169">Option</span></span> | <span data-ttu-id="e3502-170">Opis</span><span class="sxs-lookup"><span data-stu-id="e3502-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="e3502-171">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="e3502-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="e3502-172">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="e3502-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="e3502-173">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="e3502-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="e3502-174">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="e3502-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="e3502-175">Symbol wieloznaczny `*` najwyższego poziomu zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="e3502-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="e3502-176">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="e3502-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="e3502-177">Na przykład pasuje `*.contoso.com` do domeny `foo.contoso.com` podrzędnej, ale nie do domeny `contoso.com`głównej.</span><span class="sxs-lookup"><span data-stu-id="e3502-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="e3502-178">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="e3502-179">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`przykład).</span><span class="sxs-lookup"><span data-stu-id="e3502-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="e3502-180">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="e3502-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="e3502-181">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="e3502-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="e3502-182">Wartość domyślna jest pusta `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="e3502-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="e3502-183">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="e3502-184">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie `X-Forwarded-For` używa nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e3502-185">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="e3502-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="e3502-186">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="e3502-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="e3502-187">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="e3502-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="e3502-188">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="e3502-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="e3502-189">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e3502-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="e3502-190">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="e3502-191">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie `X-Forwarded-Host` używa nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e3502-192">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="e3502-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="e3502-193">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="e3502-194">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie `X-Forwarded-Proto` używa nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e3502-195">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e3502-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="e3502-196">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="e3502-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="e3502-197">`null` Ustaw, aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="e3502-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="e3502-198">Ustawienie`null` wartości innej niż wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="e3502-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="e3502-199">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="e3502-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e3502-200">Jeśli zostanie użyta wartość`1`domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="e3502-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="e3502-201">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="e3502-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="e3502-202">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="e3502-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="e3502-203">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="e3502-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="e3502-204">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole `::ffff:10.0.0.1`IPv6 jako).</span><span class="sxs-lookup"><span data-stu-id="e3502-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e3502-205">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e3502-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e3502-206">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e3502-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e3502-207">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="e3502-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e3502-208">Wartość `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> domyślna to> zawierający pojedynczy wpis dla `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e3502-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="e3502-209">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="e3502-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="e3502-210">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="e3502-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="e3502-211">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole `::ffff:10.0.0.1`IPv6 jako).</span><span class="sxs-lookup"><span data-stu-id="e3502-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e3502-212">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e3502-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e3502-213">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e3502-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e3502-214">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="e3502-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e3502-215">Wartość `IList` \< <xref:System.Net.IPAddress> domyślna to> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e3502-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="e3502-216">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="e3502-217">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="e3502-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="e3502-218">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="e3502-219">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="e3502-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="e3502-220">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="e3502-221">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e3502-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="e3502-222">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="e3502-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="e3502-223">Wartość domyślna w ASP.NET Core 1. x to `true`.</span><span class="sxs-lookup"><span data-stu-id="e3502-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="e3502-224">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma `false`wartość.</span><span class="sxs-lookup"><span data-stu-id="e3502-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="e3502-225">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="e3502-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="e3502-226">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="e3502-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="e3502-227">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="e3502-228">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e3502-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="e3502-229">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="e3502-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="e3502-230">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="e3502-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="e3502-231">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="e3502-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="e3502-232">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="e3502-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="e3502-233">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1`, oprogramowanie `Request.PathBase` pośredniczące `Request.Path` ustawia `/api/1` do `/foo` i przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e3502-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="e3502-234">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="e3502-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="e3502-235">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="e3502-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="e3502-236">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1`), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="e3502-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="e3502-237">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> właściwości:</span><span class="sxs-lookup"><span data-stu-id="e3502-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="e3502-238">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="e3502-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="e3502-239">Jeśli serwer proxy nie używa nagłówków o `X-Forwarded-For` nazwach i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> , <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Ustaw opcje i tak, aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="e3502-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="e3502-240">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="e3502-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="e3502-241">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole `::ffff:10.0.0.1` IPv6 `::ffff:a00:1`jako lub).</span><span class="sxs-lookup"><span data-stu-id="e3502-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="e3502-242">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e3502-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e3502-243">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e3502-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="e3502-244">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="e3502-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="e3502-245">Adres IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="e3502-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="e3502-246">Przekonwertowany adres IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="e3502-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="e3502-247">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="e3502-247">Converted prefix length: 104</span></span>

<span data-ttu-id="e3502-248">Możesz również podać adres w formacie szesnastkowym (`10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="e3502-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="e3502-249">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR (`8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="e3502-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="e3502-250">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="e3502-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="e3502-251">Aplikacje, które <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> wywołują i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu Azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="e3502-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="e3502-252">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="e3502-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="e3502-253">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="e3502-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="e3502-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="e3502-255">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="e3502-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="e3502-256">W `Startup.ConfigureServices`programie Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="e3502-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="e3502-257">Przekazywanie certyfikatów</span><span class="sxs-lookup"><span data-stu-id="e3502-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="e3502-258">Azure</span><span class="sxs-lookup"><span data-stu-id="e3502-258">Azure</span></span>

<span data-ttu-id="e3502-259">Aby skonfigurować Azure App Service na potrzeby przekazywania certyfikatów, zobacz [Konfigurowanie uwierzytelniania wzajemnego TLS dla Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="e3502-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="e3502-260">Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3502-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="e3502-261">W `Startup.Configure`programie Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="e3502-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="e3502-262">Skonfiguruj oprogramowanie pośredniczące do przekazywania certyfikatów, aby określić nazwę nagłówka używaną przez platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="e3502-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="e3502-263">W `Startup.ConfigureServices`programie Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="e3502-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="e3502-264">Inne serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="e3502-264">Other web proxies</span></span>

<span data-ttu-id="e3502-265">Jeśli używany jest serwer proxy, który nie jest programem IIS lub Azure App Service routingu żądań aplikacji (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu otrzymanego w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3502-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="e3502-266">W `Startup.Configure`programie Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="e3502-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="e3502-267">Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej certyfikatów, aby określić nazwę nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e3502-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="e3502-268">W `Startup.ConfigureServices`programie Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="e3502-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="e3502-269">Jeśli serwer proxy nie może zakodować certyfikatu Base64 (podobnie jak w przypadku Nginx), ustaw `HeaderConverter` opcję.</span><span class="sxs-lookup"><span data-stu-id="e3502-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="e3502-270">Rozważmy następujący przykład w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e3502-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="e3502-271">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="e3502-271">Troubleshoot</span></span>

<span data-ttu-id="e3502-272">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e3502-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e3502-273">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="e3502-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="e3502-274">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="e3502-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="e3502-275">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="e3502-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="e3502-276">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e3502-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="e3502-277">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="e3502-278">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="e3502-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="e3502-279">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="e3502-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="e3502-280">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`usłudze.</span><span class="sxs-lookup"><span data-stu-id="e3502-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="e3502-281">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="e3502-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="e3502-282">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="e3502-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e3502-283">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="e3502-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="e3502-284">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` listach `KnownNetworks` lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="e3502-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="e3502-285">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="e3502-286">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="e3502-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="e3502-287">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="e3502-288">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks`) w programie. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="e3502-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e3502-289">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="e3502-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="e3502-290">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="e3502-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="e3502-291">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="e3502-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3502-292">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e3502-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="e3502-293">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="e3502-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e3502-294">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="e3502-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="e3502-295">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e3502-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="e3502-296">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="e3502-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="e3502-297">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="e3502-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="e3502-298">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="e3502-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="e3502-299">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="e3502-299">Forwarded headers</span></span>

<span data-ttu-id="e3502-300">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3502-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="e3502-301">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="e3502-301">Header</span></span> | <span data-ttu-id="e3502-302">Opis</span><span class="sxs-lookup"><span data-stu-id="e3502-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="e3502-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="e3502-303">X-Forwarded-For</span></span> | <span data-ttu-id="e3502-304">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="e3502-305">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="e3502-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="e3502-306">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="e3502-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="e3502-307">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="e3502-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="e3502-308">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="e3502-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="e3502-309">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="e3502-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="e3502-310">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="e3502-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="e3502-311">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e3502-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="e3502-312">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="e3502-313">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="e3502-313">X-Forwarded-Host</span></span> | <span data-ttu-id="e3502-314">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="e3502-314">The original value of the Host header field.</span></span> <span data-ttu-id="e3502-315">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="e3502-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="e3502-316">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="e3502-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="e3502-317">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="e3502-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="e3502-318">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e3502-318">The middleware updates:</span></span>

* <span data-ttu-id="e3502-319">Element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; ustawiany przy `X-Forwarded-For` użyciu wartości header.</span><span class="sxs-lookup"><span data-stu-id="e3502-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="e3502-320">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania `RemoteIpAddress`pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e3502-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="e3502-321">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e3502-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="e3502-322">Element [HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; został ustawiony `X-Forwarded-Proto` przy użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e3502-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="e3502-323">Element [HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; ustawiony przy `X-Forwarded-Host` użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e3502-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="e3502-324">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="e3502-325">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="e3502-325">The default settings are:</span></span>

* <span data-ttu-id="e3502-326">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="e3502-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="e3502-327">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="e3502-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="e3502-328">Przekazane nagłówki mają nazwę `X-Forwarded-For` i. `X-Forwarded-Proto`</span><span class="sxs-lookup"><span data-stu-id="e3502-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="e3502-329">Nie wszystkie urządzenia sieciowe Dodaj nagłówki `X-Forwarded-For` i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="e3502-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="e3502-330">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="e3502-331">Jeśli urządzenie używa innych nazw nagłówka `X-Forwarded-For` niż i `X-Forwarded-Proto`, ustaw opcje <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> i <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="e3502-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="e3502-332">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="e3502-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="e3502-333">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3502-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="e3502-334">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3502-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="e3502-335">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="e3502-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="e3502-336">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków `X-Forwarded-Proto` i i jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="e3502-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="e3502-337">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e3502-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e3502-338">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="e3502-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e3502-339">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="e3502-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="e3502-340">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>za pomocą.</span><span class="sxs-lookup"><span data-stu-id="e3502-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="e3502-341">Po włączeniu oprogramowania pośredniczącego, jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e3502-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="e3502-342">Skonfiguruj oprogramowanie pośredniczące w <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> programie `X-Forwarded-For` , aby przekazywać nagłówki i `X-Forwarded-Proto` w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e3502-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e3502-343">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e3502-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="e3502-344">Jeśli wartość <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie jest określona `Startup.ConfigureServices` w lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e3502-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="e3502-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="e3502-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="e3502-346">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="e3502-346">Nginx configuration</span></span>

<span data-ttu-id="e3502-347">Aby przesłać dalej `X-Forwarded-For` nagłówki `X-Forwarded-Proto` i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="e3502-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="e3502-348">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="e3502-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="e3502-349">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="e3502-349">Apache configuration</span></span>

<span data-ttu-id="e3502-350">`X-Forwarded-For`jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="e3502-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="e3502-351">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz. <xref:host-and-deploy/linux-apache#configure-apache></span><span class="sxs-lookup"><span data-stu-id="e3502-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="e3502-352">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="e3502-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="e3502-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="e3502-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="e3502-354">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="e3502-354">The following example changes the default values:</span></span>

* <span data-ttu-id="e3502-355">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2`.</span><span class="sxs-lookup"><span data-stu-id="e3502-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="e3502-356">Dodaj znany adres serwera proxy `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="e3502-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="e3502-357">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na. `X-Forwarded-For-My-Custom-Header-Name`</span><span class="sxs-lookup"><span data-stu-id="e3502-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="e3502-358">Opcja</span><span class="sxs-lookup"><span data-stu-id="e3502-358">Option</span></span> | <span data-ttu-id="e3502-359">Opis</span><span class="sxs-lookup"><span data-stu-id="e3502-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="e3502-360">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="e3502-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="e3502-361">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="e3502-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="e3502-362">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="e3502-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="e3502-363">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="e3502-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="e3502-364">Symbol wieloznaczny `*` najwyższego poziomu zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="e3502-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="e3502-365">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="e3502-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="e3502-366">Na przykład pasuje `*.contoso.com` do domeny `foo.contoso.com` podrzędnej, ale nie do domeny `contoso.com`głównej.</span><span class="sxs-lookup"><span data-stu-id="e3502-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="e3502-367">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="e3502-368">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`przykład).</span><span class="sxs-lookup"><span data-stu-id="e3502-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="e3502-369">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="e3502-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="e3502-370">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="e3502-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="e3502-371">Wartość domyślna jest pusta `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="e3502-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="e3502-372">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="e3502-373">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie `X-Forwarded-For` używa nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e3502-374">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="e3502-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="e3502-375">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="e3502-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="e3502-376">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="e3502-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="e3502-377">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="e3502-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="e3502-378">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e3502-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="e3502-379">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="e3502-380">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie `X-Forwarded-Host` używa nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e3502-381">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="e3502-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="e3502-382">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="e3502-383">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie `X-Forwarded-Proto` używa nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e3502-384">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e3502-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="e3502-385">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="e3502-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="e3502-386">`null` Ustaw, aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="e3502-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="e3502-387">Ustawienie`null` wartości innej niż wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="e3502-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="e3502-388">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="e3502-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e3502-389">Jeśli zostanie użyta wartość`1`domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="e3502-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="e3502-390">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="e3502-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="e3502-391">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="e3502-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="e3502-392">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="e3502-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="e3502-393">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole `::ffff:10.0.0.1`IPv6 jako).</span><span class="sxs-lookup"><span data-stu-id="e3502-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e3502-394">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e3502-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e3502-395">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e3502-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e3502-396">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="e3502-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e3502-397">Wartość `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> domyślna to> zawierający pojedynczy wpis dla `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e3502-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="e3502-398">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="e3502-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="e3502-399">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="e3502-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="e3502-400">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole `::ffff:10.0.0.1`IPv6 jako).</span><span class="sxs-lookup"><span data-stu-id="e3502-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e3502-401">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e3502-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e3502-402">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e3502-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e3502-403">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="e3502-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e3502-404">Wartość `IList` \< <xref:System.Net.IPAddress> domyślna to> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e3502-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="e3502-405">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="e3502-406">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="e3502-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="e3502-407">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="e3502-408">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="e3502-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="e3502-409">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e3502-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="e3502-410">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e3502-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="e3502-411">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="e3502-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="e3502-412">Wartość domyślna w ASP.NET Core 1. x to `true`.</span><span class="sxs-lookup"><span data-stu-id="e3502-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="e3502-413">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma `false`wartość.</span><span class="sxs-lookup"><span data-stu-id="e3502-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="e3502-414">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="e3502-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="e3502-415">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="e3502-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="e3502-416">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3502-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="e3502-417">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e3502-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="e3502-418">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="e3502-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="e3502-419">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="e3502-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="e3502-420">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="e3502-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="e3502-421">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="e3502-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="e3502-422">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1`, oprogramowanie `Request.PathBase` pośredniczące `Request.Path` ustawia `/api/1` do `/foo` i przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e3502-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="e3502-423">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="e3502-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="e3502-424">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="e3502-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="e3502-425">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1`), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="e3502-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="e3502-426">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> właściwości:</span><span class="sxs-lookup"><span data-stu-id="e3502-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="e3502-427">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="e3502-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="e3502-428">Jeśli serwer proxy nie używa nagłówków o `X-Forwarded-For` nazwach i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> , <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Ustaw opcje i tak, aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="e3502-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="e3502-429">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="e3502-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="e3502-430">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole `::ffff:10.0.0.1` IPv6 `::ffff:a00:1`jako lub).</span><span class="sxs-lookup"><span data-stu-id="e3502-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="e3502-431">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e3502-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e3502-432">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e3502-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="e3502-433">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="e3502-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="e3502-434">Adres IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="e3502-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="e3502-435">Przekonwertowany adres IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="e3502-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="e3502-436">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="e3502-436">Converted prefix length: 104</span></span>

<span data-ttu-id="e3502-437">Możesz również podać adres w formacie szesnastkowym (`10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="e3502-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="e3502-438">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR (`8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="e3502-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="e3502-439">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="e3502-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="e3502-440">Aplikacje, które <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> wywołują i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu Azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="e3502-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="e3502-441">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="e3502-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="e3502-442">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="e3502-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="e3502-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="e3502-444">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="e3502-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="e3502-445">W `Startup.ConfigureServices`programie Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="e3502-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="e3502-446">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="e3502-446">Troubleshoot</span></span>

<span data-ttu-id="e3502-447">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e3502-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e3502-448">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="e3502-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="e3502-449">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="e3502-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="e3502-450">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="e3502-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="e3502-451">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e3502-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="e3502-452">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="e3502-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="e3502-453">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="e3502-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="e3502-454">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="e3502-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="e3502-455">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`usłudze.</span><span class="sxs-lookup"><span data-stu-id="e3502-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="e3502-456">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="e3502-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="e3502-457">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="e3502-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e3502-458">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="e3502-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="e3502-459">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` listach `KnownNetworks` lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="e3502-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="e3502-460">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="e3502-461">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="e3502-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="e3502-462">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="e3502-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="e3502-463">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks`) w programie. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="e3502-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e3502-464">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="e3502-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="e3502-465">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="e3502-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="e3502-466">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="e3502-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3502-467">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e3502-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="e3502-468">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="e3502-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
