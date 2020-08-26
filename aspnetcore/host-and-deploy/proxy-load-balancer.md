---
title: Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia
author: rick-anderson
description: Dowiedz się więcej o konfigurowaniu aplikacji hostowanych za serwerami proxy i usługami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 209f75edc2901422b94c619a505e0b8316aec699
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865393"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="1bc10-103">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="1bc10-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="1bc10-104">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="1bc10-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bc10-105">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="1bc10-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="1bc10-106">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1bc10-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="1bc10-107">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="1bc10-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="1bc10-108">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="1bc10-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="1bc10-109">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="1bc10-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="1bc10-110">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="1bc10-110">Forwarded headers</span></span>

<span data-ttu-id="1bc10-111">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="1bc10-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="1bc10-112">Header</span><span class="sxs-lookup"><span data-stu-id="1bc10-112">Header</span></span> | <span data-ttu-id="1bc10-113">Opis</span><span class="sxs-lookup"><span data-stu-id="1bc10-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="1bc10-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="1bc10-114">X-Forwarded-For</span></span> | <span data-ttu-id="1bc10-115">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="1bc10-116">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="1bc10-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="1bc10-117">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="1bc10-118">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="1bc10-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="1bc10-119">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="1bc10-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="1bc10-120">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="1bc10-121">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="1bc10-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="1bc10-122">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1bc10-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="1bc10-123">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="1bc10-124">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="1bc10-124">X-Forwarded-Host</span></span> | <span data-ttu-id="1bc10-125">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="1bc10-125">The original value of the Host header field.</span></span> <span data-ttu-id="1bc10-126">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="1bc10-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="1bc10-127">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="1bc10-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="1bc10-128">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="1bc10-129">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="1bc10-129">The middleware updates:</span></span>

* <span data-ttu-id="1bc10-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="1bc10-131">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="1bc10-132">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1bc10-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="1bc10-133">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="1bc10-134">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="1bc10-135">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="1bc10-136">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="1bc10-136">The default settings are:</span></span>

* <span data-ttu-id="1bc10-137">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="1bc10-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="1bc10-138">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="1bc10-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="1bc10-139">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="1bc10-140">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="1bc10-141">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="1bc10-142">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="1bc10-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="1bc10-143">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="1bc10-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="1bc10-144">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bc10-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="1bc10-145">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bc10-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="1bc10-146">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="1bc10-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="1bc10-147">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="1bc10-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="1bc10-148">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1bc10-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1bc10-149">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="1bc10-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1bc10-150">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="1bc10-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="1bc10-151">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="1bc10-152">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1bc10-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="1bc10-153">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="1bc10-154">Przekierowane nagłówki — zamówienie oprogramowania</span><span class="sxs-lookup"><span data-stu-id="1bc10-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="1bc10-155">Przekierowane nagłówki oprogramowanie pośredniczące powinno być uruchamiane przed innym programem pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="1bc10-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="1bc10-156">Takie porządkowanie zapewnia, że oprogramowanie pośredniczące polegające na informacjach o przekazanych nagłówkach może zużywać wartości nagłówka do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="1bc10-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="1bc10-157">Przekierowane nagłówki oprogramowanie pośredniczące może zostać uruchomione po obsłudze diagnostyki i błędów, ale należy je uruchomić przed wywołaniem `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="1bc10-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="1bc10-158">Alternatywnie Wywołaj `UseForwardedHeaders` przed diagnostyką:</span><span class="sxs-lookup"><span data-stu-id="1bc10-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="1bc10-159">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1bc10-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="1bc10-160"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="1bc10-161">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="1bc10-161">Nginx configuration</span></span>

<span data-ttu-id="1bc10-162">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="1bc10-163">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="1bc10-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="1bc10-164">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="1bc10-164">Apache configuration</span></span>

