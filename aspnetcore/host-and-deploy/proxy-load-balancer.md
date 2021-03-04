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
ms.openlocfilehash: 28a802414fd59f684a56e2b735140438d33be740
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109952"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="d1d98-103">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="d1d98-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="d1d98-104">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="d1d98-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1d98-105">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="d1d98-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="d1d98-106">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d1d98-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="d1d98-107">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="d1d98-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="d1d98-108">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="d1d98-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="d1d98-109">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="d1d98-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="d1d98-110">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="d1d98-110">Forwarded headers</span></span>

<span data-ttu-id="d1d98-111">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1d98-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="d1d98-112">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="d1d98-112">Header</span></span> | <span data-ttu-id="d1d98-113">Opis</span><span class="sxs-lookup"><span data-stu-id="d1d98-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="d1d98-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="d1d98-114">X-Forwarded-For</span></span> | <span data-ttu-id="d1d98-115">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="d1d98-116">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="d1d98-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="d1d98-117">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="d1d98-118">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="d1d98-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="d1d98-119">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="d1d98-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="d1d98-120">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="d1d98-121">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="d1d98-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="d1d98-122">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d1d98-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="d1d98-123">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="d1d98-124">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="d1d98-124">X-Forwarded-Host</span></span> | <span data-ttu-id="d1d98-125">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="d1d98-125">The original value of the Host header field.</span></span> <span data-ttu-id="d1d98-126">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="d1d98-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="d1d98-127">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="d1d98-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="d1d98-128">Przekazane nagłówki pośredniczące ( <xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware> ), odczytują te nagłówki i wypełniają pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="d1d98-129">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d1d98-129">The middleware updates:</span></span>

* <span data-ttu-id="d1d98-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="d1d98-131">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="d1d98-132">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d1d98-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="d1d98-133">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="d1d98-134">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="d1d98-135">Aby uzyskać więcej informacji na ten temat, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/21615)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="d1d98-135">For more information on the preceding, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21615).</span></span>

<span data-ttu-id="d1d98-136">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-136">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="d1d98-137">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="d1d98-137">The default settings are:</span></span>

* <span data-ttu-id="d1d98-138">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="d1d98-138">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="d1d98-139">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="d1d98-139">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="d1d98-140">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-140">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="d1d98-141">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-141">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="d1d98-142">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-142">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="d1d98-143">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="d1d98-143">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="d1d98-144">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="d1d98-144">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="d1d98-145">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1d98-145">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="d1d98-146">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1d98-146">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="d1d98-147">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="d1d98-147">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="d1d98-148">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d1d98-148">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="d1d98-149">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d1d98-149">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d1d98-150">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="d1d98-150">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d1d98-151">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="d1d98-151">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="d1d98-152">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-152">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="d1d98-153">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d1d98-153">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="d1d98-154">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-154">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="d1d98-155">Przekierowane nagłówki — zamówienie oprogramowania</span><span class="sxs-lookup"><span data-stu-id="d1d98-155">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="d1d98-156">Przekierowane nagłówki oprogramowanie pośredniczące powinno być uruchamiane przed innym programem pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="d1d98-156">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="d1d98-157">Takie porządkowanie zapewnia, że oprogramowanie pośredniczące polegające na informacjach o przekazanych nagłówkach może zużywać wartości nagłówka do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="d1d98-157">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="d1d98-158">Przekierowane nagłówki oprogramowanie pośredniczące może zostać uruchomione po obsłudze diagnostyki i błędów, ale należy je uruchomić przed wywołaniem `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="d1d98-158">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="d1d98-159">Alternatywnie Wywołaj `UseForwardedHeaders` przed diagnostyką:</span><span class="sxs-lookup"><span data-stu-id="d1d98-159">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="d1d98-160">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d1d98-160">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="d1d98-161"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-161">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="d1d98-162">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="d1d98-162">Nginx configuration</span></span>

<span data-ttu-id="d1d98-163">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-163">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="d1d98-164">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="d1d98-164">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="d1d98-165">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="d1d98-165">Apache configuration</span></span>

<span data-ttu-id="d1d98-166">`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="d1d98-166">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="d1d98-167">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-167">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="d1d98-168">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="d1d98-168">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="d1d98-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="d1d98-169"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="d1d98-170">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="d1d98-170">The following example changes the default values:</span></span>

