---
title: Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia
author: rick-anderson
description: Dowiedz się więcej o konfigurowaniu aplikacji hostowanych za serwerami proxy i usługami równoważenia obciążenia, które często zasłaniają ważne informacje o żądaniu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e63821743dad565b51d5c2360dcc2fbf0632754f
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530050"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="f5917-103">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="f5917-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="f5917-104">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="f5917-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5917-105">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="f5917-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="f5917-106">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f5917-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="f5917-107">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="f5917-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="f5917-108">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="f5917-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="f5917-109">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="f5917-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="f5917-110">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="f5917-110">Forwarded headers</span></span>

<span data-ttu-id="f5917-111">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f5917-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="f5917-112">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="f5917-112">Header</span></span> | <span data-ttu-id="f5917-113">Opis</span><span class="sxs-lookup"><span data-stu-id="f5917-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="f5917-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="f5917-114">X-Forwarded-For</span></span> | <span data-ttu-id="f5917-115">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="f5917-116">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="f5917-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="f5917-117">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="f5917-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="f5917-118">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="f5917-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="f5917-119">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="f5917-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="f5917-120">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="f5917-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="f5917-121">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="f5917-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="f5917-122">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f5917-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="f5917-123">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="f5917-124">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="f5917-124">X-Forwarded-Host</span></span> | <span data-ttu-id="f5917-125">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="f5917-125">The original value of the Host header field.</span></span> <span data-ttu-id="f5917-126">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="f5917-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="f5917-127">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="f5917-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="f5917-128">Przekazane nagłówki pośredniczące ( <xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware> ), odczytują te nagłówki i wypełniają pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="f5917-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="f5917-129">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="f5917-129">The middleware updates:</span></span>

* <span data-ttu-id="f5917-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="f5917-131">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="f5917-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="f5917-132">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="f5917-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="f5917-133">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="f5917-134">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="f5917-135">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="f5917-136">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="f5917-136">The default settings are:</span></span>

* <span data-ttu-id="f5917-137">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="f5917-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="f5917-138">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="f5917-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="f5917-139">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="f5917-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="f5917-140">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f5917-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="f5917-141">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="f5917-142">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="f5917-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="f5917-143">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="f5917-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="f5917-144">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5917-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="f5917-145">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5917-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="f5917-146">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="f5917-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="f5917-147">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="f5917-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="f5917-148">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="f5917-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f5917-149">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="f5917-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f5917-150">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f5917-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="f5917-151">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="f5917-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="f5917-152">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="f5917-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="f5917-153">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f5917-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="f5917-154">Przekierowane nagłówki — zamówienie oprogramowania</span><span class="sxs-lookup"><span data-stu-id="f5917-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="f5917-155">Przekierowane nagłówki oprogramowanie pośredniczące powinno być uruchamiane przed innym programem pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="f5917-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="f5917-156">Takie porządkowanie zapewnia, że oprogramowanie pośredniczące polegające na informacjach o przekazanych nagłówkach może zużywać wartości nagłówka do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="f5917-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="f5917-157">Przekierowane nagłówki oprogramowanie pośredniczące może zostać uruchomione po obsłudze diagnostyki i błędów, ale należy je uruchomić przed wywołaniem `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="f5917-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="f5917-158">Alternatywnie Wywołaj `UseForwardedHeaders` przed diagnostyką:</span><span class="sxs-lookup"><span data-stu-id="f5917-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="f5917-159">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="f5917-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="f5917-160"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="f5917-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="f5917-161">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="f5917-161">Nginx configuration</span></span>

<span data-ttu-id="f5917-162">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="f5917-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="f5917-163">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="f5917-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="f5917-164">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="f5917-164">Apache configuration</span></span>

<span data-ttu-id="f5917-165">`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="f5917-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="f5917-166">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="f5917-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="f5917-167">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="f5917-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="f5917-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="f5917-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="f5917-169">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="f5917-169">The following example changes the default values:</span></span>

