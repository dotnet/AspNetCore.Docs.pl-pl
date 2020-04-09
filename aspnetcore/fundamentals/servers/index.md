---
title: Implementacje serwera sieci Web w ASP.NET Core
author: rick-anderson
description: Odkryj serwery internetowe Kestrel i HTTP.sys dla ASP.NET Core. Dowiedz się, jak wybrać serwer i kiedy używać odwrotnego serwera proxy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666342"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="d9f0f-104">Implementacje serwera sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f0f-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="d9f0f-105">[Przez Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), Stephen [Halter](https://twitter.com/halter73)i [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="d9f0f-106">Aplikacja ASP.NET Core jest uruchamiana z implementacją serwera HTTP w toku.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="d9f0f-107">Implementacja serwera nasłuchuje żądań HTTP i przedstawia je w aplikacji <xref:Microsoft.AspNetCore.Http.HttpContext>jako zestaw funkcji [żądania](xref:fundamentals/request-features) skomponowanych w pliku .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="d9f0f-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="d9f0f-108">Kestrel</span></span>

<span data-ttu-id="d9f0f-109">Pustułka jest domyślnym serwerem www określonym przez szablony projektów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="d9f0f-110">Użyj Pustułki:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-110">Use Kestrel:</span></span>

* <span data-ttu-id="d9f0f-111">Sam jako serwer brzegowy przetwarza żądania bezpośrednio z sieci, w tym z Internetu.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="d9f0f-113">Z *odwrotnym serwerem proxy,* takim jak [Internetowe usługi informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d9f0f-114">Serwer odwrotnego serwera proxy odbiera żądania HTTP z Internetu i przekazuje je do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d9f0f-116">Obsługiwana jest&mdash;konfiguracja hostingu&mdash;z odwrotnym serwerem proxy lub bez niego.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="d9f0f-117">Aby uzyskać wskazówki dotyczące konfiguracji kestrelu i informacje o <xref:fundamentals/servers/kestrel>tym, kiedy używać Kestrel w odwrotnej konfiguracji serwera proxy, zobacz .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="d9f0f-118">Windows</span><span class="sxs-lookup"><span data-stu-id="d9f0f-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="d9f0f-119">ASP.NET Core jest dostarczany z następującymi elementami:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="d9f0f-120">[Serwer pustułki](xref:fundamentals/servers/kestrel) jest domyślną, wieloplatformową implementacją serwera HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="d9f0f-121">Serwer HTTP usług IIS jest [serwerem w trakcie](#hosting-models) usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="d9f0f-122">[Serwer HTTP.sys](xref:fundamentals/servers/httpsys) jest serwerem HTTP dostępnym tylko dla systemu Windows opartym na [sterowniku jądra HTTP.sys i interfejsie API serwera HTTP.](/windows/desktop/Http/http-api-start-page)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="d9f0f-123">W przypadku korzystania z [usługi IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja jest uruchamiana:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="d9f0f-124">W tym samym procesie co proces roboczy usług IIS [(model hostingu w trakcie)](#hosting-models)z serwerem HTTP usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="d9f0f-125">*W procesie* jest zalecana konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="d9f0f-126">W procesie oddzielonym od procesu roboczego usług IIS [(model hostingu poza procesem)](#hosting-models)z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="d9f0f-127">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) jest macierzystym modułem usług IIS, który obsługuje natywne żądania usług IIS między usługami IIS a serwerem HTTP usług IIS w trakcie procesu lub pustułką.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="d9f0f-128">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="d9f0f-129">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="d9f0f-129">Hosting models</span></span>

<span data-ttu-id="d9f0f-130">Korzystając z hostingu w trakcie, aplikacja ASP.NET Core działa w tym samym procesie co proces roboczy IIS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="d9f0f-131">Hosting w trakcie zapewnia lepszą wydajność w zakresie hostingu poza procesem, ponieważ żądania nie są przerzaszane przez kartę sprzężenia zwrotnego, interfejs sieciowy, który zwraca wychodzący ruch sieciowy z powrotem na ten sam komputer.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="d9f0f-132">Usługi IIS obsługują zarządzanie procesami za pomocą [usługi aktywacji procesów systemu Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d9f0f-133">Korzystając z hostingu poza procesem, aplikacje core ASP.NET są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, a moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="d9f0f-134">Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d9f0f-135">Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d9f0f-136">Aby uzyskać więcej informacji i wskazówek dotyczących konfiguracji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="d9f0f-137">macOS</span><span class="sxs-lookup"><span data-stu-id="d9f0f-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="d9f0f-138">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="d9f0f-139">Linux</span><span class="sxs-lookup"><span data-stu-id="d9f0f-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="d9f0f-140">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="d9f0f-141">Windows</span><span class="sxs-lookup"><span data-stu-id="d9f0f-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="d9f0f-142">ASP.NET Core jest dostarczany z następującymi elementami:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="d9f0f-143">[Serwer pustułki](xref:fundamentals/servers/kestrel) jest domyślnym, wieloplatformowym serwerem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="d9f0f-144">[Serwer HTTP.sys](xref:fundamentals/servers/httpsys) jest serwerem HTTP dostępnym tylko dla systemu Windows opartym na [sterowniku jądra HTTP.sys i interfejsie API serwera HTTP.](/windows/desktop/Http/http-api-start-page)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="d9f0f-145">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [usług IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja jest uruchamiana w procesie odrębnym od procesu roboczego usług IIS *(poza procesem)* z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="d9f0f-146">Ponieważ ASP.NET aplikacje Core są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="d9f0f-147">Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d9f0f-148">Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d9f0f-149">Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane poza procesem:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="d9f0f-151">Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d9f0f-152">Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d9f0f-153">Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d9f0f-154">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwać na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="d9f0f-155">Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d9f0f-156">Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d9f0f-157">Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d9f0f-158">Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d9f0f-159">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d9f0f-160">Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d9f0f-161">W przypadku usług IIS i ASP.NET wskazówki dotyczące konfiguracji modułu podstawowego można znaleźć w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="d9f0f-162">macOS</span><span class="sxs-lookup"><span data-stu-id="d9f0f-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="d9f0f-163">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="d9f0f-164">Linux</span><span class="sxs-lookup"><span data-stu-id="d9f0f-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="d9f0f-165">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="d9f0f-166">Nginx z kestrelem</span><span class="sxs-lookup"><span data-stu-id="d9f0f-166">Nginx with Kestrel</span></span>

<span data-ttu-id="d9f0f-167">Aby uzyskać informacje na temat używania Nginx w systemie Linux <xref:host-and-deploy/linux-nginx>jako odwrotnego serwera proxy dla Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="d9f0f-168">Apache z Kestrel</span><span class="sxs-lookup"><span data-stu-id="d9f0f-168">Apache with Kestrel</span></span>

<span data-ttu-id="d9f0f-169">Aby uzyskać informacje na temat używania Apache w systemie Linux <xref:host-and-deploy/linux-apache>jako odwrotnego serwera proxy dla Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="d9f0f-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d9f0f-170">HTTP.sys</span></span>

<span data-ttu-id="d9f0f-171">Jeśli aplikacje ASP.NET Core są uruchamiane w systemie Windows, http.sys jest alternatywą dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="d9f0f-172">Pustułka jest ogólnie zalecana dla najlepszej wydajności.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="d9f0f-173">HTTP.sys może służyć w scenariuszach, w których aplikacja jest narażona na Internet i wymagane funkcje są obsługiwane przez HTTP.sys, ale nie Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="d9f0f-174">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys komunikuje się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="d9f0f-176">Http.sys może być również używany dla aplikacji, które są udostępniane tylko w sieci wewnętrznej.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d9f0f-178">Aby uzyskać wskazówki dotyczące konfiguracji <xref:fundamentals/servers/httpsys>http.sys, zobacz .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="d9f0f-179">ASP.NET Podstawowa infrastruktura serwera</span><span class="sxs-lookup"><span data-stu-id="d9f0f-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="d9f0f-180">Dostępne <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> w `Startup.Configure` metodzie udostępnia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> właściwość <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>typu .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="d9f0f-181">Pustułka i HTTP.sys tylko uwidaczniają jedną funkcję, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>ale różne implementacje serwera mogą udostępniać dodatkowe funkcje.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="d9f0f-182">`IServerAddressesFeature`można użyć, aby dowiedzieć się, który port implementacji serwera ma powiązane w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="d9f0f-183">Serwery niestandardowe</span><span class="sxs-lookup"><span data-stu-id="d9f0f-183">Custom servers</span></span>

<span data-ttu-id="d9f0f-184">Jeśli wbudowane serwery nie spełniają wymagań aplikacji, można utworzyć niestandardową implementację serwera.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="d9f0f-185">Przewodnik [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) pokazuje, jak napisać implementację opartą na <xref:Microsoft.AspNetCore.Hosting.Server.IServer> [nowin.](https://github.com/Bobris/Nowin)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="d9f0f-186">Tylko interfejsy funkcji, które używa aplikacji wymaga implementacji, choć co najmniej <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> i <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> muszą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="d9f0f-187">Uruchamianie serwera</span><span class="sxs-lookup"><span data-stu-id="d9f0f-187">Server startup</span></span>

<span data-ttu-id="d9f0f-188">Serwer jest uruchamiany po uruchomieniu aplikacji zintegrowanego środowiska programistycznego (IDE) lub edytora:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="d9f0f-189">[Profile](https://visualstudio.microsoft.com) &ndash; uruchamiania programu Visual Studio mogą służyć do uruchamiania aplikacji i serwera za pomocą [programu IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET core module](xref:host-and-deploy/aspnet-core-module) lub konsoli.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="d9f0f-190">[Visual Studio Kod](https://code.visualstudio.com/) &ndash; Aplikacja i serwer są uruchamiane przez [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), który aktywuje debuger CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="d9f0f-191">[Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; Aplikacja i serwer są uruchamiane przez [debuger mono tryb](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)miękki .</span><span class="sxs-lookup"><span data-stu-id="d9f0f-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="d9f0f-192">Podczas uruchamiania aplikacji z wiersza polecenia w folderze projektu, [dotnet uruchom](/dotnet/core/tools/dotnet-run) uruchamia aplikację i serwer (Tylko Kestrel i HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="d9f0f-193">Konfiguracja jest określona przez `-c|--configuration` opcję, która `Debug` jest ustawiona na opcję (domyślna) lub `Release`.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="d9f0f-194">Plik *launchSettings.json* zapewnia konfigurację podczas `dotnet run` uruchamiania aplikacji z debugerem lub z wbudowanym debugerem, takim jak Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="d9f0f-195">Jeśli profile uruchamiania są obecne w pliku *launchSettings.json,* użyj `--launch-profile {PROFILE NAME}` opcji z `dotnet run` poleceniem lub wybierz profil w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="d9f0f-196">Aby uzyskać więcej informacji, zobacz [dotnet run](/dotnet/core/tools/dotnet-run) i [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="d9f0f-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="d9f0f-197">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d9f0f-197">HTTP/2 support</span></span>

<span data-ttu-id="d9f0f-198">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany przez ASP.NET Core w następujących scenariuszach wdrażania:</span><span class="sxs-lookup"><span data-stu-id="d9f0f-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="d9f0f-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="d9f0f-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="d9f0f-200">System operacyjny</span><span class="sxs-lookup"><span data-stu-id="d9f0f-200">Operating system</span></span>
    * <span data-ttu-id="d9f0f-201">Windows Server 2016/Windows 10 lub nowsze&dagger;</span><span class="sxs-lookup"><span data-stu-id="d9f0f-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="d9f0f-202">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16.04 lub nowszym)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="d9f0f-203">Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="d9f0f-204">Struktura docelowa: .NET Core 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="d9f0f-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="d9f0f-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d9f0f-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="d9f0f-206">Windows Server 2016/Windows 10 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d9f0f-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="d9f0f-207">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="d9f0f-208">IIS (w toku)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="d9f0f-209">Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d9f0f-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d9f0f-210">Struktura docelowa: .NET Core 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="d9f0f-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="d9f0f-211">IIS (poza procesem)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="d9f0f-212">Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d9f0f-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d9f0f-213">Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z Kestrelem używa protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d9f0f-214">Struktura docelowa: nie dotyczy wdrożeń poza procesem usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="d9f0f-215">&dagger;Pustułka ma ograniczoną obsługę protokołu HTTP/2 w systemach Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d9f0f-216">Obsługa jest ograniczona, ponieważ lista obsługiwanych pakietów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d9f0f-217">Certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego krzywej eliptycznej (ECDSA) może być wymagany do zabezpieczenia połączeń TLS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="d9f0f-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="d9f0f-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="d9f0f-219">Windows Server 2016/Windows 10 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d9f0f-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="d9f0f-220">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="d9f0f-221">IIS (poza procesem)</span><span class="sxs-lookup"><span data-stu-id="d9f0f-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="d9f0f-222">Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="d9f0f-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d9f0f-223">Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z Kestrelem używa protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d9f0f-224">Struktura docelowa: nie dotyczy wdrożeń poza procesem usług IIS.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="d9f0f-225">Połączenie HTTP/2 musi używać [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) i protokołu TLS 1.2 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="d9f0f-226">Aby uzyskać więcej informacji, zobacz tematy dotyczące scenariuszy wdrażania serwera.</span><span class="sxs-lookup"><span data-stu-id="d9f0f-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9f0f-227">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d9f0f-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