* <span data-ttu-id="d1d98-171">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-171">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="d1d98-172">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-172">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="d1d98-173">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-173">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="d1d98-174">Opcja</span><span class="sxs-lookup"><span data-stu-id="d1d98-174">Option</span></span> | <span data-ttu-id="d1d98-175">Opis</span><span class="sxs-lookup"><span data-stu-id="d1d98-175">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="d1d98-176">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="d1d98-176">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="d1d98-177">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="d1d98-177">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="d1d98-178">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="d1d98-178">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="d1d98-179">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="d1d98-179">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="d1d98-180">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="d1d98-180">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="d1d98-181">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="d1d98-181">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="d1d98-182">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-182">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="d1d98-183">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-183">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="d1d98-184">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-184">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="d1d98-185">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="d1d98-185">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="d1d98-186">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="d1d98-186">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="d1d98-187">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-187">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="d1d98-188">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-188">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="d1d98-189">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-189">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d1d98-190">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-190">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="d1d98-191">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-191">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="d1d98-192">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="d1d98-192">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="d1d98-193">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-193">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="d1d98-194">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d1d98-194">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="d1d98-195">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-195">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="d1d98-196">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-196">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d1d98-197">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-197">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="d1d98-198">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-198">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="d1d98-199">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-199">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d1d98-200">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-200">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="d1d98-201">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-201">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="d1d98-202">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-202">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="d1d98-203">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="d1d98-203">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="d1d98-204">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-204">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d1d98-205">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="d1d98-205">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="d1d98-206">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-206">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="d1d98-207">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="d1d98-207">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="d1d98-208">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="d1d98-208">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="d1d98-209">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-209">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d1d98-210">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-210">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d1d98-211">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-211">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="d1d98-212">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="d1d98-213">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="d1d98-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="d1d98-214">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="d1d98-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="d1d98-215">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d1d98-216">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d1d98-217">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="d1d98-218">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-218">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="d1d98-219">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-219">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="d1d98-220">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-220">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="d1d98-221">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-221">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="d1d98-222">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-222">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="d1d98-223">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-223">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="d1d98-224">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-224">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="d1d98-225">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="d1d98-225">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="d1d98-226">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-226">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="d1d98-227">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-227">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="d1d98-228">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="d1d98-228">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="d1d98-229">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="d1d98-229">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="d1d98-230">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-230">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="d1d98-231">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d1d98-231">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="d1d98-232">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="d1d98-232">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="d1d98-233">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="d1d98-233">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="d1d98-234">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="d1d98-234">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="d1d98-235">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="d1d98-235">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="d1d98-236">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="d1d98-236">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="d1d98-237">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="d1d98-237">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="d1d98-238">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-238">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="d1d98-239">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="d1d98-239">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="d1d98-240">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="d1d98-240">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="d1d98-241">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="d1d98-241">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="d1d98-242">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="d1d98-242">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="d1d98-243">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="d1d98-243">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="d1d98-244">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="d1d98-244">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="d1d98-245">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-245">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="d1d98-246">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-246">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="d1d98-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="d1d98-247"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="d1d98-248">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="d1d98-248">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="d1d98-249">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="d1d98-249">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="d1d98-250">Przekazywanie certyfikatów</span><span class="sxs-lookup"><span data-stu-id="d1d98-250">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="d1d98-251">Azure</span><span class="sxs-lookup"><span data-stu-id="d1d98-251">Azure</span></span>

