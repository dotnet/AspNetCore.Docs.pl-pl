---
title: Implementacje serwera sieci Web w ASP.NET Core
author: rick-anderson
description: Odkryj serwery sieci Web Kestrel i HTTP.sys ASP.NET Core. Dowiedz się, jak wybrać serwer i kiedy używać serwera zwrotnego serwera proxy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/servers/index
ms.openlocfilehash: a27fdd70963830d22b3501972d6150dde5e1ea54
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059510"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="5e1bf-104">Implementacje serwera sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e1bf-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="5e1bf-105">Autorzy [Dykstra](https://github.com/tdykstra), [Steve Kowalski](https://ardalis.com/), [Stephen](https://twitter.com/halter73), i [Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="5e1bf-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="5e1bf-106">Aplikacja ASP.NET Core jest uruchamiana z użyciem implementacji serwera HTTP w procesie.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="5e1bf-107">Implementacja serwera nasłuchuje żądań HTTP i umieszcza je w aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="5e1bf-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5e1bf-108">Kestrel</span></span>

<span data-ttu-id="5e1bf-109">Kestrel to domyślny serwer sieci Web określony przez szablony projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="5e1bf-110">Użyj Kestrel:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-110">Use Kestrel:</span></span>

* <span data-ttu-id="5e1bf-111">Jako serwer graniczny przetwarza żądania bezpośrednio z sieci, w tym z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="5e1bf-113">*Serwer proxy zwrotnego* , taki jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-113">With a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="5e1bf-114">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="5e1bf-116">&mdash;Obsługiwana jest konfiguracja hostingu z serwerem zwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="5e1bf-117">Aby uzyskać wskazówki dotyczące konfiguracji Kestrel i informacje o tym, kiedy używać Kestrel w konfiguracji zwrotnego serwera proxy, zobacz <xref:fundamentals/servers/kestrel> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="5e1bf-118">Windows</span><span class="sxs-lookup"><span data-stu-id="5e1bf-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="5e1bf-119">ASP.NET Core dostarcza następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="5e1bf-120">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="5e1bf-121">Serwer HTTP usług IIS jest [serwerem w procesie](#hosting-models) dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="5e1bf-122">[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="5e1bf-123">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)uruchomiona jest aplikacja:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="5e1bf-124">W tym samym procesie co proces roboczy usług IIS ( [model hostingu w procesie](#hosting-models)) z serwerem HTTP usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="5e1bf-125">*W procesie* jest zalecana konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="5e1bf-126">W procesie innym niż proces roboczy usług IIS ( [model hostingu poza procesem](#hosting-models)) z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="5e1bf-127">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest natywnym modułem usług IIS, który obsługuje natywne żądania usług IIS między usługami IIS a serwerem HTTP lub Kestrel IIS w procesie.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="5e1bf-128">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5e1bf-129">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="5e1bf-129">Hosting models</span></span>

<span data-ttu-id="5e1bf-130">Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="5e1bf-131">Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="5e1bf-132">Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5e1bf-133">Korzystając z hostingu poza procesem, ASP.NET Core aplikacje są uruchamiane w procesie oddzielonym od procesu roboczego usług IIS, a moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="5e1bf-134">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5e1bf-135">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5e1bf-136">Aby uzyskać więcej informacji i wskazówki dotyczące konfiguracji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="5e1bf-137">macOS</span><span class="sxs-lookup"><span data-stu-id="5e1bf-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="5e1bf-138">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="5e1bf-139">Linux</span><span class="sxs-lookup"><span data-stu-id="5e1bf-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="5e1bf-140">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="5e1bf-141">Windows</span><span class="sxs-lookup"><span data-stu-id="5e1bf-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="5e1bf-142">ASP.NET Core dostarcza następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="5e1bf-143">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="5e1bf-144">[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="5e1bf-145">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja działa w procesie innym niż proces roboczy usług IIS ( *poza procesem* ) z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process ( *out-of-process* ) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="5e1bf-146">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="5e1bf-147">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="5e1bf-148">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5e1bf-149">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="5e1bf-151">Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5e1bf-152">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5e1bf-153">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5e1bf-154">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5e1bf-155">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5e1bf-156">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5e1bf-157">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5e1bf-158">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5e1bf-159">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5e1bf-160">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5e1bf-161">Aby uzyskać wskazówki dotyczące konfiguracji modułu usług IIS i ASP.NET Core, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="5e1bf-162">macOS</span><span class="sxs-lookup"><span data-stu-id="5e1bf-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="5e1bf-163">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="5e1bf-164">Linux</span><span class="sxs-lookup"><span data-stu-id="5e1bf-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="5e1bf-165">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="5e1bf-166">Nginx z Kestrel</span><span class="sxs-lookup"><span data-stu-id="5e1bf-166">Nginx with Kestrel</span></span>

<span data-ttu-id="5e1bf-167">Aby uzyskać informacje na temat używania Nginx w systemie Linux jako serwera zwrotnego proxy dla Kestrel, zobacz <xref:host-and-deploy/linux-nginx> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="5e1bf-168">Apache z Kestrel</span><span class="sxs-lookup"><span data-stu-id="5e1bf-168">Apache with Kestrel</span></span>

<span data-ttu-id="5e1bf-169">Aby uzyskać informacje na temat korzystania z usługi Apache w systemie Linux jako serwera zwrotnego proxy dla usługi Kestrel, zobacz <xref:host-and-deploy/linux-apache> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="5e1bf-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="5e1bf-170">HTTP.sys</span></span>

<span data-ttu-id="5e1bf-171">Jeśli ASP.NET Core aplikacje są uruchamiane w systemie Windows, HTTP.sys jest alternatywą dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="5e1bf-172">Kestrel jest zwykle zalecana w celu uzyskania najlepszej wydajności.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="5e1bf-173">HTTP.sys można używać w scenariuszach, w których aplikacja jest narażona na dostęp do Internetu, a wymagane możliwości są obsługiwane przez HTTP.sys ale nie Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="5e1bf-174">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys komunikują się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="5e1bf-176">HTTP.sys można również używać dla aplikacji, które są dostępne tylko w sieci wewnętrznej.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="5e1bf-178">Aby uzyskać HTTP.sys wskazówki dotyczące konfiguracji, zobacz <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="5e1bf-179">Infrastruktura serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e1bf-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="5e1bf-180"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Dostępne w `Startup.Configure` metodzie uwidacznia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> Właściwość typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="5e1bf-181">Kestrel i HTTP.sys uwidaczniają tylko jedną funkcję, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ale różne implementacje serwera mogą uwidaczniać dodatkowe funkcje.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="5e1bf-182">`IServerAddressesFeature` można go użyć, aby dowiedzieć się, który port implementacji serwera w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="5e1bf-183">Serwery niestandardowe</span><span class="sxs-lookup"><span data-stu-id="5e1bf-183">Custom servers</span></span>

<span data-ttu-id="5e1bf-184">Jeśli wbudowane serwery nie spełniają wymagań aplikacji, można utworzyć niestandardową implementację serwera.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="5e1bf-185">W [przewodniku otwierania interfejsu sieci Web dla platformy .NET (Owin)](xref:fundamentals/owin) przedstawiono sposób pisania implementacji opartej na [nowin](https://github.com/Bobris/Nowin) <xref:Microsoft.AspNetCore.Hosting.Server.IServer> .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="5e1bf-186">Tylko interfejsy funkcji używane przez aplikację wymagają implementacji, ale <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> muszą być obsługiwane w minimalnym i <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> wymaganym czasie.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="5e1bf-187">Uruchamianie serwera</span><span class="sxs-lookup"><span data-stu-id="5e1bf-187">Server startup</span></span>

<span data-ttu-id="5e1bf-188">Serwer jest uruchamiany po uruchomieniu aplikacji zintegrowanego środowiska programistycznego (IDE) lub edytora:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="5e1bf-189">[Visual Studio](https://visualstudio.microsoft.com): profile uruchamiania mogą służyć do uruchamiania aplikacji i serwera za pomocą [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) lub konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="5e1bf-190">[Visual Studio Code](https://code.visualstudio.com/): aplikacja i serwer są uruchamiane przez [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), która aktywuje debuger CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="5e1bf-191">[Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/): aplikacja i serwer są uruchamiane za pomocą [debugera Soft-Mode mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="5e1bf-192">Podczas uruchamiania aplikacji z poziomu wiersza polecenia w folderze projektu, [uruchomienie dotnet](/dotnet/core/tools/dotnet-run) uruchamia aplikację i serwer (tylko Kestrel i HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="5e1bf-193">Konfiguracja jest określana przez `-c|--configuration` opcję, która jest ustawiona na wartość `Debug` (domyślnie) lub `Release` .</span><span class="sxs-lookup"><span data-stu-id="5e1bf-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="5e1bf-194">*launchSettings.jsw* pliku zapewnia konfigurację podczas uruchamiania aplikacji przy użyciu `dotnet run` debugera wbudowanego w narzędzia, takiego jak Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="5e1bf-195">Jeśli profile uruchamiania są obecne w *launchSettings.jsw* pliku, użyj `--launch-profile {PROFILE NAME}` opcji z `dotnet run` poleceniem lub wybierz profil w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="5e1bf-196">Aby uzyskać więcej informacji, [dotnet run](/dotnet/core/tools/dotnet-run) zobacz [pakietem rozkładu dotnet i .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="5e1bf-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="5e1bf-197">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5e1bf-197">HTTP/2 support</span></span>

<span data-ttu-id="5e1bf-198">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania:</span><span class="sxs-lookup"><span data-stu-id="5e1bf-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="5e1bf-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5e1bf-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="5e1bf-200">System operacyjny</span><span class="sxs-lookup"><span data-stu-id="5e1bf-200">Operating system</span></span>
    * <span data-ttu-id="5e1bf-201">Windows Server 2016/Windows 10 lub nowszy&dagger;</span><span class="sxs-lookup"><span data-stu-id="5e1bf-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="5e1bf-202">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="5e1bf-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="5e1bf-203">Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="5e1bf-204">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="5e1bf-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="5e1bf-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="5e1bf-206">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="5e1bf-207">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="5e1bf-208">Usługi IIS (w procesie)</span><span class="sxs-lookup"><span data-stu-id="5e1bf-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="5e1bf-209">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5e1bf-210">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="5e1bf-211">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="5e1bf-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="5e1bf-212">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5e1bf-213">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="5e1bf-214">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="5e1bf-215">&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="5e1bf-216">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="5e1bf-217">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="5e1bf-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="5e1bf-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="5e1bf-219">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="5e1bf-220">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="5e1bf-221">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="5e1bf-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="5e1bf-222">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5e1bf-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="5e1bf-223">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="5e1bf-224">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="5e1bf-225">Połączenie HTTP/2 musi korzystać z [negocjacji protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) i TLS 1,2 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="5e1bf-226">Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi scenariuszy wdrażania serwera.</span><span class="sxs-lookup"><span data-stu-id="5e1bf-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e1bf-227">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5e1bf-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