* <span data-ttu-id="f5917-170">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="f5917-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="f5917-171">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="f5917-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="f5917-172">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="f5917-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="f5917-173">Opcja</span><span class="sxs-lookup"><span data-stu-id="f5917-173">Option</span></span> | <span data-ttu-id="f5917-174">Opis</span><span class="sxs-lookup"><span data-stu-id="f5917-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="f5917-175">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="f5917-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="f5917-176">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="f5917-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="f5917-177">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="f5917-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="f5917-178">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="f5917-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="f5917-179">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="f5917-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="f5917-180">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="f5917-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="f5917-181">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="f5917-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="f5917-182">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="f5917-183">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="f5917-184">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="f5917-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="f5917-185">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="f5917-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="f5917-186">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="f5917-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="f5917-187">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="f5917-188">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="f5917-189">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="f5917-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="f5917-190">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="f5917-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="f5917-191">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="f5917-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="f5917-192">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="f5917-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="f5917-193">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="f5917-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="f5917-194">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="f5917-195">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="f5917-196">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="f5917-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="f5917-197">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="f5917-198">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="f5917-199">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="f5917-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="f5917-200">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="f5917-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="f5917-201">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="f5917-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="f5917-202">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="f5917-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="f5917-203">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="f5917-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="f5917-204">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="f5917-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="f5917-205">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="f5917-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="f5917-206">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="f5917-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="f5917-207">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="f5917-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="f5917-208">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="f5917-209">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="f5917-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="f5917-210">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="f5917-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="f5917-211">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="f5917-211">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="f5917-212">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="f5917-212">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="f5917-213">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="f5917-213">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="f5917-214">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-214">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="f5917-215">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="f5917-215">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="f5917-216">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="f5917-216">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="f5917-217">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="f5917-217">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="f5917-218">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="f5917-219">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="f5917-219">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="f5917-220">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="f5917-221">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="f5917-221">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="f5917-222">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="f5917-223">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="f5917-223">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="f5917-224">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="f5917-224">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="f5917-225">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="f5917-225">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="f5917-226">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="f5917-226">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="f5917-227">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="f5917-227">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="f5917-228">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="f5917-228">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="f5917-229">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-229">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="f5917-230">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="f5917-230">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="f5917-231">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="f5917-231">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="f5917-232">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="f5917-232">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="f5917-233">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="f5917-233">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="f5917-234">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="f5917-234">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="f5917-235">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="f5917-235">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="f5917-236">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="f5917-236">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="f5917-237">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="f5917-237">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="f5917-238">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="f5917-238">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="f5917-239">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f5917-239">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="f5917-240">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="f5917-240">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="f5917-241">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="f5917-241">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="f5917-242">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="f5917-242">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="f5917-243">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="f5917-243">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="f5917-244">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="f5917-244">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="f5917-245">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-245">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="f5917-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="f5917-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="f5917-247">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="f5917-247">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="f5917-248">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="f5917-248">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="f5917-249">Przekazywanie certyfikatów</span><span class="sxs-lookup"><span data-stu-id="f5917-249">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="f5917-250">Azure</span><span class="sxs-lookup"><span data-stu-id="f5917-250">Azure</span></span>

