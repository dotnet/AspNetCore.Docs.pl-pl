---
title: Implementacje serwera sieci Web w ASP.NET Core
author: rick-anderson
description: Odkryj serwery sieci Web Kestrel i HTTP.sys ASP.NET Core. Dowiedz się, jak wybrać serwer i kiedy używać serwera zwrotnego serwera proxy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217560"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="1726a-104">Implementacje serwera sieci Web w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1726a-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="1726a-105">Autorzy [Dykstra](https://github.com/tdykstra), [Steve Kowalski](https://ardalis.com/), [Stephen](https://twitter.com/halter73), i [Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="1726a-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="1726a-106">Aplikacja ASP.NET Core jest uruchamiana z użyciem implementacji serwera HTTP w procesie.</span><span class="sxs-lookup"><span data-stu-id="1726a-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="1726a-107">Implementacja serwera nasłuchuje żądań HTTP i umieszcza je w aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="1726a-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="1726a-108">Windows</span><span class="sxs-lookup"><span data-stu-id="1726a-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="1726a-109">ASP.NET Core dostarcza następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="1726a-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="1726a-110">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http.</span><span class="sxs-lookup"><span data-stu-id="1726a-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="1726a-111">Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji w HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1726a-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="1726a-112">Aby uzyskać więcej informacji, zobacz [Kestrel a HTTP.sys](#korh) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="1726a-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>
* <span data-ttu-id="1726a-113">Serwer HTTP usług IIS jest [serwerem w procesie](#hosting-models) dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1726a-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="1726a-114">[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="1726a-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="1726a-115">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)uruchomiona jest aplikacja:</span><span class="sxs-lookup"><span data-stu-id="1726a-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="1726a-116">W tym samym procesie co proces roboczy usług IIS ( [model hostingu w procesie](#hosting-models)) z serwerem HTTP usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1726a-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="1726a-117">*W procesie* jest zalecana konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="1726a-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="1726a-118">W procesie innym niż proces roboczy usług IIS ( [model hostingu poza procesem](#hosting-models)) z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="1726a-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="1726a-119">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest natywnym modułem usług IIS, który obsługuje natywne żądania usług IIS między usługami IIS a serwerem HTTP lub Kestrel IIS w procesie.</span><span class="sxs-lookup"><span data-stu-id="1726a-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="1726a-120">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="1726a-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="1726a-121">Kestrel a HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1726a-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="1726a-122">Kestrel ma następujące zalety w stosunku do HTTP.sys:</span><span class="sxs-lookup"><span data-stu-id="1726a-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="1726a-123">Lepsza wydajność i użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="1726a-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="1726a-124">Wiele platform</span><span class="sxs-lookup"><span data-stu-id="1726a-124">Cross platform</span></span>
  * <span data-ttu-id="1726a-125">Elastyczność, opracowana i niezależna od systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="1726a-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="1726a-126">Port programistyczny i konfiguracja protokołu TLS</span><span class="sxs-lookup"><span data-stu-id="1726a-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="1726a-127">Rozszerzalność umożliwiająca obsługę protokołów, takich jak [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) i alternatywne transporty.</span><span class="sxs-lookup"><span data-stu-id="1726a-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="1726a-128">Http.Sys działa jako współużytkowany składnik trybu jądra z następującymi funkcjami, które nie są dostępne dla Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1726a-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="1726a-129">Udostępnianie portów</span><span class="sxs-lookup"><span data-stu-id="1726a-129">Port sharing</span></span>
  * <span data-ttu-id="1726a-130">Uwierzytelnianie systemu Windows trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="1726a-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="1726a-131">[Kestrel obsługuje tylko uwierzytelnianie w trybie użytkownika](xref:security/authentication/windowsauth#kestrel).</span><span class="sxs-lookup"><span data-stu-id="1726a-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="1726a-132">Szybki serwer proxy za pośrednictwem transferów kolejek</span><span class="sxs-lookup"><span data-stu-id="1726a-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="1726a-133">Bezpośrednia transmisja plików</span><span class="sxs-lookup"><span data-stu-id="1726a-133">Direct file transmission</span></span>
  * <span data-ttu-id="1726a-134">Buforowanie odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="1726a-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="1726a-135">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="1726a-135">Hosting models</span></span>

<span data-ttu-id="1726a-136">Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1726a-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="1726a-137">Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.</span><span class="sxs-lookup"><span data-stu-id="1726a-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="1726a-138">Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="1726a-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="1726a-139">Korzystając z hostingu poza procesem, ASP.NET Core aplikacje są uruchamiane w procesie oddzielonym od procesu roboczego usług IIS, a moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="1726a-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="1726a-140">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="1726a-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="1726a-141">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="1726a-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="1726a-142">Aby uzyskać więcej informacji i wskazówki dotyczące konfiguracji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="1726a-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="1726a-143">macOS</span><span class="sxs-lookup"><span data-stu-id="1726a-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="1726a-144">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="1726a-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="1726a-145">Linux</span><span class="sxs-lookup"><span data-stu-id="1726a-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="1726a-146">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="1726a-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="1726a-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="1726a-147">Kestrel</span></span>

 <span data-ttu-id="1726a-148">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http.</span><span class="sxs-lookup"><span data-stu-id="1726a-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="1726a-149">Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji w HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1726a-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="1726a-150">Aby uzyskać więcej informacji, zobacz [Kestrel a HTTP.sys](#korh) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="1726a-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="1726a-151">Użyj Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1726a-151">Use Kestrel:</span></span>

* <span data-ttu-id="1726a-152">Jako serwer graniczny przetwarza żądania bezpośrednio z sieci, w tym z Internetu.</span><span class="sxs-lookup"><span data-stu-id="1726a-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="1726a-154">*Serwer proxy zwrotnego*, taki jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1726a-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="1726a-155">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1726a-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="1726a-157">&mdash;Obsługiwana jest konfiguracja hostingu z serwerem zwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="1726a-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="1726a-158">Aby uzyskać wskazówki dotyczące konfiguracji Kestrel i informacje o tym, kiedy używać Kestrel w konfiguracji zwrotnego serwera proxy, zobacz <xref:fundamentals/servers/kestrel> .</span><span class="sxs-lookup"><span data-stu-id="1726a-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="1726a-159">Windows</span><span class="sxs-lookup"><span data-stu-id="1726a-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="1726a-160">ASP.NET Core dostarcza następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="1726a-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="1726a-161">[Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="1726a-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="1726a-162">[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="1726a-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="1726a-163">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja działa w procesie innym niż proces roboczy usług IIS (*poza procesem*) z [serwerem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="1726a-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="1726a-164">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="1726a-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="1726a-165">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="1726a-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="1726a-166">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="1726a-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="1726a-167">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="1726a-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="1726a-169">Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="1726a-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="1726a-170">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1726a-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="1726a-171">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="1726a-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="1726a-172">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="1726a-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="1726a-173">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="1726a-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="1726a-174">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1726a-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="1726a-175">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="1726a-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="1726a-176">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1726a-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="1726a-177">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1726a-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="1726a-178">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="1726a-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="1726a-179">Aby uzyskać wskazówki dotyczące konfiguracji modułu usług IIS i ASP.NET Core, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="1726a-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="1726a-180">macOS</span><span class="sxs-lookup"><span data-stu-id="1726a-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="1726a-181">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="1726a-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="1726a-182">Linux</span><span class="sxs-lookup"><span data-stu-id="1726a-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="1726a-183">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.</span><span class="sxs-lookup"><span data-stu-id="1726a-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="1726a-184">Nginx z Kestrel</span><span class="sxs-lookup"><span data-stu-id="1726a-184">Nginx with Kestrel</span></span>

<span data-ttu-id="1726a-185">Aby uzyskać informacje na temat używania Nginx w systemie Linux jako serwera zwrotnego proxy dla Kestrel, zobacz <xref:host-and-deploy/linux-nginx> .</span><span class="sxs-lookup"><span data-stu-id="1726a-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="1726a-186">Apache z Kestrel</span><span class="sxs-lookup"><span data-stu-id="1726a-186">Apache with Kestrel</span></span>

<span data-ttu-id="1726a-187">Aby uzyskać informacje na temat korzystania z usługi Apache w systemie Linux jako serwera zwrotnego proxy dla usługi Kestrel, zobacz <xref:host-and-deploy/linux-apache> .</span><span class="sxs-lookup"><span data-stu-id="1726a-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="1726a-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1726a-188">HTTP.sys</span></span>

<span data-ttu-id="1726a-189">Jeśli ASP.NET Core aplikacje są uruchamiane w systemie Windows, HTTP.sys jest alternatywą dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1726a-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="1726a-190">Kestrel jest zalecane w stosunku do HTTP.sys, chyba że aplikacja wymaga funkcji niedostępnych w Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1726a-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="1726a-191">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="1726a-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys komunikują się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="1726a-193">HTTP.sys można również używać dla aplikacji, które są dostępne tylko w sieci wewnętrznej.</span><span class="sxs-lookup"><span data-stu-id="1726a-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="1726a-195">Aby uzyskać HTTP.sys wskazówki dotyczące konfiguracji, zobacz <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="1726a-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="1726a-196">Infrastruktura serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1726a-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="1726a-197"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Dostępne w `Startup.Configure` metodzie uwidacznia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> Właściwość typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> .</span><span class="sxs-lookup"><span data-stu-id="1726a-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="1726a-198">Kestrel i HTTP.sys uwidaczniają tylko jedną funkcję, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ale różne implementacje serwera mogą uwidaczniać dodatkowe funkcje.</span><span class="sxs-lookup"><span data-stu-id="1726a-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="1726a-199">`IServerAddressesFeature` można go użyć, aby dowiedzieć się, który port implementacji serwera w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="1726a-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="1726a-200">Serwery niestandardowe</span><span class="sxs-lookup"><span data-stu-id="1726a-200">Custom servers</span></span>

<span data-ttu-id="1726a-201">Jeśli wbudowane serwery nie spełniają wymagań aplikacji, można utworzyć niestandardową implementację serwera.</span><span class="sxs-lookup"><span data-stu-id="1726a-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="1726a-202">W [przewodniku otwierania interfejsu sieci Web dla platformy .NET (Owin)](xref:fundamentals/owin) przedstawiono sposób pisania implementacji opartej na [nowin](https://github.com/Bobris/Nowin) <xref:Microsoft.AspNetCore.Hosting.Server.IServer> .</span><span class="sxs-lookup"><span data-stu-id="1726a-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="1726a-203">Tylko interfejsy funkcji używane przez aplikację wymagają implementacji, ale <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> muszą być obsługiwane w minimalnym i <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> wymaganym czasie.</span><span class="sxs-lookup"><span data-stu-id="1726a-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="1726a-204">Uruchamianie serwera</span><span class="sxs-lookup"><span data-stu-id="1726a-204">Server startup</span></span>

<span data-ttu-id="1726a-205">Serwer jest uruchamiany po uruchomieniu aplikacji zintegrowanego środowiska programistycznego (IDE) lub edytora:</span><span class="sxs-lookup"><span data-stu-id="1726a-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="1726a-206">[Visual Studio](https://visualstudio.microsoft.com): profile uruchamiania mogą służyć do uruchamiania aplikacji i serwera za pomocą [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) lub konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="1726a-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="1726a-207">[Visual Studio Code](https://code.visualstudio.com/): aplikacja i serwer są uruchamiane przez [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), która aktywuje debuger CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="1726a-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="1726a-208">[Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/): aplikacja i serwer są uruchamiane za pomocą [debugera Soft-Mode mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="1726a-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="1726a-209">Podczas uruchamiania aplikacji z poziomu wiersza polecenia w folderze projektu, [uruchomienie dotnet](/dotnet/core/tools/dotnet-run) uruchamia aplikację i serwer (tylko Kestrel i HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="1726a-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="1726a-210">Konfiguracja jest określana przez `-c|--configuration` opcję, która jest ustawiona na wartość `Debug` (domyślnie) lub `Release` .</span><span class="sxs-lookup"><span data-stu-id="1726a-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="1726a-211">*launchSettings.jsw* pliku zapewnia konfigurację podczas uruchamiania aplikacji przy użyciu `dotnet run` debugera wbudowanego w narzędzia, takiego jak Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1726a-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="1726a-212">Jeśli profile uruchamiania są obecne w *launchSettings.jsw* pliku, użyj `--launch-profile {PROFILE NAME}` opcji z `dotnet run` poleceniem lub wybierz profil w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1726a-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="1726a-213">Aby uzyskać więcej informacji, [](/dotnet/core/tools/dotnet-run) zobacz [pakietem rozkładu dotnet i .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="1726a-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="1726a-214">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1726a-214">HTTP/2 support</span></span>

<span data-ttu-id="1726a-215">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania:</span><span class="sxs-lookup"><span data-stu-id="1726a-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="1726a-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="1726a-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="1726a-217">System operacyjny</span><span class="sxs-lookup"><span data-stu-id="1726a-217">Operating system</span></span>
    * <span data-ttu-id="1726a-218">Windows Server 2016/Windows 10 lub nowszy&dagger;</span><span class="sxs-lookup"><span data-stu-id="1726a-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="1726a-219">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="1726a-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="1726a-220">Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1726a-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="1726a-221">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="1726a-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1726a-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="1726a-223">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="1726a-224">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1726a-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="1726a-225">Usługi IIS (w procesie)</span><span class="sxs-lookup"><span data-stu-id="1726a-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="1726a-226">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1726a-227">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="1726a-228">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="1726a-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="1726a-229">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1726a-230">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1726a-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="1726a-231">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1726a-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="1726a-232">&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="1726a-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="1726a-233">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="1726a-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="1726a-234">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="1726a-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="1726a-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="1726a-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="1726a-236">System operacyjny</span><span class="sxs-lookup"><span data-stu-id="1726a-236">Operating system</span></span>
    * <span data-ttu-id="1726a-237">Windows Server 2016/Windows 10 lub nowszy&dagger;</span><span class="sxs-lookup"><span data-stu-id="1726a-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="1726a-238">Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)</span><span class="sxs-lookup"><span data-stu-id="1726a-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="1726a-239">Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="1726a-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="1726a-240">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="1726a-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1726a-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="1726a-242">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="1726a-243">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1726a-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="1726a-244">Usługi IIS (w procesie)</span><span class="sxs-lookup"><span data-stu-id="1726a-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="1726a-245">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1726a-246">Platforma docelowa: .NET Core 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="1726a-247">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="1726a-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="1726a-248">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1726a-249">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1726a-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="1726a-250">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1726a-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="1726a-251">&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="1726a-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="1726a-252">Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="1726a-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="1726a-253">Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.</span><span class="sxs-lookup"><span data-stu-id="1726a-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="1726a-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1726a-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="1726a-255">Windows Server 2016/Windows 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="1726a-256">Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1726a-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="1726a-257">Usługi IIS (pozaprocesowe)</span><span class="sxs-lookup"><span data-stu-id="1726a-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="1726a-258">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="1726a-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1726a-259">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1726a-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="1726a-260">Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1726a-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="1726a-261">Połączenie HTTP/2 musi korzystać z [negocjacji protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) i TLS 1,2 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="1726a-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="1726a-262">Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi scenariuszy wdrażania serwera.</span><span class="sxs-lookup"><span data-stu-id="1726a-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1726a-263">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1726a-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