<span data-ttu-id="1bc10-165">`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="1bc10-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="1bc10-166">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="1bc10-167">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="1bc10-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="1bc10-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="1bc10-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="1bc10-169">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="1bc10-169">The following example changes the default values:</span></span>

* <span data-ttu-id="1bc10-170">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="1bc10-171">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="1bc10-172">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="1bc10-173">Opcja</span><span class="sxs-lookup"><span data-stu-id="1bc10-173">Option</span></span> | <span data-ttu-id="1bc10-174">Opis</span><span class="sxs-lookup"><span data-stu-id="1bc10-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="1bc10-175">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="1bc10-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="1bc10-176">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="1bc10-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="1bc10-177">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="1bc10-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="1bc10-178">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="1bc10-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="1bc10-179">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="1bc10-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="1bc10-180">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="1bc10-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="1bc10-181">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="1bc10-182">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="1bc10-183">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="1bc10-184">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="1bc10-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="1bc10-185">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="1bc10-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="1bc10-186">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="1bc10-187">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="1bc10-188">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1bc10-189">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="1bc10-190">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="1bc10-191">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="1bc10-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="1bc10-192">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="1bc10-193">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1bc10-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="1bc10-194">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="1bc10-195">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1bc10-196">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="1bc10-197">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="1bc10-198">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1bc10-199">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="1bc10-200">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="1bc10-201">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="1bc10-202">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="1bc10-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="1bc10-203">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1bc10-204">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="1bc10-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="1bc10-205">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="1bc10-206">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="1bc10-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="1bc10-207">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="1bc10-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="1bc10-208">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1bc10-209">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1bc10-210">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1bc10-211">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1bc10-212">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="1bc10-213">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="1bc10-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="1bc10-214">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="1bc10-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="1bc10-215">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1bc10-216">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1bc10-217">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1bc10-218">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1bc10-219">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="1bc10-220">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="1bc10-221">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="1bc10-222">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="1bc10-223">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="1bc10-224">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="1bc10-225">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="1bc10-226">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="1bc10-227">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="1bc10-228">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="1bc10-229">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="1bc10-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="1bc10-230">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="1bc10-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="1bc10-231">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="1bc10-232">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="1bc10-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="1bc10-233">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="1bc10-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="1bc10-234">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="1bc10-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="1bc10-235">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="1bc10-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="1bc10-236">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="1bc10-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="1bc10-237">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="1bc10-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="1bc10-238">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="1bc10-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="1bc10-239">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="1bc10-240">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="1bc10-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="1bc10-241">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="1bc10-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="1bc10-242">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="1bc10-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="1bc10-243">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="1bc10-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="1bc10-244">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="1bc10-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="1bc10-245">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="1bc10-246">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1bc10-247">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="1bc10-248">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="1bc10-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="1bc10-249">Adres IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="1bc10-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="1bc10-250">Przekonwertowany adres IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="1bc10-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="1bc10-251">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="1bc10-251">Converted prefix length: 104</span></span>

<span data-ttu-id="1bc10-252">Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="1bc10-253">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="1bc10-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="1bc10-254">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="1bc10-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="1bc10-255">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="1bc10-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="1bc10-256">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="1bc10-257">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="1bc10-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="1bc10-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="1bc10-259">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="1bc10-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="1bc10-260">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1bc10-260">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="1bc10-261">Przekazywanie certyfikatów</span><span class="sxs-lookup"><span data-stu-id="1bc10-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="1bc10-262">Azure</span><span class="sxs-lookup"><span data-stu-id="1bc10-262">Azure</span></span>

<span data-ttu-id="1bc10-263">Aby skonfigurować Azure App Service na potrzeby przekazywania certyfikatów, zobacz [Konfigurowanie uwierzytelniania wzajemnego TLS dla Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="1bc10-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="1bc10-264">Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bc10-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="1bc10-265">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="1bc10-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="1bc10-266">Skonfiguruj oprogramowanie pośredniczące do przekazywania certyfikatów, aby określić nazwę nagłówka używaną przez platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="1bc10-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="1bc10-267">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="1bc10-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="1bc10-268">Inne serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="1bc10-268">Other web proxies</span></span>

<span data-ttu-id="1bc10-269">Jeśli używany jest serwer proxy, który nie jest programem IIS lub Azure App Service routingu żądań aplikacji (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu otrzymanego w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="1bc10-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="1bc10-270">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="1bc10-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="1bc10-271">Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej certyfikatów, aby określić nazwę nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="1bc10-272">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="1bc10-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="1bc10-273">Jeśli serwer proxy nie może zakodować certyfikatu Base64 (podobnie jak w przypadku Nginx), ustaw `HeaderConverter` opcję.</span><span class="sxs-lookup"><span data-stu-id="1bc10-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="1bc10-274">Rozważmy następujący przykład w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1bc10-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="1bc10-275">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="1bc10-275">Troubleshoot</span></span>

<span data-ttu-id="1bc10-276">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="1bc10-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1bc10-277">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="1bc10-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="1bc10-278">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="1bc10-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="1bc10-279">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1bc10-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="1bc10-280">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1bc10-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="1bc10-281">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="1bc10-282">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="1bc10-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="1bc10-283">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="1bc10-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="1bc10-284">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="1bc10-285">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="1bc10-286">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1bc10-287">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="1bc10-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="1bc10-288">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="1bc10-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="1bc10-289">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="1bc10-290">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="1bc10-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="1bc10-291">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="1bc10-292">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1bc10-293">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="1bc10-294">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="1bc10-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="1bc10-295">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="1bc10-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bc10-296">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1bc10-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="1bc10-297">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="1bc10-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bc10-298">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="1bc10-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="1bc10-299">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1bc10-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="1bc10-300">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="1bc10-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="1bc10-301">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="1bc10-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="1bc10-302">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="1bc10-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="1bc10-303">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="1bc10-303">Forwarded headers</span></span>

<span data-ttu-id="1bc10-304">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="1bc10-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="1bc10-305">Header</span><span class="sxs-lookup"><span data-stu-id="1bc10-305">Header</span></span> | <span data-ttu-id="1bc10-306">Opis</span><span class="sxs-lookup"><span data-stu-id="1bc10-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="1bc10-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="1bc10-307">X-Forwarded-For</span></span> | <span data-ttu-id="1bc10-308">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="1bc10-309">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="1bc10-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="1bc10-310">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="1bc10-311">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="1bc10-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="1bc10-312">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="1bc10-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="1bc10-313">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="1bc10-314">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="1bc10-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="1bc10-315">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1bc10-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="1bc10-316">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="1bc10-317">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="1bc10-317">X-Forwarded-Host</span></span> | <span data-ttu-id="1bc10-318">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="1bc10-318">The original value of the Host header field.</span></span> <span data-ttu-id="1bc10-319">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="1bc10-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="1bc10-320">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="1bc10-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="1bc10-321">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="1bc10-322">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="1bc10-322">The middleware updates:</span></span>

* <span data-ttu-id="1bc10-323">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="1bc10-324">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="1bc10-325">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1bc10-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="1bc10-326">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="1bc10-327">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="1bc10-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="1bc10-328">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="1bc10-329">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="1bc10-329">The default settings are:</span></span>

* <span data-ttu-id="1bc10-330">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="1bc10-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="1bc10-331">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="1bc10-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="1bc10-332">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="1bc10-333">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="1bc10-334">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="1bc10-335">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="1bc10-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="1bc10-336">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="1bc10-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="1bc10-337">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bc10-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="1bc10-338">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bc10-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="1bc10-339">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="1bc10-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="1bc10-340">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="1bc10-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="1bc10-341">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="1bc10-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1bc10-342">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="1bc10-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1bc10-343">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="1bc10-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="1bc10-344">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="1bc10-345">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1bc10-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="1bc10-346">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1bc10-347">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="1bc10-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="1bc10-348">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1bc10-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="1bc10-349"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="1bc10-350">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="1bc10-350">Nginx configuration</span></span>

<span data-ttu-id="1bc10-351">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="1bc10-352">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="1bc10-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="1bc10-353">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="1bc10-353">Apache configuration</span></span>

<span data-ttu-id="1bc10-354">`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="1bc10-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="1bc10-355">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="1bc10-356">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="1bc10-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="1bc10-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="1bc10-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="1bc10-358">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="1bc10-358">The following example changes the default values:</span></span>