<span data-ttu-id="f5917-251">Aby skonfigurować Azure App Service na potrzeby przekazywania certyfikatów, zobacz [Konfigurowanie uwierzytelniania wzajemnego TLS dla Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="f5917-251">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="f5917-252">Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5917-252">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="f5917-253">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="f5917-253">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="f5917-254">Skonfiguruj oprogramowanie pośredniczące do przekazywania certyfikatów, aby określić nazwę nagłówka używaną przez platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="f5917-254">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="f5917-255">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="f5917-255">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="f5917-256">Inne serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="f5917-256">Other web proxies</span></span>

<span data-ttu-id="f5917-257">Jeśli używany jest serwer proxy, który nie jest programem IIS lub Azure App Service routingu żądań aplikacji (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu otrzymanego w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="f5917-257">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="f5917-258">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="f5917-258">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="f5917-259">Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej certyfikatów, aby określić nazwę nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-259">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="f5917-260">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="f5917-260">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="f5917-261">Jeśli serwer proxy nie może zakodować certyfikatu Base64 (podobnie jak w przypadku Nginx), ustaw `HeaderConverter` opcję.</span><span class="sxs-lookup"><span data-stu-id="f5917-261">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="f5917-262">Rozważmy następujący przykład w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f5917-262">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="f5917-263">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="f5917-263">Troubleshoot</span></span>

<span data-ttu-id="f5917-264">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="f5917-264">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f5917-265">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="f5917-265">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="f5917-266">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="f5917-266">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="f5917-267">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f5917-267">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="f5917-268">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f5917-268">You can write to logs instead of the response body.</span></span> <span data-ttu-id="f5917-269">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-269">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="f5917-270">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f5917-270">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="f5917-271">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="f5917-271">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="f5917-272">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f5917-272">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="f5917-273">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="f5917-273">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="f5917-274">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="f5917-274">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="f5917-275">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="f5917-275">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="f5917-276">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="f5917-276">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="f5917-277">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-277">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="f5917-278">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="f5917-278">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="f5917-279">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-279">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="f5917-280">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f5917-280">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5917-281">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="f5917-281">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="f5917-282">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="f5917-282">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="f5917-283">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="f5917-283">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5917-284">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f5917-284">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="f5917-285">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="f5917-285">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5917-286">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="f5917-286">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="f5917-287">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f5917-287">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="f5917-288">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="f5917-288">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="f5917-289">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="f5917-289">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="f5917-290">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="f5917-290">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="f5917-291">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="f5917-291">Forwarded headers</span></span>

<span data-ttu-id="f5917-292">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="f5917-292">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="f5917-293">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="f5917-293">Header</span></span> | <span data-ttu-id="f5917-294">Opis</span><span class="sxs-lookup"><span data-stu-id="f5917-294">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="f5917-295">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="f5917-295">X-Forwarded-For</span></span> | <span data-ttu-id="f5917-296">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-296">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="f5917-297">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="f5917-297">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="f5917-298">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="f5917-298">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="f5917-299">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="f5917-299">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="f5917-300">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="f5917-300">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="f5917-301">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="f5917-301">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="f5917-302">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="f5917-302">X-Forwarded-Proto</span></span> | <span data-ttu-id="f5917-303">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f5917-303">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="f5917-304">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-304">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="f5917-305">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="f5917-305">X-Forwarded-Host</span></span> | <span data-ttu-id="f5917-306">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="f5917-306">The original value of the Host header field.</span></span> <span data-ttu-id="f5917-307">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="f5917-307">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="f5917-308">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="f5917-308">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="f5917-309">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="f5917-309">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="f5917-310">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="f5917-310">The middleware updates:</span></span>

* <span data-ttu-id="f5917-311">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-311">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="f5917-312">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="f5917-312">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="f5917-313">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="f5917-313">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="f5917-314">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-314">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="f5917-315">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="f5917-315">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="f5917-316">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-316">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="f5917-317">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="f5917-317">The default settings are:</span></span>

* <span data-ttu-id="f5917-318">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="f5917-318">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="f5917-319">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="f5917-319">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="f5917-320">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="f5917-320">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="f5917-321">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f5917-321">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="f5917-322">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-322">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="f5917-323">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="f5917-323">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="f5917-324">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="f5917-324">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="f5917-325">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5917-325">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="f5917-326">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5917-326">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="f5917-327">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="f5917-327">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="f5917-328">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="f5917-328">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="f5917-329">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="f5917-329">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f5917-330">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="f5917-330">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f5917-331">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="f5917-331">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="f5917-332">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="f5917-332">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="f5917-333">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="f5917-333">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="f5917-334">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f5917-334">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5917-335">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="f5917-335">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="f5917-336">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="f5917-336">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="f5917-337"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="f5917-337">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="f5917-338">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="f5917-338">Nginx configuration</span></span>

<span data-ttu-id="f5917-339">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="f5917-339">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="f5917-340">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="f5917-340">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="f5917-341">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="f5917-341">Apache configuration</span></span>

<span data-ttu-id="f5917-342">`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="f5917-342">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="f5917-343">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="f5917-343">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="f5917-344">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="f5917-344">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="f5917-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="f5917-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="f5917-346">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="f5917-346">The following example changes the default values:</span></span>

* <span data-ttu-id="f5917-347">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="f5917-347">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="f5917-348">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="f5917-348">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="f5917-349">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="f5917-349">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="f5917-350">Opcja</span><span class="sxs-lookup"><span data-stu-id="f5917-350">Option</span></span> | <span data-ttu-id="f5917-351">Opis</span><span class="sxs-lookup"><span data-stu-id="f5917-351">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="f5917-352">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="f5917-352">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="f5917-353">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="f5917-353">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="f5917-354">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="f5917-354">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="f5917-355">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="f5917-355">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="f5917-356">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="f5917-356">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="f5917-357">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="f5917-357">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="f5917-358">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="f5917-358">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="f5917-359">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-359">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="f5917-360">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-360">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="f5917-361">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="f5917-361">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="f5917-362">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="f5917-362">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="f5917-363">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="f5917-363">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="f5917-364">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-364">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="f5917-365">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-365">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="f5917-366">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="f5917-366">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="f5917-367">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="f5917-367">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="f5917-368">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="f5917-368">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="f5917-369">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="f5917-369">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="f5917-370">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="f5917-370">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="f5917-371">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-371">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="f5917-372">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-372">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="f5917-373">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="f5917-373">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="f5917-374">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-374">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="f5917-375">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-375">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="f5917-376">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="f5917-376">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="f5917-377">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="f5917-377">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="f5917-378">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="f5917-378">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="f5917-379">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="f5917-379">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="f5917-380">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="f5917-380">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="f5917-381">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="f5917-381">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="f5917-382">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="f5917-382">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="f5917-383">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="f5917-383">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="f5917-384">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="f5917-384">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="f5917-385">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-385">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="f5917-386">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="f5917-386">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="f5917-387">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="f5917-387">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="f5917-388">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="f5917-388">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="f5917-389">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="f5917-389">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="f5917-390">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="f5917-390">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="f5917-391">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="f5917-391">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="f5917-392">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-392">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="f5917-393">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="f5917-393">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="f5917-394">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="f5917-394">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="f5917-395">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="f5917-395">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="f5917-396">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="f5917-396">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="f5917-397">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-397">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="f5917-398">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="f5917-398">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="f5917-399">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-399">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="f5917-400">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="f5917-400">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="f5917-401">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="f5917-401">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="f5917-402">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="f5917-402">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="f5917-403">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="f5917-403">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="f5917-404">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="f5917-404">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="f5917-405">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="f5917-405">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="f5917-406">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="f5917-406">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="f5917-407">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="f5917-407">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="f5917-408">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f5917-408">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="f5917-409">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="f5917-409">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="f5917-410">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="f5917-410">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="f5917-411">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="f5917-411">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="f5917-412">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="f5917-412">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="f5917-413">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="f5917-413">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="f5917-414">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="f5917-414">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="f5917-415">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="f5917-415">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="f5917-416">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="f5917-416">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="f5917-417">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="f5917-417">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="f5917-418">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="f5917-418">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="f5917-419">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="f5917-419">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="f5917-420">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="f5917-420">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="f5917-421">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="f5917-421">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="f5917-422">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-422">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="f5917-423">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="f5917-423">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="f5917-424">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="f5917-424">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="f5917-425">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="f5917-425">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="f5917-426">Adres IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="f5917-426">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="f5917-427">Przekonwertowany adres IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="f5917-427">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="f5917-428">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="f5917-428">Converted prefix length: 104</span></span>

<span data-ttu-id="f5917-429">Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="f5917-429">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="f5917-430">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="f5917-430">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="f5917-431">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="f5917-431">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="f5917-432">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="f5917-432">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="f5917-433">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="f5917-433">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="f5917-434">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-434">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="f5917-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="f5917-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="f5917-436">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="f5917-436">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="f5917-437">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="f5917-437">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="f5917-438">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="f5917-438">Troubleshoot</span></span>

<span data-ttu-id="f5917-439">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="f5917-439">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f5917-440">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="f5917-440">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="f5917-441">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="f5917-441">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="f5917-442">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f5917-442">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="f5917-443">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f5917-443">You can write to logs instead of the response body.</span></span> <span data-ttu-id="f5917-444">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="f5917-444">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="f5917-445">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f5917-445">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="f5917-446">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="f5917-446">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="f5917-447">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f5917-447">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="f5917-448">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="f5917-448">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="f5917-449">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="f5917-449">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="f5917-450">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="f5917-450">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="f5917-451">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="f5917-451">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="f5917-452">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-452">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="f5917-453">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="f5917-453">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="f5917-454">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="f5917-454">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="f5917-455">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f5917-455">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5917-456">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="f5917-456">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="f5917-457">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="f5917-457">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="f5917-458">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="f5917-458">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5917-459">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f5917-459">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="f5917-460">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="f5917-460">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