<span data-ttu-id="d1d98-252">Aby skonfigurować Azure App Service na potrzeby przekazywania certyfikatów, zobacz [Konfigurowanie uwierzytelniania wzajemnego TLS dla Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="d1d98-252">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="d1d98-253">Poniższe wskazówki dotyczą konfigurowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1d98-253">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="d1d98-254">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="d1d98-254">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="d1d98-255">Skonfiguruj oprogramowanie pośredniczące do przekazywania certyfikatów, aby określić nazwę nagłówka używaną przez platformę Azure.</span><span class="sxs-lookup"><span data-stu-id="d1d98-255">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="d1d98-256">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="d1d98-256">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="d1d98-257">Inne serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="d1d98-257">Other web proxies</span></span>

<span data-ttu-id="d1d98-258">Jeśli używany jest serwer proxy, który nie jest programem IIS lub Azure App Service routingu żądań aplikacji (ARR), skonfiguruj serwer proxy do przesyłania dalej certyfikatu otrzymanego w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1d98-258">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="d1d98-259">W programie `Startup.Configure` Dodaj następujący kod przed wywołaniem do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="d1d98-259">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="d1d98-260">Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej certyfikatów, aby określić nazwę nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-260">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="d1d98-261">W programie `Startup.ConfigureServices` Dodaj następujący kod, aby skonfigurować nagłówek, z którego oprogramowanie pośredniczące kompiluje certyfikat:</span><span class="sxs-lookup"><span data-stu-id="d1d98-261">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="d1d98-262">Jeśli serwer proxy nie może zakodować certyfikatu Base64 (podobnie jak w przypadku Nginx), ustaw `HeaderConverter` opcję.</span><span class="sxs-lookup"><span data-stu-id="d1d98-262">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="d1d98-263">Rozważmy następujący przykład w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d1d98-263">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="d1d98-264">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="d1d98-264">Troubleshoot</span></span>

<span data-ttu-id="d1d98-265">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d1d98-265">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d1d98-266">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="d1d98-266">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="d1d98-267">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="d1d98-267">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="d1d98-268">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d1d98-268">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="d1d98-269">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d1d98-269">You can write to logs instead of the response body.</span></span> <span data-ttu-id="d1d98-270">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-270">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="d1d98-271">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="d1d98-271">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="d1d98-272">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d1d98-272">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="d1d98-273">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-273">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1d98-274">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-274">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="d1d98-275">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-275">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d1d98-276">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="d1d98-276">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="d1d98-277">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="d1d98-277">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="d1d98-278">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-278">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="d1d98-279">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="d1d98-279">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="d1d98-280">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-280">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="d1d98-281">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-281">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1d98-282">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="d1d98-282">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="d1d98-283">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="d1d98-283">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="d1d98-284">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="d1d98-284">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1d98-285">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="d1d98-285">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="d1d98-286">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="d1d98-286">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1d98-287">W zalecanej konfiguracji dla ASP.NET Core aplikacja jest hostowana przy użyciu usług IIS/ASP. NET Core Module, Nginx lub Apache.</span><span class="sxs-lookup"><span data-stu-id="d1d98-287">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="d1d98-288">Serwery proxy, moduły równoważenia obciążenia i inne urządzenia sieciowe często zasłaniają informacje o żądaniu przed jego przekazaniem do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d1d98-288">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="d1d98-289">Gdy żądania HTTPS są przekazywane za pośrednictwem protokołu HTTP, oryginalny schemat (HTTPS) zostanie utracony i musi zostać przesłany dalej w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="d1d98-289">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="d1d98-290">Ponieważ aplikacja odbiera żądanie od serwera proxy, a nie jego prawdziwe źródło w Internecie lub sieci firmowej, adres IP inicjującego klienta musi być również przekazywany w nagłówku.</span><span class="sxs-lookup"><span data-stu-id="d1d98-290">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="d1d98-291">Te informacje mogą być ważne w przetwarzaniu żądań, na przykład w przekierowaniach, uwierzytelnianiu, generowaniu łączy, ocenie zasad i geolokalizacji klienta.</span><span class="sxs-lookup"><span data-stu-id="d1d98-291">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="d1d98-292">Nagłówki przesłane dalej</span><span class="sxs-lookup"><span data-stu-id="d1d98-292">Forwarded headers</span></span>

<span data-ttu-id="d1d98-293">Zgodnie z Konwencją serwery proxy przesyłają dalej informacje w nagłówkach HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1d98-293">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="d1d98-294">Nagłówek</span><span class="sxs-lookup"><span data-stu-id="d1d98-294">Header</span></span> | <span data-ttu-id="d1d98-295">Opis</span><span class="sxs-lookup"><span data-stu-id="d1d98-295">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="d1d98-296">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="d1d98-296">X-Forwarded-For</span></span> | <span data-ttu-id="d1d98-297">Zawiera informacje o kliencie, który zainicjował żądanie i kolejne serwery proxy w łańcuchu serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-297">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="d1d98-298">Ten parametr może zawierać adresy IP (i opcjonalnie numery portów).</span><span class="sxs-lookup"><span data-stu-id="d1d98-298">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="d1d98-299">W łańcuchu serwerów proxy pierwszy parametr wskazuje klienta, na którym żądanie zostało po raz pierwszy wykonane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-299">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="d1d98-300">Kolejne identyfikatory serwerów proxy są następujące.</span><span class="sxs-lookup"><span data-stu-id="d1d98-300">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="d1d98-301">Ostatni serwer proxy w łańcuchu nie znajduje się na liście parametrów.</span><span class="sxs-lookup"><span data-stu-id="d1d98-301">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="d1d98-302">Adres IP ostatniego serwera proxy i opcjonalnie numer portu są dostępne jako zdalny adres IP w warstwie transportowej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-302">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="d1d98-303">X-Forwarded-proto</span><span class="sxs-lookup"><span data-stu-id="d1d98-303">X-Forwarded-Proto</span></span> | <span data-ttu-id="d1d98-304">Wartość schematu inicjującego (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d1d98-304">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="d1d98-305">Ta wartość może być również listą schematów, jeśli żądanie przełączyło wiele serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-305">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="d1d98-306">X-Forward-Host</span><span class="sxs-lookup"><span data-stu-id="d1d98-306">X-Forwarded-Host</span></span> | <span data-ttu-id="d1d98-307">Oryginalna wartość pola nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="d1d98-307">The original value of the Host header field.</span></span> <span data-ttu-id="d1d98-308">Zazwyczaj proxy nie modyfikują nagłówka hosta.</span><span class="sxs-lookup"><span data-stu-id="d1d98-308">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="d1d98-309">Zobacz [Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) , aby uzyskać informacje na temat luki w zabezpieczeniach dotyczącej podniesienia uprawnień, która ma wpływ na systemy, w których serwer proxy nie weryfikuje ani nie ogranicza nagłówków hosta do znanych prawidłowych wartości.</span><span class="sxs-lookup"><span data-stu-id="d1d98-309">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="d1d98-310">Przekazane nagłówki — oprogramowanie pośredniczące, z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) , odczytuje te nagłówki i wypełnia pola skojarzone w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-310">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="d1d98-311">Aktualizacje oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d1d98-311">The middleware updates:</span></span>

* <span data-ttu-id="d1d98-312">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Ustaw za pomocą `X-Forwarded-For` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-312">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="d1d98-313">Ustawienia dodatkowe mają wpływ na sposób tworzenia oprogramowania pośredniczącego `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-313">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="d1d98-314">Aby uzyskać szczegółowe informacje, zobacz [Opcje oprogramowania pośredniczącego z przekazanymi nagłówkami](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d1d98-314">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="d1d98-315">[HttpContext. Request. Schema](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Ustaw za pomocą `X-Forwarded-Proto` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-315">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="d1d98-316">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Ustaw za pomocą `X-Forwarded-Host` wartości nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d1d98-316">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="d1d98-317">Można skonfigurować przekierowane nagłówki — [domyślne ustawienia](#forwarded-headers-middleware-options) oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-317">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="d1d98-318">Ustawienia domyślne to:</span><span class="sxs-lookup"><span data-stu-id="d1d98-318">The default settings are:</span></span>

* <span data-ttu-id="d1d98-319">Między aplikacją i źródłem żądań istnieje tylko *jeden serwer proxy* .</span><span class="sxs-lookup"><span data-stu-id="d1d98-319">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="d1d98-320">Tylko adresy sprzężenia zwrotnego są konfigurowane dla znanych serwerów proxy i znanych sieci.</span><span class="sxs-lookup"><span data-stu-id="d1d98-320">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="d1d98-321">Przekazane nagłówki mają nazwę `X-Forwarded-For` i `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-321">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="d1d98-322">Nie wszystkie urządzenia sieciowe Dodaj `X-Forwarded-For` nagłówki i `X-Forwarded-Proto` bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-322">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="d1d98-323">Zapoznaj się ze wskazówkami producenta urządzenia, jeśli żądania proxy nie zawierają tych nagłówków podczas uzyskiwania dostępu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-323">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="d1d98-324">Jeśli urządzenie używa innych nazw nagłówka niż `X-Forwarded-For` i `X-Forwarded-Proto` , ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez urządzenie.</span><span class="sxs-lookup"><span data-stu-id="d1d98-324">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="d1d98-325">Aby uzyskać więcej informacji, zobacz Opcje i konfiguracja [przekierowanych nagłówków](#forwarded-headers-middleware-options) [dla serwera proxy, który używa innych nazw nagłówków](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="d1d98-325">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="d1d98-326">Usługi IIS/IIS Express i moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1d98-326">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="d1d98-327">Przekazane nagłówki — oprogramowanie pośredniczące jest domyślnie włączone przez [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , gdy aplikacja jest hostowana [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model) usług iis i modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1d98-327">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="d1d98-328">Przekierowane nagłówki oprogramowania są aktywowane do uruchomienia najpierw w potoku pośredniczącym z ograniczoną konfiguracją specyficzną dla modułu ASP.NET Core z powodu obaw zaufania z przekierowanymi nagłówkami (na przykład [fałszowanie adresów IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="d1d98-328">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="d1d98-329">Oprogramowanie pośredniczące jest skonfigurowane do przesyłania dalej `X-Forwarded-For` nagłówków i i `X-Forwarded-Proto` jest ograniczone do jednego serwera proxy hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d1d98-329">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="d1d98-330">Jeśli wymagana jest dodatkowa konfiguracja, zapoznaj się z [opcjami przekierowanych nagłówków](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d1d98-330">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d1d98-331">Inne scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="d1d98-331">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d1d98-332">Poza użyciem [integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), przekazane nagłówki nie są domyślnie włączone.</span><span class="sxs-lookup"><span data-stu-id="d1d98-332">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="d1d98-333">Przesyłane nagłówki muszą być włączone, aby aplikacja mogła przetworzyć przekazane nagłówki za pomocą <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-333">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="d1d98-334">Po włączeniu oprogramowania pośredniczącego, jeśli nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zostanie określony do oprogramowania pośredniczącego, domyślnie [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d1d98-334">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="d1d98-335">Skonfiguruj oprogramowanie pośredniczące w programie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-335">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1d98-336">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d1d98-336">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="d1d98-337">Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona w `Startup.ConfigureServices` lub bezpośrednio do metody rozszerzenia z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , domyślne nagłówki do przodu to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d1d98-337">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="d1d98-338"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Właściwość musi być skonfigurowana z nagłówkami do przesyłania dalej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-338">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="d1d98-339">Konfiguracja Nginx</span><span class="sxs-lookup"><span data-stu-id="d1d98-339">Nginx configuration</span></span>

<span data-ttu-id="d1d98-340">Aby przesłać dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i, zobacz <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-340">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="d1d98-341">Aby uzyskać więcej informacji, zobacz [Nginx: Using](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)The Forwarded header.</span><span class="sxs-lookup"><span data-stu-id="d1d98-341">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="d1d98-342">Konfiguracja Apache</span><span class="sxs-lookup"><span data-stu-id="d1d98-342">Apache configuration</span></span>

<span data-ttu-id="d1d98-343">`X-Forwarded-For` jest automatycznie dodawany (zobacz [Apache Module mod_proxy: nagłówki żądań zwrotnego serwera proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="d1d98-343">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="d1d98-344">Aby uzyskać informacje na temat przekazywania `X-Forwarded-Proto` nagłówka, zobacz <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-344">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="d1d98-345">Przekazane nagłówki — Opcje oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="d1d98-345">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="d1d98-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> Steruj zachowaniem przekierowanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="d1d98-346"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="d1d98-347">Poniższy przykład zmienia wartości domyślne:</span><span class="sxs-lookup"><span data-stu-id="d1d98-347">The following example changes the default values:</span></span>

* <span data-ttu-id="d1d98-348">Ogranicz liczbę wpisów w nagłówkach przesyłanych dalej do `2` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-348">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="d1d98-349">Dodaj znany adres serwera proxy `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-349">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="d1d98-350">Zmień nazwę przekazanego nagłówka z domyślnej `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-350">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="d1d98-351">Opcja</span><span class="sxs-lookup"><span data-stu-id="d1d98-351">Option</span></span> | <span data-ttu-id="d1d98-352">Opis</span><span class="sxs-lookup"><span data-stu-id="d1d98-352">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="d1d98-353">Ogranicza hosty według `X-Forwarded-Host` nagłówka do dostarczonych wartości.</span><span class="sxs-lookup"><span data-stu-id="d1d98-353">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="d1d98-354">Wartości są porównywane przy użyciu liczby porządkowej-ignorowanie wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="d1d98-354">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="d1d98-355">Numery portów muszą być wykluczone.</span><span class="sxs-lookup"><span data-stu-id="d1d98-355">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="d1d98-356">Jeśli lista jest pusta, dozwolone są wszystkie hosty.</span><span class="sxs-lookup"><span data-stu-id="d1d98-356">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="d1d98-357">Symbol wieloznaczny najwyższego poziomu `*` zezwala na wszystkie niepuste hosty.</span><span class="sxs-lookup"><span data-stu-id="d1d98-357">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="d1d98-358">Symbole wielodomenowe są dozwolone, ale nie są zgodne z domeną główną.</span><span class="sxs-lookup"><span data-stu-id="d1d98-358">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="d1d98-359">Na przykład `*.contoso.com` pasuje do domeny podrzędnej, `foo.contoso.com` ale nie do domeny głównej `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-359">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="d1d98-360">Nazwy hostów Unicode są dozwolone, ale są konwertowane na [formacie Punycode](https://tools.ietf.org/html/rfc3492) w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-360">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="d1d98-361">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) muszą zawierać nawiasy ograniczające i być w [formacie konwencjonalnym](https://tools.ietf.org/html/rfc4291#section-2.2) (na przykład `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-361">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="d1d98-362">Adresy IPv6 nie są specjalne, aby sprawdzać równość logiczną między różnymi formatami i nie są wykonywane żadne znaki.</span><span class="sxs-lookup"><span data-stu-id="d1d98-362">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="d1d98-363">Nieograniczenie dozwolonych hostów może pozwolić atakującemu na sfałszowanie linków wygenerowanych przez usługę.</span><span class="sxs-lookup"><span data-stu-id="d1d98-363">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="d1d98-364">Wartość domyślna jest pusta `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-364">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="d1d98-365">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-365">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="d1d98-366">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-For` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-366">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d1d98-367">Wartość domyślna to `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-367">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="d1d98-368">Określa, które usługi przesyłania dalej powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-368">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="d1d98-369">Zobacz [Wyliczenie ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) dla listy pól, które mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="d1d98-369">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="d1d98-370">Typowe wartości przypisane do tej właściwości to `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-370">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="d1d98-371">Wartość domyślna to [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d1d98-371">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="d1d98-372">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="d1d98-373">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Host` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d1d98-374">Wartość domyślna to `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-374">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="d1d98-375">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-375">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="d1d98-376">Ta opcja jest używana, gdy serwer proxy/usługa przesyłania dalej nie używa `X-Forwarded-Proto` nagłówka, ale używa innego nagłówka do przekazywania informacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-376">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d1d98-377">Wartość domyślna to `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-377">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="d1d98-378">Ogranicza liczbę wpisów w nagłówkach, które są przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-378">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="d1d98-379">Ustaw `null` , aby wyłączyć limit, ale należy to zrobić tylko wtedy, gdy `KnownProxies` lub `KnownNetworks` są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="d1d98-379">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="d1d98-380">Ustawienie wartości innej niż `null` wartość jest środkiem (ale nie gwarancją) do ochrony przed błędami skonfigurowanymi serwerami proxy i złośliwymi żądaniami przychodzącymi z kanałów bocznych w sieci.</span><span class="sxs-lookup"><span data-stu-id="d1d98-380">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="d1d98-381">Przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-381">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d1d98-382">Jeśli `1` zostanie użyta wartość domyślna (), tylko wartość po prawej stronie nagłówka jest przetwarzana, chyba że `ForwardLimit` zostanie zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="d1d98-382">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="d1d98-383">Wartość domyślna to `1`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-383">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="d1d98-384">Zakresy adresów znanych sieci, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="d1d98-384">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="d1d98-385">Podaj zakresy adresów IP przy użyciu notacji międzydomenowego routingu bezklasowego (CIDR).</span><span class="sxs-lookup"><span data-stu-id="d1d98-385">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="d1d98-386">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-386">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d1d98-387">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-387">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d1d98-388">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-388">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="d1d98-389">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="d1d98-389">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="d1d98-390">Wartość domyślna to `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> zawierający pojedynczy wpis dla `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-390">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="d1d98-391">Adresy znanych serwerów proxy, z których mają być akceptowane przekazane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="d1d98-391">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="d1d98-392">Użyj `KnownProxies` , aby określić dokładne dopasowania adresów IP.</span><span class="sxs-lookup"><span data-stu-id="d1d98-392">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="d1d98-393">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d1d98-394">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d1d98-395">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="d1d98-396">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="d1d98-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="d1d98-397">Wartość domyślna to `IList` \<<xref:System.Net.IPAddress>> zawierający pojedynczy wpis dla `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-397">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="d1d98-398">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-398">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="d1d98-399">Wartość domyślna to `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-399">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="d1d98-400">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-400">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="d1d98-401">Wartość domyślna to `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-401">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="d1d98-402">Użyj nagłówka określonego przez tę właściwość zamiast elementu określonego przez [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d1d98-402">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="d1d98-403">Wartość domyślna to `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d1d98-403">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="d1d98-404">Wymagaj synchronizacji wartości nagłówka między przetworzonym [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="d1d98-404">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="d1d98-405">Wartość domyślna w ASP.NET Core 1. x to `true` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-405">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="d1d98-406">Wartość domyślna w ASP.NET Core 2,0 lub nowszej ma wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-406">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="d1d98-407">Scenariusze i przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="d1d98-407">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="d1d98-408">Gdy nie można dodać przekierowanych nagłówków i wszystkie żądania są bezpieczne</span><span class="sxs-lookup"><span data-stu-id="d1d98-408">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="d1d98-409">W niektórych przypadkach może być niemożliwe dodanie przekierowanych nagłówków do żądań wysyłanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d1d98-409">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="d1d98-410">Jeśli serwer proxy wymusza, że wszystkie publiczne żądania zewnętrzne są HTTPS, schemat można ustawić ręcznie `Startup.Configure` przed użyciem dowolnego typu oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="d1d98-410">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="d1d98-411">Ten kod można wyłączyć za pomocą zmiennej środowiskowej lub innego ustawienia konfiguracji w środowisku programistycznym lub przejściowym.</span><span class="sxs-lookup"><span data-stu-id="d1d98-411">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="d1d98-412">Postępowanie z ścieżką bazową i serwerami proxy, które zmieniają ścieżkę żądania</span><span class="sxs-lookup"><span data-stu-id="d1d98-412">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="d1d98-413">Niektóre serwery proxy przechodzą ze ścieżki bez zmian, ale z ścieżką bazową aplikacji, która powinna zostać usunięta, aby Routing działał prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="d1d98-413">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="d1d98-414">Oprogramowanie pośredniczące [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) dzieli ścieżkę na [HttpRequest. Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) i ścieżkę bazową aplikacji na [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="d1d98-414">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="d1d98-415">Jeśli `/foo` jest ścieżką bazową aplikacji dla ścieżki serwera proxy przekazaną jako `/foo/api/1` , oprogramowanie pośredniczące ustawia `Request.PathBase` do `/foo` i `Request.Path` `/api/1` przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="d1d98-415">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="d1d98-416">Oryginalna ścieżka i baza ścieżki są ponownie stosowane, gdy oprogramowanie pośredniczące zostanie wywołane w odwrocie.</span><span class="sxs-lookup"><span data-stu-id="d1d98-416">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="d1d98-417">Aby uzyskać więcej informacji na temat przetwarzania zamówień oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="d1d98-417">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="d1d98-418">Jeśli serwer proxy przycinanie ścieżki (na przykład przekazywania `/foo/api/1` do `/api/1` ), napraw przekierowania i linki przez ustawienie właściwości [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) żądania:</span><span class="sxs-lookup"><span data-stu-id="d1d98-418">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="d1d98-419">Jeśli serwer proxy dodaje dane ścieżki, należy odrzucić część ścieżki, aby naprawić przekierowania i linki przy użyciu <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> i przypisywać do <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="d1d98-419">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="d1d98-420">Konfiguracja serwera proxy, który używa innych nazw nagłówków</span><span class="sxs-lookup"><span data-stu-id="d1d98-420">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="d1d98-421">Jeśli serwer proxy nie używa nagłówków o nazwach `X-Forwarded-For` i `X-Forwarded-Proto` do przesyłania dalej informacji o adresie/porcie i źródłowym serwerze proxy, ustaw <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> Opcje i tak, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> aby były zgodne z nazwami nagłówków używanymi przez serwer proxy:</span><span class="sxs-lookup"><span data-stu-id="d1d98-421">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="d1d98-422">Konfiguracja adresu IPv4 reprezentowanego jako adres IPv6</span><span class="sxs-lookup"><span data-stu-id="d1d98-422">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="d1d98-423">Jeśli serwer używa gniazd z dwoma trybami, adresy IPv4 są dostarczane w formacie IPv6 (na przykład `10.0.0.1` w protokole IPv4 przedstawionym w protokole IPv6 jako `::ffff:10.0.0.1` lub `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-423">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="d1d98-424">Zobacz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-424">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d1d98-425">Sprawdź, czy ten format jest wymagany, przeglądając element [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d1d98-425">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="d1d98-426">W poniższym przykładzie adres sieciowy, który dostarcza przekazane nagłówki, jest dodawany do `KnownNetworks` listy w formacie IPv6.</span><span class="sxs-lookup"><span data-stu-id="d1d98-426">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="d1d98-427">Adres IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="d1d98-427">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="d1d98-428">Przekonwertowany adres IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="d1d98-428">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="d1d98-429">Konwertowana długość prefiksu: 104</span><span class="sxs-lookup"><span data-stu-id="d1d98-429">Converted prefix length: 104</span></span>

<span data-ttu-id="d1d98-430">Możesz również podać adres w formacie szesnastkowym ( `10.11.12.1` reprezentowanym w protokole IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="d1d98-430">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="d1d98-431">Podczas konwertowania adresu IPv4 na IPv6 Dodaj 96 do długości prefiksu CIDR ( `8` w przykładzie), aby uwzględnić dodatkowy `::ffff:` prefiks IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="d1d98-431">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="d1d98-432">Prześlij dalej schemat dla serwera proxy zwrotnego z systemem Linux i innym niż IIS</span><span class="sxs-lookup"><span data-stu-id="d1d98-432">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="d1d98-433">Aplikacje, które wywołują <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> i <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umieszczają lokację w nieskończoną pętlę, jeśli są wdrażane w ramach systemu azure Linux App Service, maszyny wirtualnej systemu Linux (VM) lub za jakimkolwiek innym zwrotnym serwerem proxy poza usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="d1d98-433">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="d1d98-434">Protokół TLS jest zakończony przez zwrotny serwer proxy, a Kestrel nie wie o poprawnym schemacie żądania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-434">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="d1d98-435">Uwierzytelnianie OAuth i OIDC w tej konfiguracji również nie powiedzie się, ponieważ generują one nieprawidłowe przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-435">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="d1d98-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> dodaje i konfiguruje przekierowane nagłówki oprogramowania pośredniczącego, gdy działa za usługami IIS, ale nie ma żadnej zgodnej konfiguracji automatycznej dla systemu Linux (Integracja Apache lub nginx).</span><span class="sxs-lookup"><span data-stu-id="d1d98-436"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="d1d98-437">Aby przesłać dalej schemat z serwera proxy w scenariuszach innych niż usługi IIS, Dodaj i skonfiguruj przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="d1d98-437">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="d1d98-438">W programie `Startup.ConfigureServices` Użyj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="d1d98-438">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="d1d98-439">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="d1d98-439">Troubleshoot</span></span>

<span data-ttu-id="d1d98-440">Gdy nagłówki nie są przekazywane zgodnie z oczekiwaniami, należy włączyć [Rejestrowanie](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d1d98-440">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d1d98-441">Jeśli dzienniki nie zapewniają wystarczających informacji, aby rozwiązać problem, należy wyliczyć nagłówki żądań odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="d1d98-441">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="d1d98-442">Użyj wbudowanego oprogramowania pośredniczącego w celu zapisania nagłówków żądań w odpowiedzi aplikacji lub zarejestrowania nagłówków.</span><span class="sxs-lookup"><span data-stu-id="d1d98-442">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="d1d98-443">Aby zapisać nagłówki w odpowiedzi aplikacji, umieść następujące oprogramowanie pośredniczące w tekście terminalu bezpośrednio po wywołaniu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d1d98-443">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="d1d98-444">Możesz pisać do dzienników zamiast treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d1d98-444">You can write to logs instead of the response body.</span></span> <span data-ttu-id="d1d98-445">Zapis w dziennikach umożliwia normalne działanie lokacji podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="d1d98-445">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="d1d98-446">Do zapisu dzienników, a nie do treści odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="d1d98-446">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="d1d98-447">Wsuń `ILogger<Startup>` do `Startup` klasy zgodnie z opisem w temacie [Tworzenie dzienników w programie startowym](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d1d98-447">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="d1d98-448">Umieść następujące wbudowane oprogramowanie pośredniczące bezpośrednio po wywołaniu w usłudze <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-448">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1d98-449">Podczas przetwarzania `X-Forwarded-{For|Proto|Host}` wartości są przenoszone do `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-449">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="d1d98-450">Jeśli w danym nagłówku znajduje się wiele wartości, przekierowane nagłówki przetwarzają nagłówki w odwrotnej kolejności od prawej do lewej.</span><span class="sxs-lookup"><span data-stu-id="d1d98-450">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d1d98-451">Wartość domyślna `ForwardLimit` to `1` (jeden), więc przetwarzana jest tylko pierwsza od prawej wartości z nagłówków, chyba że zostanie `ForwardLimit` zwiększona wartość.</span><span class="sxs-lookup"><span data-stu-id="d1d98-451">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="d1d98-452">Oryginalny zdalny adres IP żądania musi być zgodny z wpisem na `KnownProxies` `KnownNetworks` listach lub przed przetworzeniem przekazanych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="d1d98-452">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="d1d98-453">To ogranicza fałszowanie nagłówków przez nieakceptowanie usług przesyłania dalej z niezaufanych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-453">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="d1d98-454">Po wykryciu nieznanego serwera proxy rejestrowanie wskazuje adres serwera proxy:</span><span class="sxs-lookup"><span data-stu-id="d1d98-454">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="d1d98-455">W poprzednim przykładzie 10.0.0.100 jest serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="d1d98-455">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="d1d98-456">Jeśli serwer jest zaufanym serwerem proxy, Dodaj adres IP serwera do `KnownProxies` (lub Dodaj zaufaną sieć do `KnownNetworks` ) w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d1d98-456">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1d98-457">Aby uzyskać więcej informacji, zobacz sekcję [Opcje oprogramowania pośredniczącego z przekazaniem nagłówków](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="d1d98-457">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="d1d98-458">Zezwalanie na nagłówki tylko zaufanym serwerom proxy i sieciom.</span><span class="sxs-lookup"><span data-stu-id="d1d98-458">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="d1d98-459">W przeciwnym razie ataki [metodą fałszowania adresów IP](https://www.iplocation.net/ip-spoofing) są możliwe.</span><span class="sxs-lookup"><span data-stu-id="d1d98-459">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1d98-460">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="d1d98-460">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="d1d98-461">Poradnik zabezpieczeń firmy Microsoft CVE-2018-0787: ASP.NET Core podniesienia poziomu uprawnień</span><span class="sxs-lookup"><span data-stu-id="d1d98-461">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
