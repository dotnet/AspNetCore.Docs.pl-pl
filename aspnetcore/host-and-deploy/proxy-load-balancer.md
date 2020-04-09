---
title: Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia
author: rick-anderson
description: Dowiedz się więcej o konfiguracji aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniach.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659384"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="2d331-103">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="2d331-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="2d331-104">Przez [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="2d331-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2d331-105">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu modułu rdzenia IIS/ASP.NET, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="2d331-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="2d331-106">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu, zanim dotrze do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2d331-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="2d331-107">Gdy żądania HTTPS są przełączone przez protokół HTTP, oryginalny schemat (HTTPS) jest tracony i musi być przekazytywnie w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="2d331-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="2d331-108">Ponieważ aplikacja odbiera żądanie z serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, źródłowy adres IP klienta musi być również przekazywane w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="2d331-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="2d331-109">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="2d331-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="2d331-110">Nagłówki przekazywane dalej</span><span class="sxs-lookup"><span data-stu-id="2d331-110">Forwarded headers</span></span>

<span data-ttu-id="2d331-111">Zgodnie z konwencją serwery proxy prześliją informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d331-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="2d331-112">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="2d331-112">Header</span></span> | <span data-ttu-id="2d331-113">Opis</span><span class="sxs-lookup"><span data-stu-id="2d331-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2d331-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="2d331-114">X-Forwarded-For</span></span> | <span data-ttu-id="2d331-115">Przechowuje informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="2d331-116">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="2d331-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="2d331-117">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, w którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="2d331-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="2d331-118">Kolejne identyfikatory serwera proxy są następcami.</span><span class="sxs-lookup"><span data-stu-id="2d331-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="2d331-119">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="2d331-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="2d331-120">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportu.</span><span class="sxs-lookup"><span data-stu-id="2d331-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="2d331-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="2d331-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="2d331-122">Wartość schematu źródłowego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2d331-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="2d331-123">Wartość może być również lista schematów, jeśli żądanie przechodziło przez wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="2d331-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="2d331-124">X-Forwarded-Host</span></span> | <span data-ttu-id="2d331-125">Oryginalna wartość pola Nagłówek hosta.</span><span class="sxs-lookup"><span data-stu-id="2d331-125">The original value of the Host header field.</span></span> <span data-ttu-id="2d331-126">Zazwyczaj serwery proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="2d331-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="2d331-127">Informacje na temat luki w zabezpieczeniach uprawnień, która dotyczy systemów, w których serwer proxy nie sprawdza poprawności ani nie ogranicza nagłówków hostów do znanych dobrych wartości, zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295)</span><span class="sxs-lookup"><span data-stu-id="2d331-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="2d331-128">Oprogramowanie pośredniczące nagłówków przesyłanych dalej z pakietu [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) odczytuje te nagłówki <xref:Microsoft.AspNetCore.Http.HttpContext>i wypełnia skojarzone pola w programie .</span><span class="sxs-lookup"><span data-stu-id="2d331-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="2d331-129">Oprogramowanie pośredniczące jest aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="2d331-129">The middleware updates:</span></span>

