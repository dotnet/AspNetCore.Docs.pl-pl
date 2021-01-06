---
title: Hosting poza procesem przy użyciu usług IIS i ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat hostingu poza procesem przy użyciu usług IIS i modułu ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060420"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="2ccac-103">Hosting poza procesem przy użyciu usług IIS i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ccac-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="2ccac-104">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="2ccac-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="2ccac-105">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="2ccac-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="2ccac-106">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="2ccac-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2ccac-107">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="2ccac-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="2ccac-109">Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="2ccac-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="2ccac-110">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2ccac-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="2ccac-111">Skonfigurowany port jest zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2ccac-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="2ccac-112">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2ccac-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="2ccac-113">Port losowy nie jest 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="2ccac-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="2ccac-114">Moduł ASP.NET Core określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2ccac-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="2ccac-115"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Rozszerzenie konfiguruje serwer do nasłuchiwania `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="2ccac-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="2ccac-116">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2ccac-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2ccac-117">Moduł nie obsługuje przekazywania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2ccac-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="2ccac-118">Żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługi IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2ccac-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2ccac-119">Po poKestrel przez program żądania z modułu żądanie zostanie przekazane do ASP.NET Core potoku programu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="2ccac-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2ccac-120">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2ccac-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2ccac-121">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2ccac-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2ccac-122">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która przekazuje ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="2ccac-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2ccac-123">Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="2ccac-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="2ccac-124">Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="2ccac-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="2ccac-125">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="2ccac-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="2ccac-126">Włącz składniki IISIntegration</span><span class="sxs-lookup"><span data-stu-id="2ccac-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="2ccac-127">Podczas kompilowania hosta w `CreateHostBuilder` ( `Program.cs` ), wywołaj, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> Aby włączyć integrację usług IIS:</span><span class="sxs-lookup"><span data-stu-id="2ccac-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="2ccac-128">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/generic-host#default-builder-settings> .</span><span class="sxs-lookup"><span data-stu-id="2ccac-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="2ccac-129">**Model hostingu poza procesem**</span><span class="sxs-lookup"><span data-stu-id="2ccac-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="2ccac-130">Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> .</span><span class="sxs-lookup"><span data-stu-id="2ccac-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="2ccac-131">Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="2ccac-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="2ccac-132">Opcja</span><span class="sxs-lookup"><span data-stu-id="2ccac-132">Option</span></span>                         | <span data-ttu-id="2ccac-133">Domyślne</span><span class="sxs-lookup"><span data-stu-id="2ccac-133">Default</span></span> | <span data-ttu-id="2ccac-134">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="2ccac-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2ccac-135">Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="2ccac-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2ccac-136">Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="2ccac-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2ccac-137">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="2ccac-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2ccac-138">Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="2ccac-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2ccac-139">Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="2ccac-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="2ccac-140">Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony.</span><span class="sxs-lookup"><span data-stu-id="2ccac-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2ccac-141">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="2ccac-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2ccac-142">[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) i moduł ASP.NET Core są skonfigurowane do przesyłania dalej:</span><span class="sxs-lookup"><span data-stu-id="2ccac-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="2ccac-143">Schemat (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2ccac-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="2ccac-144">Zdalny adres IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="2ccac-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="2ccac-145">[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) konfiguruje przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="2ccac-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="2ccac-146">W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="2ccac-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="2ccac-147">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="2ccac-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2ccac-148">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="2ccac-148">Out-of-process hosting model</span></span>

<span data-ttu-id="2ccac-149">Aby skonfigurować aplikację do hostingu poza procesem, ustaw wartość `<AspNetCoreHostingModel>` właściwości na `OutOfProcess` w pliku projektu ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="2ccac-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2ccac-150">Hosting w procesie jest ustawiony przy użyciu `InProcess` wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="2ccac-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="2ccac-151">Wartość `<AspNetCoreHostingModel>` jest uwzględniana wielkość liter, dlatego `inprocess` i `outofprocess` są prawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="2ccac-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2ccac-152">Serwer [Kestrel](xref:fundamentals/servers/kestrel) jest używany zamiast serwera http usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="2ccac-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2ccac-153">W przypadku nieprzetwarzanych [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) wywołań <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> :</span><span class="sxs-lookup"><span data-stu-id="2ccac-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="2ccac-154">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ccac-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2ccac-155">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2ccac-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="2ccac-156">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="2ccac-156">Process name</span></span>

<span data-ttu-id="2ccac-157">`Process.GetCurrentProcess().ProcessName`Raporty `w3wp` / `iisexpress` (w procesie) lub `dotnet` (pozaprocesowe).</span><span class="sxs-lookup"><span data-stu-id="2ccac-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2ccac-158">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2ccac-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2ccac-159">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="2ccac-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2ccac-160">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="2ccac-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2ccac-161">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2ccac-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2ccac-162">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2ccac-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2ccac-163">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2ccac-163">Forward Windows authentication tokens.</span></span>