* <span data-ttu-id="1bc10-359">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="1bc10-360">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="1bc10-361">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="1bc10-362">Opcja</span><span class="sxs-lookup"><span data-stu-id="1bc10-362">Option</span></span> | <span data-ttu-id="1bc10-363">Opis</span><span class="sxs-lookup"><span data-stu-id="1bc10-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="1bc10-364">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="1bc10-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="1bc10-365">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="1bc10-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="1bc10-366">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="1bc10-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="1bc10-367">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="1bc10-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="1bc10-368">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="1bc10-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="1bc10-369">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="1bc10-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="1bc10-370">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="1bc10-371">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="1bc10-372">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="1bc10-373">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="1bc10-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="1bc10-374">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="1bc10-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="1bc10-375">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="1bc10-376">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="1bc10-377">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1bc10-378">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="1bc10-379">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="1bc10-380">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="1bc10-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="1bc10-381">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="1bc10-382">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="1bc10-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="1bc10-383">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="1bc10-384">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1bc10-385">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="1bc10-386">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="1bc10-387">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="1bc10-388">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="1bc10-389">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="1bc10-390">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="1bc10-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="1bc10-391">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="1bc10-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="1bc10-392">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1bc10-393">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="1bc10-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="1bc10-394">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="1bc10-395">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="1bc10-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="1bc10-396">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="1bc10-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="1bc10-397">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1bc10-398">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1bc10-399">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1bc10-400">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1bc10-401">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="1bc10-402">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="1bc10-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="1bc10-403">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="1bc10-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="1bc10-404">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="1bc10-405">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1bc10-406">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="1bc10-407">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="1bc10-408">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="1bc10-409">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="1bc10-410">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="1bc10-411">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="1bc10-412">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="1bc10-413">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="1bc10-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="1bc10-414">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="1bc10-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="1bc10-415">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="1bc10-416">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="1bc10-417">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="1bc10-418">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="1bc10-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="1bc10-419">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="1bc10-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="1bc10-420">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1bc10-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="1bc10-421">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="1bc10-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="1bc10-422">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="1bc10-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="1bc10-423">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="1bc10-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="1bc10-424">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="1bc10-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="1bc10-425">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="1bc10-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="1bc10-426">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="1bc10-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="1bc10-427">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="1bc10-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="1bc10-428">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="1bc10-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="1bc10-429">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="1bc10-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="1bc10-430">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="1bc10-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="1bc10-431">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="1bc10-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="1bc10-432">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="1bc10-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="1bc10-433">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="1bc10-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="1bc10-434">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="1bc10-435">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="1bc10-436">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="1bc10-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="1bc10-437">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="1bc10-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="1bc10-438">Adres IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="1bc10-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="1bc10-439">Przekonwertowany adres IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="1bc10-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="1bc10-440">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="1bc10-440">Converted prefix length: 104</span></span>

<span data-ttu-id="1bc10-441">Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="1bc10-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="1bc10-442">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="1bc10-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="1bc10-443">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="1bc10-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="1bc10-444">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="1bc10-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="1bc10-445">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="1bc10-446">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="1bc10-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="1bc10-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="1bc10-448">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="1bc10-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="1bc10-449">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1bc10-449">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="1bc10-450">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="1bc10-450">Troubleshoot</span></span>

<span data-ttu-id="1bc10-451">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="1bc10-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1bc10-452">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="1bc10-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="1bc10-453">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="1bc10-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="1bc10-454">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1bc10-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="1bc10-455">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1bc10-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="1bc10-456">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="1bc10-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="1bc10-457">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="1bc10-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="1bc10-458">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="1bc10-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="1bc10-459">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="1bc10-460">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="1bc10-461">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="1bc10-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="1bc10-462">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="1bc10-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="1bc10-463">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="1bc10-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="1bc10-464">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="1bc10-465">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="1bc10-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="1bc10-466">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="1bc10-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="1bc10-467">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1bc10-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1bc10-468">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="1bc10-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="1bc10-469">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="1bc10-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="1bc10-470">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="1bc10-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bc10-471">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1bc10-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="1bc10-472">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="1bc10-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