* <span data-ttu-id="2d331-130">[Zestaw adresów HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; przy użyciu wartości nagłówka. `X-Forwarded-For`</span><span class="sxs-lookup"><span data-stu-id="2d331-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="2d331-131">Dodatkowe ustawienia wpływają na `RemoteIpAddress`sposób ustawiania oprogramowania pośredniczącego .</span><span class="sxs-lookup"><span data-stu-id="2d331-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="2d331-132">Aby uzyskać szczegółowe informacje, zobacz [opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2d331-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="2d331-133">[Zestaw httpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; `X-Forwarded-Proto` przy użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2d331-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="2d331-134">[Zestaw httpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; `X-Forwarded-Host` przy użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2d331-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="2d331-135">Można skonfigurować [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania pośredniczącego oprogramowania nagłówkowego przesyłane dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="2d331-136">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="2d331-136">The default settings are:</span></span>

* <span data-ttu-id="2d331-137">Istnieje tylko *jeden serwer proxy* między aplikacją a źródłem żądań.</span><span class="sxs-lookup"><span data-stu-id="2d331-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="2d331-138">Tylko adresy sprzężenia zwrotnego są skonfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="2d331-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="2d331-139">Przesłane dalej nagłówki `X-Forwarded-For` mają `X-Forwarded-Proto`nazwy i .</span><span class="sxs-lookup"><span data-stu-id="2d331-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="2d331-140">Nie wszystkie urządzenia sieciowe dodają `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` nagłówki bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2d331-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="2d331-141">Jeśli żądania pośredniczące nie zawierają tych nagłówków po dotarciu do aplikacji, należy zapoznać się ze wskazówkami producenta urządzenia.</span><span class="sxs-lookup"><span data-stu-id="2d331-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="2d331-142">Jeśli urządzenie używa innych nazw `X-Forwarded-For` `X-Forwarded-Proto`nagłówków <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> niż <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> i ustawia opcje i opcje, aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="2d331-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="2d331-143">Aby uzyskać więcej informacji, zobacz [Opcje oprogramowania pośredniczącego nagłówków przesyłania dalej](#forwarded-headers-middleware-options) i [Konfiguracja serwera proxy, który używa różnych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="2d331-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="2d331-144">Moduł podstawowy IIS/IIS Express i ASP.NET</span><span class="sxs-lookup"><span data-stu-id="2d331-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="2d331-145">Oprogramowanie pośredniczące przekazywania nagłówków jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usługi IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za iIS i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d331-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="2d331-146">Oprogramowanie pośredniczące nagłówków przesyłanych dalej jest aktywowane w celu uruchomienia jako pierwsze w potoku oprogramowania pośredniczącego z ograniczoną konfiguracją specyficzną dla ASP.NET Modułu Rdzenia z powodu obaw dotyczących zaufania z przesłałymi nagłówkami (na przykład [faofing IP).](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="2d331-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="2d331-147">Oprogramowanie pośredniczące jest skonfigurowane do przekazywania `X-Forwarded-For` nagłówków i `X-Forwarded-Proto` i jest ograniczone do jednego serwera proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="2d331-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="2d331-148">Jeśli wymagana jest dodatkowa [konfiguracja, zobacz opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2d331-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2d331-149">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="2d331-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2d331-150">Poza używaniem [integracji iis](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model)oprogramowanie pośredniczące nagłówków przesyłanych dalej nie jest domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="2d331-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="2d331-151">Oprogramowanie pośredniczące nagłówków przesyłanych dalej musi być włączone, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikacja przetwarzała przesłane nagłówki za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="2d331-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="2d331-152">Po włączeniu oprogramowania <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pośredniczącego, jeśli oprogramowanie pośredniczące nie jest określone, domyślne [forwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2d331-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="2d331-153">Skonfiguruj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące, aby przesłać `X-Forwarded-For` dalej nagłówki i `X-Forwarded-Proto` nagłówki w pliku `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2d331-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d331-154">Wywołać <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="2d331-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="2d331-155">Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie są `Startup.ConfigureServices` określone w lub bezpośrednio <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>do metody rozszerzenia z , domyślne nagłówki do przekazania są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2d331-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="2d331-156">Właściwość <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musi być skonfigurowana z nagłówkami do przekazania.</span><span class="sxs-lookup"><span data-stu-id="2d331-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="2d331-157">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="2d331-157">Nginx configuration</span></span>

<span data-ttu-id="2d331-158">Aby `X-Forwarded-For` przesłać `X-Forwarded-Proto` dalej nagłówki <xref:host-and-deploy/linux-nginx#configure-nginx>i nagłówki, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2d331-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="2d331-159">Aby uzyskać więcej informacji, zobacz [NGINX: Korzystanie z nagłówka Przesyłania dalej](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="2d331-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="2d331-160">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="2d331-160">Apache configuration</span></span>

<span data-ttu-id="2d331-161">`X-Forwarded-For`jest dodawany automatycznie (patrz [Mod_proxy modułu Apache: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="2d331-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="2d331-162">Aby uzyskać informacje na `X-Forwarded-Proto` temat <xref:host-and-deploy/linux-apache#configure-apache>przekazywania nagłówka, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2d331-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="2d331-163">Opcje oprogramowania pośredniczącego nagłówków z przekazywaniem dalej</span><span class="sxs-lookup"><span data-stu-id="2d331-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="2d331-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>kontrolować zachowanie oprogramowania pośredniczącego nagłówków przesyłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="2d331-165">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="2d331-165">The following example changes the default values:</span></span>

* <span data-ttu-id="2d331-166">Ogranicz liczbę wpisów w nagłówkach `2`przekazy.</span><span class="sxs-lookup"><span data-stu-id="2d331-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="2d331-167">Dodaj znany adres `127.0.10.1`serwera proxy .</span><span class="sxs-lookup"><span data-stu-id="2d331-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="2d331-168">Zmień nazwę nagłówka przesyłane `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name`dalej z domyślnej na .</span><span class="sxs-lookup"><span data-stu-id="2d331-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="2d331-169">Opcja</span><span class="sxs-lookup"><span data-stu-id="2d331-169">Option</span></span> | <span data-ttu-id="2d331-170">Opis</span><span class="sxs-lookup"><span data-stu-id="2d331-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="2d331-171">Ogranicza hosty `X-Forwarded-Host` przez nagłówek do podanych wartości.</span><span class="sxs-lookup"><span data-stu-id="2d331-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="2d331-172">Wartości są porównywane przy użyciu liczby porządkowej-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="2d331-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="2d331-173">Należy wykluczyć numery portów.</span><span class="sxs-lookup"><span data-stu-id="2d331-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="2d331-174">Jeśli lista jest pusta, wszystkie hosty są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="2d331-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="2d331-175">Symbol wieloznaczny `*` najwyższego poziomu umożliwia wszystkim niepustym hostom.</span><span class="sxs-lookup"><span data-stu-id="2d331-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="2d331-176">Symbole wieloznaczne poddomeny są dozwolone, ale nie pasują do domeny głównej.</span><span class="sxs-lookup"><span data-stu-id="2d331-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="2d331-177">Na przykład `*.contoso.com` pasuje do `foo.contoso.com` poddomeny, `contoso.com`ale nie do domeny głównej .</span><span class="sxs-lookup"><span data-stu-id="2d331-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="2d331-178">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [Punycode](https://tools.ietf.org/html/rfc3492) do dopasowania.</span><span class="sxs-lookup"><span data-stu-id="2d331-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="2d331-179">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`być w [formie konwencjonalnej](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład).</span><span class="sxs-lookup"><span data-stu-id="2d331-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="2d331-180">Adresy IPv6 nie są specjalne cased, aby sprawdzić, czy logiczna równość między różnymi formatami i nie jest wykonywana kanonizacja.</span><span class="sxs-lookup"><span data-stu-id="2d331-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="2d331-181">Niepodtrzymanie dozwolonych hostów może zezwolić osobie atakującej na fałszowanie łączy generowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2d331-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="2d331-182">Wartość domyślna to `IList<string>`pusta .</span><span class="sxs-lookup"><span data-stu-id="2d331-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="2d331-183">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="2d331-184">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-For` ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2d331-185">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2d331-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="2d331-186">Określa, które podmioty przekazu powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="2d331-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="2d331-187">Zobacz [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="2d331-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="2d331-188">Typowe wartości przypisane do `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tej właściwości to .</span><span class="sxs-lookup"><span data-stu-id="2d331-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="2d331-189">Wartością domyślną jest [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2d331-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="2d331-190">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="2d331-191">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Host` ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2d331-192">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2d331-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="2d331-193">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="2d331-194">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Proto` ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2d331-195">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2d331-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="2d331-196">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="2d331-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="2d331-197">Ustaw, `null` aby wyłączyć limit, ale należy `KnownProxies` to `KnownNetworks` zrobić tylko wtedy, gdy lub są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="2d331-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="2d331-198">Ustawienie wartości`null` niewartościowej jest środkiem ostrożności (ale nie gwarancją) w celu ochrony przed nieprawidłowo skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="2d331-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="2d331-199">Przekazywane nagłówki Oprogramowanie pośredniczące przetwarza nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="2d331-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2d331-200">Jeśli używana jest`1`wartość domyślna ( ) przetwarzana jest tylko wartość po `ForwardLimit` prawej stronie z nagłówków, chyba że wartość jest zwiększana.</span><span class="sxs-lookup"><span data-stu-id="2d331-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="2d331-201">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="2d331-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="2d331-202">Zakresy adresów znanych sieci do akceptowania przesłanych dalej nagłówków.</span><span class="sxs-lookup"><span data-stu-id="2d331-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="2d331-203">Podaj zakresy adresów IP przy użyciu notacji CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="2d331-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="2d331-204">Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="2d331-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2d331-205">Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2d331-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2d331-206">Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2d331-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2d331-207">Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="2d331-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2d331-208">Wartość domyślna `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> to> zawierający pojedynczy `IPAddress.Loopback`wpis dla .</span><span class="sxs-lookup"><span data-stu-id="2d331-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="2d331-209">Adresy znanych serwerów proxy do akceptowania przesłanych dalej nagłówków.</span><span class="sxs-lookup"><span data-stu-id="2d331-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="2d331-210">Służy `KnownProxies` do określania dokładnych dopasowań adresów IP.</span><span class="sxs-lookup"><span data-stu-id="2d331-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="2d331-211">Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="2d331-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2d331-212">Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2d331-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2d331-213">Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2d331-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2d331-214">Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="2d331-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2d331-215">Wartość domyślna `IList` \< <xref:System.Net.IPAddress> to> zawierający pojedynczy `IPAddress.IPv6Loopback`wpis dla .</span><span class="sxs-lookup"><span data-stu-id="2d331-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="2d331-216">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="2d331-217">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="2d331-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="2d331-218">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="2d331-219">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="2d331-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="2d331-220">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="2d331-221">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2d331-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="2d331-222">Wymagaj, aby liczba wartości nagłówka była synchronizowana między [przetwarzanego pliku ForwardedHeadersOptions.ForwardedHeaders.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="2d331-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="2d331-223">Wartość domyślna w ASP.NET Core 1.x to `true`.</span><span class="sxs-lookup"><span data-stu-id="2d331-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="2d331-224">Wartość domyślna w ASP.NET Core 2.0 `false`lub nowszym to .</span><span class="sxs-lookup"><span data-stu-id="2d331-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="2d331-225">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="2d331-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="2d331-226">Gdy nie można dodać nagłówków przesyłanych dalej, a wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="2d331-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="2d331-227">W niektórych przypadkach może nie być możliwe dodanie przesłanych dalej nagłówków do żądań przesłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="2d331-228">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są `Startup.Configure` HTTPS, schemat można ustawić ręcznie przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="2d331-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="2d331-229">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku deweloperskim lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="2d331-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="2d331-230">Radzenie sobie z bazą ścieżki i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="2d331-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="2d331-231">Niektóre serwery proxy przechodzą ścieżkę w stanie nienaruszonym, ale ze ścieżką podstawową aplikacji, która powinna zostać usunięta, aby routing działał poprawnie.</span><span class="sxs-lookup"><span data-stu-id="2d331-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="2d331-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) oprogramowanie pośredniczące dzieli ścieżkę na [httpRequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę podstawową aplikacji do [httpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="2d331-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="2d331-233">Jeśli `/foo` jest to ścieżka podstawowa aplikacji `/foo/api/1`dla ścieżki `Request.PathBase` proxy `/foo` `Request.Path` przekazywane `/api/1` jako , oprogramowanie pośredniczące ustawia się do i do następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="2d331-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="2d331-234">Oryginalna ścieżka i podstawa ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące jest wywoływane ponownie w odwrotnej kolejności.</span><span class="sxs-lookup"><span data-stu-id="2d331-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="2d331-235">Aby uzyskać więcej informacji na <xref:fundamentals/middleware/index>temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2d331-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="2d331-236">Jeśli serwer proxy przycina ścieżkę (na przykład przekierowanie `/foo/api/1` do `/api/1`), napraw przekierowania i łącza, ustawiając właściwość [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="2d331-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="2d331-237">Jeśli serwer proxy dodaje dane ścieżki, odrzuć część ścieżki, aby naprawić przekierowania i łącza za pomocą <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywanie do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> właściwości:</span><span class="sxs-lookup"><span data-stu-id="2d331-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="2d331-238">Konfiguracja serwera proxy, który używa różnych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="2d331-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="2d331-239">Jeśli serwer proxy nie używa `X-Forwarded-For` nagłówków o nazwie i `X-Forwarded-Proto` do przekazywania dalej <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> informacji o adresie/porcie i początkowym schemacie serwera proxy, ustaw i opcje zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="2d331-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="2d331-240">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="2d331-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="2d331-241">Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1` `::ffff:a00:1`IPv4 reprezentowanym w IPv6 jako lub ).</span><span class="sxs-lookup"><span data-stu-id="2d331-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="2d331-242">Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2d331-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2d331-243">Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2d331-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="2d331-244">W poniższym przykładzie adres sieciowy, który dostarcza przekazane `KnownNetworks` nagłówki, jest dodawany do listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="2d331-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="2d331-245">Adres IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="2d331-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="2d331-246">Przekonwertowany adres IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="2d331-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="2d331-247">Przekonwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="2d331-247">Converted prefix length: 104</span></span>

<span data-ttu-id="2d331-248">Można również podać adres w formacie szesnastkowym (reprezentowanym`10.11.12.1` w IPv6 jako `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="2d331-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="2d331-249">Podczas konwertowania adresu IPv4 na protokół IPv6 dodaj 96 do prefiksu CIDR Length (w`8` przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="2d331-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="2d331-250">Przesyłanie dalej schematu dla odwrotnych serwerów proxy systemu Linux i innych niż IIS</span><span class="sxs-lookup"><span data-stu-id="2d331-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="2d331-251">Aplikacje, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> które wywołują i umieszczają witrynę w nieskończonej pętli, jeśli są wdrażane w usłudze Azure Linux App Service, maszynie wirtualnej systemu Azure Linux (VM) lub za dowolnym innym serwerem proxy wstecznym oprócz usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2d331-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="2d331-252">TLS jest zakończony przez odwrotny serwer proxy, a Kestrel nie jest świadomy prawidłowego schematu żądań.</span><span class="sxs-lookup"><span data-stu-id="2d331-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="2d331-253">OAuth i OIDC również nie w tej konfiguracji, ponieważ generują niepoprawne przekierowania.</span><span class="sxs-lookup"><span data-stu-id="2d331-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="2d331-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej podczas uruchamiania za usługami IIS, ale nie ma pasującej automatycznej konfiguracji dla systemu Linux (integracja Apache lub Nginx).</span><span class="sxs-lookup"><span data-stu-id="2d331-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="2d331-255">Aby przesłać schemat dalej z serwera proxy w scenariuszach innych niż IIS, dodaj i skonfiguruj oprogramowanie pośredniczące nagłówków przesłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="2d331-256">W `Startup.ConfigureServices`, użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2d331-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="2d331-257">Przekazywanie certyfikatów</span><span class="sxs-lookup"><span data-stu-id="2d331-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="2d331-258">Azure</span><span class="sxs-lookup"><span data-stu-id="2d331-258">Azure</span></span>

<span data-ttu-id="2d331-259">Aby skonfigurować usługę Azure App Service do przekazywania certyfikatów, zobacz [Konfigurowanie wzajemnego uwierzytelniania TLS dla usługi Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="2d331-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="2d331-260">Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d331-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="2d331-261">W `Startup.Configure`, dodaj następujący kod `app.UseAuthentication();`przed wywołaniem do:</span><span class="sxs-lookup"><span data-stu-id="2d331-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="2d331-262">Skonfiguruj oprogramowanie pośredniczące przekazywania certyfikatów, aby określić nazwę nagłówka używana przez platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="2d331-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="2d331-263">W `Startup.ConfigureServices`polu Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące tworzy certyfikat:</span><span class="sxs-lookup"><span data-stu-id="2d331-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="2d331-264">Inne serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="2d331-264">Other web proxies</span></span>

<span data-ttu-id="2d331-265">Jeśli używany jest serwer proxy, który nie jest usługami IIS lub routingiem żądań aplikacji usługi Azure App Service (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu, który otrzymał w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d331-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="2d331-266">W `Startup.Configure`, dodaj następujący kod `app.UseAuthentication();`przed wywołaniem do:</span><span class="sxs-lookup"><span data-stu-id="2d331-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="2d331-267">Skonfiguruj oprogramowanie pośredniczące przekazywania certyfikatów, aby określić nazwę nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2d331-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="2d331-268">W `Startup.ConfigureServices`polu Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące tworzy certyfikat:</span><span class="sxs-lookup"><span data-stu-id="2d331-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="2d331-269">Jeśli serwer proxy nie jest base64-kodowanie certyfikatu (jak w przypadku `HeaderConverter` Nginx), należy ustawić opcję.</span><span class="sxs-lookup"><span data-stu-id="2d331-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="2d331-270">Rozważmy poniższy `Startup.ConfigureServices`przykład w:</span><span class="sxs-lookup"><span data-stu-id="2d331-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="2d331-271">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2d331-271">Troubleshoot</span></span>

<span data-ttu-id="2d331-272">Jeśli nagłówki nie są przekazywane dalej zgodnie z oczekiwaniami, włącz [rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2d331-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2d331-273">Jeśli dzienniki nie zawierają wystarczających informacji, aby rozwiązać problem, wylicz nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="2d331-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="2d331-274">Użyj wbudowanego oprogramowania pośredniczącego, aby zapisywać nagłówki żądań do odpowiedzi aplikacji lub rejestrować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="2d331-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="2d331-275">Aby zapisać nagłówki do odpowiedzi aplikacji, umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminalu natychmiast po wywołaniu w :</span><span class="sxs-lookup"><span data-stu-id="2d331-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="2d331-276">Można zapisywać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2d331-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="2d331-277">Zapisywanie do dzienników umożliwia normalnie funkcjonować witryny podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="2d331-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="2d331-278">Aby napisać dzienniki, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="2d331-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="2d331-279">Wstrzyknąć `ILogger<Startup>` do klasy, `Startup` jak opisano w Tworzenie [dzienników w starcie](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="2d331-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="2d331-280">Umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`natychmiast po wywołaniu w .</span><span class="sxs-lookup"><span data-stu-id="2d331-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2d331-281">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są `X-Original-{For|Proto|Host}`przenoszone do .</span><span class="sxs-lookup"><span data-stu-id="2d331-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="2d331-282">Jeśli w danym nagłówku znajduje się wiele wartości, oprogramowanie pośredniczące nagłówków przesyłanych dalej przetwarza nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="2d331-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2d331-283">Wartość `ForwardLimit` domyślna jest `1` (jeden), więc tylko po prawej stronie wartość `ForwardLimit` z nagłówków jest przetwarzany, chyba że wartość jest zwiększona.</span><span class="sxs-lookup"><span data-stu-id="2d331-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="2d331-284">Oryginalny zdalny adres IP żądania musi `KnownProxies` być `KnownNetworks` zgodny z wpisem na listach lub przed przetworzeniem nagłówków przesyłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="2d331-285">Ogranicza to fałszowanie nagłówka przez nieuchównienie przesyłania dalej od niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="2d331-286">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="2d331-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="2d331-287">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="2d331-288">Jeśli serwer jest zaufanym serwerem proxy, dodaj `KnownProxies` adres IP serwera `KnownNetworks`do `Startup.ConfigureServices`(lub dodaj zaufaną sieć) w pliku .</span><span class="sxs-lookup"><span data-stu-id="2d331-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d331-289">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego nagłówków dalej.](#forwarded-headers-middleware-options)</span><span class="sxs-lookup"><span data-stu-id="2d331-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="2d331-290">Zezwalaj tylko zaufanym serwerom proxy i sieciom na przekazywanie nagłówków.</span><span class="sxs-lookup"><span data-stu-id="2d331-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="2d331-291">W przeciwnym razie możliwe są ataki [fałszowania adresów IP.](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="2d331-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d331-292">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2d331-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="2d331-293">Poradnia zabezpieczeń firmy Microsoft CVE-2018-0787: luka w zabezpieczeniach ASP.NET rdzeń z podniesieniem uprawnień</span><span class="sxs-lookup"><span data-stu-id="2d331-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2d331-294">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu modułu rdzenia IIS/ASP.NET, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="2d331-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="2d331-295">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu, zanim dotrze do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2d331-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="2d331-296">Gdy żądania HTTPS są przełączone przez protokół HTTP, oryginalny schemat (HTTPS) jest tracony i musi być przekazytywnie w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="2d331-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="2d331-297">Ponieważ aplikacja odbiera żądanie z serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, źródłowy adres IP klienta musi być również przekazywane w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="2d331-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="2d331-298">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="2d331-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="2d331-299">Nagłówki przekazywane dalej</span><span class="sxs-lookup"><span data-stu-id="2d331-299">Forwarded headers</span></span>

<span data-ttu-id="2d331-300">Zgodnie z konwencją serwery proxy prześliją informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d331-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="2d331-301">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="2d331-301">Header</span></span> | <span data-ttu-id="2d331-302">Opis</span><span class="sxs-lookup"><span data-stu-id="2d331-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2d331-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="2d331-303">X-Forwarded-For</span></span> | <span data-ttu-id="2d331-304">Przechowuje informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="2d331-305">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="2d331-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="2d331-306">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, w którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="2d331-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="2d331-307">Kolejne identyfikatory serwera proxy są następcami.</span><span class="sxs-lookup"><span data-stu-id="2d331-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="2d331-308">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="2d331-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="2d331-309">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportu.</span><span class="sxs-lookup"><span data-stu-id="2d331-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="2d331-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="2d331-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="2d331-311">Wartość schematu źródłowego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2d331-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="2d331-312">Wartość może być również lista schematów, jeśli żądanie przechodziło przez wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="2d331-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="2d331-313">X-Forwarded-Host</span></span> | <span data-ttu-id="2d331-314">Oryginalna wartość pola Nagłówek hosta.</span><span class="sxs-lookup"><span data-stu-id="2d331-314">The original value of the Host header field.</span></span> <span data-ttu-id="2d331-315">Zazwyczaj serwery proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="2d331-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="2d331-316">Informacje na temat luki w zabezpieczeniach uprawnień, która dotyczy systemów, w których serwer proxy nie sprawdza poprawności ani nie ogranicza nagłówków hostów do znanych dobrych wartości, zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295)</span><span class="sxs-lookup"><span data-stu-id="2d331-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="2d331-317">Oprogramowanie pośredniczące nagłówków przesyłanych dalej z pakietu [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) odczytuje te nagłówki <xref:Microsoft.AspNetCore.Http.HttpContext>i wypełnia skojarzone pola w programie .</span><span class="sxs-lookup"><span data-stu-id="2d331-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="2d331-318">Oprogramowanie pośredniczące jest aktualizowane:</span><span class="sxs-lookup"><span data-stu-id="2d331-318">The middleware updates:</span></span>

* <span data-ttu-id="2d331-319">[Zestaw adresów HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; przy użyciu wartości nagłówka. `X-Forwarded-For`</span><span class="sxs-lookup"><span data-stu-id="2d331-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="2d331-320">Dodatkowe ustawienia wpływają na `RemoteIpAddress`sposób ustawiania oprogramowania pośredniczącego .</span><span class="sxs-lookup"><span data-stu-id="2d331-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="2d331-321">Aby uzyskać szczegółowe informacje, zobacz [opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2d331-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="2d331-322">[Zestaw httpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; `X-Forwarded-Proto` przy użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2d331-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="2d331-323">[Zestaw httpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; `X-Forwarded-Host` przy użyciu wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2d331-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="2d331-324">Można skonfigurować [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania pośredniczącego oprogramowania nagłówkowego przesyłane dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="2d331-325">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="2d331-325">The default settings are:</span></span>

* <span data-ttu-id="2d331-326">Istnieje tylko *jeden serwer proxy* między aplikacją a źródłem żądań.</span><span class="sxs-lookup"><span data-stu-id="2d331-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="2d331-327">Tylko adresy sprzężenia zwrotnego są skonfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="2d331-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="2d331-328">Przesłane dalej nagłówki `X-Forwarded-For` mają `X-Forwarded-Proto`nazwy i .</span><span class="sxs-lookup"><span data-stu-id="2d331-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="2d331-329">Nie wszystkie urządzenia sieciowe dodają `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` nagłówki bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2d331-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="2d331-330">Jeśli żądania pośredniczące nie zawierają tych nagłówków po dotarciu do aplikacji, należy zapoznać się ze wskazówkami producenta urządzenia.</span><span class="sxs-lookup"><span data-stu-id="2d331-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="2d331-331">Jeśli urządzenie używa innych nazw `X-Forwarded-For` `X-Forwarded-Proto`nagłówków <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> niż <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> i ustawia opcje i opcje, aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="2d331-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="2d331-332">Aby uzyskać więcej informacji, zobacz [Opcje oprogramowania pośredniczącego nagłówków przesyłania dalej](#forwarded-headers-middleware-options) i [Konfiguracja serwera proxy, który używa różnych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="2d331-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="2d331-333">Moduł podstawowy IIS/IIS Express i ASP.NET</span><span class="sxs-lookup"><span data-stu-id="2d331-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="2d331-334">Oprogramowanie pośredniczące przekazywania nagłówków jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usługi IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za iIS i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d331-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="2d331-335">Oprogramowanie pośredniczące nagłówków przesyłanych dalej jest aktywowane w celu uruchomienia jako pierwsze w potoku oprogramowania pośredniczącego z ograniczoną konfiguracją specyficzną dla ASP.NET Modułu Rdzenia z powodu obaw dotyczących zaufania z przesłałymi nagłówkami (na przykład [faofing IP).](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="2d331-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="2d331-336">Oprogramowanie pośredniczące jest skonfigurowane do przekazywania `X-Forwarded-For` nagłówków i `X-Forwarded-Proto` i jest ograniczone do jednego serwera proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="2d331-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="2d331-337">Jeśli wymagana jest dodatkowa [konfiguracja, zobacz opcje oprogramowania pośredniczącego nagłówków przesyłanych dalej](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2d331-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2d331-338">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="2d331-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2d331-339">Poza używaniem [integracji iis](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model)oprogramowanie pośredniczące nagłówków przesyłanych dalej nie jest domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="2d331-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="2d331-340">Oprogramowanie pośredniczące nagłówków przesyłanych dalej musi być włączone, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikacja przetwarzała przesłane nagłówki za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="2d331-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="2d331-341">Po włączeniu oprogramowania <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pośredniczącego, jeśli oprogramowanie pośredniczące nie jest określone, domyślne [forwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2d331-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="2d331-342">Skonfiguruj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące, aby przesłać `X-Forwarded-For` dalej nagłówki i `X-Forwarded-Proto` nagłówki w pliku `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2d331-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d331-343">Wywołać <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="2d331-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="2d331-344">Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nie są `Startup.ConfigureServices` określone w lub bezpośrednio <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>do metody rozszerzenia z , domyślne nagłówki do przekazania są [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2d331-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="2d331-345">Właściwość <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musi być skonfigurowana z nagłówkami do przekazania.</span><span class="sxs-lookup"><span data-stu-id="2d331-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="2d331-346">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="2d331-346">Nginx configuration</span></span>

<span data-ttu-id="2d331-347">Aby `X-Forwarded-For` przesłać `X-Forwarded-Proto` dalej nagłówki <xref:host-and-deploy/linux-nginx#configure-nginx>i nagłówki, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2d331-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="2d331-348">Aby uzyskać więcej informacji, zobacz [NGINX: Korzystanie z nagłówka Przesyłania dalej](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="2d331-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="2d331-349">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="2d331-349">Apache configuration</span></span>

<span data-ttu-id="2d331-350">`X-Forwarded-For`jest dodawany automatycznie (patrz [Mod_proxy modułu Apache: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="2d331-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="2d331-351">Aby uzyskać informacje na `X-Forwarded-Proto` temat <xref:host-and-deploy/linux-apache#configure-apache>przekazywania nagłówka, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2d331-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="2d331-352">Opcje oprogramowania pośredniczącego nagłówków z przekazywaniem dalej</span><span class="sxs-lookup"><span data-stu-id="2d331-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="2d331-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>kontrolować zachowanie oprogramowania pośredniczącego nagłówków przesyłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="2d331-354">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="2d331-354">The following example changes the default values:</span></span>

* <span data-ttu-id="2d331-355">Ogranicz liczbę wpisów w nagłówkach `2`przekazy.</span><span class="sxs-lookup"><span data-stu-id="2d331-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="2d331-356">Dodaj znany adres `127.0.10.1`serwera proxy .</span><span class="sxs-lookup"><span data-stu-id="2d331-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="2d331-357">Zmień nazwę nagłówka przesyłane `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name`dalej z domyślnej na .</span><span class="sxs-lookup"><span data-stu-id="2d331-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="2d331-358">Opcja</span><span class="sxs-lookup"><span data-stu-id="2d331-358">Option</span></span> | <span data-ttu-id="2d331-359">Opis</span><span class="sxs-lookup"><span data-stu-id="2d331-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="2d331-360">Ogranicza hosty `X-Forwarded-Host` przez nagłówek do podanych wartości.</span><span class="sxs-lookup"><span data-stu-id="2d331-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="2d331-361">Wartości są porównywane przy użyciu liczby porządkowej-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="2d331-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="2d331-362">Należy wykluczyć numery portów.</span><span class="sxs-lookup"><span data-stu-id="2d331-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="2d331-363">Jeśli lista jest pusta, wszystkie hosty są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="2d331-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="2d331-364">Symbol wieloznaczny `*` najwyższego poziomu umożliwia wszystkim niepustym hostom.</span><span class="sxs-lookup"><span data-stu-id="2d331-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="2d331-365">Symbole wieloznaczne poddomeny są dozwolone, ale nie pasują do domeny głównej.</span><span class="sxs-lookup"><span data-stu-id="2d331-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="2d331-366">Na przykład `*.contoso.com` pasuje do `foo.contoso.com` poddomeny, `contoso.com`ale nie do domeny głównej .</span><span class="sxs-lookup"><span data-stu-id="2d331-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="2d331-367">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [Punycode](https://tools.ietf.org/html/rfc3492) do dopasowania.</span><span class="sxs-lookup"><span data-stu-id="2d331-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="2d331-368">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`być w [formie konwencjonalnej](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład).</span><span class="sxs-lookup"><span data-stu-id="2d331-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="2d331-369">Adresy IPv6 nie są specjalne cased, aby sprawdzić, czy logiczna równość między różnymi formatami i nie jest wykonywana kanonizacja.</span><span class="sxs-lookup"><span data-stu-id="2d331-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="2d331-370">Niepodtrzymanie dozwolonych hostów może zezwolić osobie atakującej na fałszowanie łączy generowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2d331-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="2d331-371">Wartość domyślna to `IList<string>`pusta .</span><span class="sxs-lookup"><span data-stu-id="2d331-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="2d331-372">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="2d331-373">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-For` ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2d331-374">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2d331-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="2d331-375">Określa, które podmioty przekazu powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="2d331-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="2d331-376">Zobacz [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="2d331-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="2d331-377">Typowe wartości przypisane do `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tej właściwości to .</span><span class="sxs-lookup"><span data-stu-id="2d331-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="2d331-378">Wartością domyślną jest [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2d331-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="2d331-379">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="2d331-380">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Host` ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2d331-381">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2d331-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="2d331-382">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="2d331-383">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa nagłówka, `X-Forwarded-Proto` ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2d331-384">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2d331-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="2d331-385">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="2d331-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="2d331-386">Ustaw, `null` aby wyłączyć limit, ale należy `KnownProxies` to `KnownNetworks` zrobić tylko wtedy, gdy lub są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="2d331-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="2d331-387">Ustawienie wartości`null` niewartościowej jest środkiem ostrożności (ale nie gwarancją) w celu ochrony przed nieprawidłowo skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="2d331-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="2d331-388">Przekazywane nagłówki Oprogramowanie pośredniczące przetwarza nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="2d331-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2d331-389">Jeśli używana jest`1`wartość domyślna ( ) przetwarzana jest tylko wartość po `ForwardLimit` prawej stronie z nagłówków, chyba że wartość jest zwiększana.</span><span class="sxs-lookup"><span data-stu-id="2d331-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="2d331-390">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="2d331-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="2d331-391">Zakresy adresów znanych sieci do akceptowania przesłanych dalej nagłówków.</span><span class="sxs-lookup"><span data-stu-id="2d331-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="2d331-392">Podaj zakresy adresów IP przy użyciu notacji CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="2d331-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="2d331-393">Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="2d331-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2d331-394">Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2d331-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2d331-395">Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2d331-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2d331-396">Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="2d331-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2d331-397">Wartość domyślna `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> to> zawierający pojedynczy `IPAddress.Loopback`wpis dla .</span><span class="sxs-lookup"><span data-stu-id="2d331-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="2d331-398">Adresy znanych serwerów proxy do akceptowania przesłanych dalej nagłówków.</span><span class="sxs-lookup"><span data-stu-id="2d331-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="2d331-399">Służy `KnownProxies` do określania dokładnych dopasowań adresów IP.</span><span class="sxs-lookup"><span data-stu-id="2d331-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="2d331-400">Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1`IPv4 reprezentowanym w IPv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="2d331-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2d331-401">Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2d331-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2d331-402">Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2d331-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2d331-403">Aby uzyskać więcej informacji, zobacz [konfigurację adresu IPv4 reprezentowanego jako sekcja adresu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="2d331-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2d331-404">Wartość domyślna `IList` \< <xref:System.Net.IPAddress> to> zawierający pojedynczy `IPAddress.IPv6Loopback`wpis dla .</span><span class="sxs-lookup"><span data-stu-id="2d331-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="2d331-405">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="2d331-406">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="2d331-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="2d331-407">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="2d331-408">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="2d331-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="2d331-409">Użyj nagłówka określonego przez tę właściwość zamiast nagłówka określonego przez [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2d331-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="2d331-410">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2d331-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="2d331-411">Wymagaj, aby liczba wartości nagłówka była synchronizowana między [przetwarzanego pliku ForwardedHeadersOptions.ForwardedHeaders.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="2d331-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="2d331-412">Wartość domyślna w ASP.NET Core 1.x to `true`.</span><span class="sxs-lookup"><span data-stu-id="2d331-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="2d331-413">Wartość domyślna w ASP.NET Core 2.0 `false`lub nowszym to .</span><span class="sxs-lookup"><span data-stu-id="2d331-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="2d331-414">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="2d331-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="2d331-415">Gdy nie można dodać nagłówków przesyłanych dalej, a wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="2d331-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="2d331-416">W niektórych przypadkach może nie być możliwe dodanie przesłanych dalej nagłówków do żądań przesłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2d331-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="2d331-417">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są `Startup.Configure` HTTPS, schemat można ustawić ręcznie przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="2d331-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="2d331-418">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku deweloperskim lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="2d331-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="2d331-419">Radzenie sobie z bazą ścieżki i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="2d331-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="2d331-420">Niektóre serwery proxy przechodzą ścieżkę w stanie nienaruszonym, ale ze ścieżką podstawową aplikacji, która powinna zostać usunięta, aby routing działał poprawnie.</span><span class="sxs-lookup"><span data-stu-id="2d331-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="2d331-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) oprogramowanie pośredniczące dzieli ścieżkę na [httpRequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę podstawową aplikacji do [httpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="2d331-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="2d331-422">Jeśli `/foo` jest to ścieżka podstawowa aplikacji `/foo/api/1`dla ścieżki `Request.PathBase` proxy `/foo` `Request.Path` przekazywane `/api/1` jako , oprogramowanie pośredniczące ustawia się do i do następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="2d331-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="2d331-423">Oryginalna ścieżka i podstawa ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące jest wywoływane ponownie w odwrotnej kolejności.</span><span class="sxs-lookup"><span data-stu-id="2d331-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="2d331-424">Aby uzyskać więcej informacji na <xref:fundamentals/middleware/index>temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2d331-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="2d331-425">Jeśli serwer proxy przycina ścieżkę (na przykład przekierowanie `/foo/api/1` do `/api/1`), napraw przekierowania i łącza, ustawiając właściwość [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="2d331-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="2d331-426">Jeśli serwer proxy dodaje dane ścieżki, odrzuć część ścieżki, aby naprawić przekierowania i łącza za pomocą <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywanie do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> właściwości:</span><span class="sxs-lookup"><span data-stu-id="2d331-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="2d331-427">Konfiguracja serwera proxy, który używa różnych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="2d331-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="2d331-428">Jeśli serwer proxy nie używa `X-Forwarded-For` nagłówków o nazwie i `X-Forwarded-Proto` do przekazywania dalej <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> informacji o adresie/porcie i początkowym schemacie serwera proxy, ustaw i opcje zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="2d331-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="2d331-429">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="2d331-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="2d331-430">Jeśli serwer korzysta z gniazd dwumodowych, adresy IPv4 są dostarczane w `10.0.0.1` formacie IPv6 (na przykład w `::ffff:10.0.0.1` `::ffff:a00:1`IPv4 reprezentowanym w IPv6 jako lub ).</span><span class="sxs-lookup"><span data-stu-id="2d331-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="2d331-431">Zobacz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2d331-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2d331-432">Określ, czy ten format jest wymagany, patrząc na [httpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2d331-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="2d331-433">W poniższym przykładzie adres sieciowy, który dostarcza przekazane `KnownNetworks` nagłówki, jest dodawany do listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="2d331-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="2d331-434">Adres IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="2d331-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="2d331-435">Przekonwertowany adres IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="2d331-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="2d331-436">Przekonwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="2d331-436">Converted prefix length: 104</span></span>

<span data-ttu-id="2d331-437">Można również podać adres w formacie szesnastkowym (reprezentowanym`10.11.12.1` w IPv6 jako `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="2d331-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="2d331-438">Podczas konwertowania adresu IPv4 na protokół IPv6 dodaj 96 do prefiksu CIDR Length (w`8` przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="2d331-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="2d331-439">Przesyłanie dalej schematu dla odwrotnych serwerów proxy systemu Linux i innych niż IIS</span><span class="sxs-lookup"><span data-stu-id="2d331-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="2d331-440">Aplikacje, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> które wywołują i umieszczają witrynę w nieskończonej pętli, jeśli są wdrażane w usłudze Azure Linux App Service, maszynie wirtualnej systemu Azure Linux (VM) lub za dowolnym innym serwerem proxy wstecznym oprócz usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2d331-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="2d331-441">TLS jest zakończony przez odwrotny serwer proxy, a Kestrel nie jest świadomy prawidłowego schematu żądań.</span><span class="sxs-lookup"><span data-stu-id="2d331-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="2d331-442">OAuth i OIDC również nie w tej konfiguracji, ponieważ generują niepoprawne przekierowania.</span><span class="sxs-lookup"><span data-stu-id="2d331-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="2d331-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>dodaje i konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej podczas uruchamiania za usługami IIS, ale nie ma pasującej automatycznej konfiguracji dla systemu Linux (integracja Apache lub Nginx).</span><span class="sxs-lookup"><span data-stu-id="2d331-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="2d331-444">Aby przesłać schemat dalej z serwera proxy w scenariuszach innych niż IIS, dodaj i skonfiguruj oprogramowanie pośredniczące nagłówków przesłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="2d331-445">W `Startup.ConfigureServices`, użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2d331-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="2d331-446">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2d331-446">Troubleshoot</span></span>

<span data-ttu-id="2d331-447">Jeśli nagłówki nie są przekazywane dalej zgodnie z oczekiwaniami, włącz [rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2d331-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2d331-448">Jeśli dzienniki nie zawierają wystarczających informacji, aby rozwiązać problem, wylicz nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="2d331-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="2d331-449">Użyj wbudowanego oprogramowania pośredniczącego, aby zapisywać nagłówki żądań do odpowiedzi aplikacji lub rejestrować nagłówki.</span><span class="sxs-lookup"><span data-stu-id="2d331-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="2d331-450">Aby zapisać nagłówki do odpowiedzi aplikacji, umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminalu natychmiast po wywołaniu w :</span><span class="sxs-lookup"><span data-stu-id="2d331-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="2d331-451">Można zapisywać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2d331-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="2d331-452">Zapisywanie do dzienników umożliwia normalnie funkcjonować witryny podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="2d331-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="2d331-453">Aby napisać dzienniki, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="2d331-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="2d331-454">Wstrzyknąć `ILogger<Startup>` do klasy, `Startup` jak opisano w Tworzenie [dzienników w starcie](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="2d331-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="2d331-455">Umieść następujące wbudowane oprogramowanie pośredniczące <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`natychmiast po wywołaniu w .</span><span class="sxs-lookup"><span data-stu-id="2d331-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2d331-456">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są `X-Original-{For|Proto|Host}`przenoszone do .</span><span class="sxs-lookup"><span data-stu-id="2d331-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="2d331-457">Jeśli w danym nagłówku znajduje się wiele wartości, oprogramowanie pośredniczące nagłówków przesyłanych dalej przetwarza nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="2d331-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2d331-458">Wartość `ForwardLimit` domyślna jest `1` (jeden), więc tylko po prawej stronie wartość `ForwardLimit` z nagłówków jest przetwarzany, chyba że wartość jest zwiększona.</span><span class="sxs-lookup"><span data-stu-id="2d331-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="2d331-459">Oryginalny zdalny adres IP żądania musi `KnownProxies` być `KnownNetworks` zgodny z wpisem na listach lub przed przetworzeniem nagłówków przesyłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="2d331-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="2d331-460">Ogranicza to fałszowanie nagłówka przez nieuchównienie przesyłania dalej od niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="2d331-461">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="2d331-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="2d331-462">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="2d331-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="2d331-463">Jeśli serwer jest zaufanym serwerem proxy, dodaj `KnownProxies` adres IP serwera `KnownNetworks`do `Startup.ConfigureServices`(lub dodaj zaufaną sieć) w pliku .</span><span class="sxs-lookup"><span data-stu-id="2d331-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d331-464">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego nagłówków dalej.](#forwarded-headers-middleware-options)</span><span class="sxs-lookup"><span data-stu-id="2d331-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="2d331-465">Zezwalaj tylko zaufanym serwerom proxy i sieciom na przekazywanie nagłówków.</span><span class="sxs-lookup"><span data-stu-id="2d331-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="2d331-466">W przeciwnym razie możliwe są ataki [fałszowania adresów IP.](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="2d331-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d331-467">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2d331-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="2d331-468">Poradnia zabezpieczeń firmy Microsoft CVE-2018-0787: luka w zabezpieczeniach ASP.NET rdzeń z podniesieniem uprawnień</span><span class="sxs-lookup"><span data-stu-id="2d331-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
