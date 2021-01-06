---
title: Hosting w procesie z usługami IIS i ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat hostingu w procesie przy użyciu usług IIS i modułu ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058483"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="87b3b-103">Hosting w procesie z usługami IIS i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87b3b-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="87b3b-104">Hosting w procesie uruchamia aplikację ASP.NET Core w tym samym procesie co proces roboczy usług IIS.</span><span class="sxs-lookup"><span data-stu-id="87b3b-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="87b3b-105">Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.</span><span class="sxs-lookup"><span data-stu-id="87b3b-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="87b3b-106">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowaną w procesie aplikacją:</span><span class="sxs-lookup"><span data-stu-id="87b3b-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Moduł ASP.NET Core w scenariuszu hostingu w procesie](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="87b3b-108">Włącz hosting w procesie</span><span class="sxs-lookup"><span data-stu-id="87b3b-108">Enable in-process hosting</span></span>

<span data-ttu-id="87b3b-109">Ponieważ ASP.NET Core 3,0, hosting w procesie został domyślnie włączony dla całej aplikacji wdrożonej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="87b3b-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="87b3b-110">Aby jawnie skonfigurować aplikację do hostingu w procesie, ustaw wartość `<AspNetCoreHostingModel>` właściwości na `InProcess` w pliku projektu ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="87b3b-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="87b3b-111">Architektura ogólna</span><span class="sxs-lookup"><span data-stu-id="87b3b-111">General architecture</span></span>

<span data-ttu-id="87b3b-112">Ogólny przepływ żądania jest następujący:</span><span class="sxs-lookup"><span data-stu-id="87b3b-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="87b3b-113">Żądanie dociera do sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="87b3b-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="87b3b-114">Sterownik kieruje natywne żądanie do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="87b3b-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="87b3b-115">Moduł ASP.NET Core odbiera żądanie natywne i przekazuje go do serwera HTTP usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="87b3b-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="87b3b-116">Serwer HTTP IIS jest implementacją serwera w procesie dla usług IIS, która konwertuje żądanie z natywnego na zarządzane.</span><span class="sxs-lookup"><span data-stu-id="87b3b-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="87b3b-117">Po przetworzeniu żądania przez serwer HTTP IIS:</span><span class="sxs-lookup"><span data-stu-id="87b3b-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="87b3b-118">Żądanie jest wysyłane do ASP.NET Core potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="87b3b-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="87b3b-119">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="87b3b-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="87b3b-120">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS za pośrednictwem serwera HTTP IIS.</span><span class="sxs-lookup"><span data-stu-id="87b3b-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="87b3b-121">Program IIS wysyła odpowiedź do klienta, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="87b3b-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="87b3b-122">`CreateDefaultBuilder` dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie przez wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> metody w celu uruchomienia [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji w procesie roboczym usług IIS ( `w3wp.exe` lub `iisexpress.exe` ).</span><span class="sxs-lookup"><span data-stu-id="87b3b-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="87b3b-123">Testy wydajności wskazują, że hostowanie aplikacji platformy .NET Core w procesie zapewnia znacznie wyższą przepływność żądań w porównaniu z obsługą żądań proxy poza procesem i [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="87b3b-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="87b3b-124">Aplikacje publikowane jako pojedynczy plik wykonywalny nie mogą zostać załadowane przez model hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="87b3b-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="87b3b-125">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="87b3b-125">Application configuration</span></span>

<span data-ttu-id="87b3b-126">Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISServerOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> .</span><span class="sxs-lookup"><span data-stu-id="87b3b-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="87b3b-127">Poniższy przykład wyłącza AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="87b3b-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="87b3b-128">Opcja</span><span class="sxs-lookup"><span data-stu-id="87b3b-128">Option</span></span> | <span data-ttu-id="87b3b-129">Domyślne</span><span class="sxs-lookup"><span data-stu-id="87b3b-129">Default</span></span> | <span data-ttu-id="87b3b-130">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="87b3b-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="87b3b-131">Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="87b3b-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="87b3b-132">Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="87b3b-133">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="87b3b-134">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="87b3b-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="87b3b-135">Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="87b3b-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="87b3b-136">Czy synchroniczna operacja we/wy jest dozwolona dla `HttpContext.Request` i `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="87b3b-137">Pobiera lub ustawia maksymalny rozmiar treści żądania dla `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="87b3b-138">Należy zauważyć, że same usługi IIS mają limit, `maxAllowedContentLength` który zostanie przetworzony przed `MaxRequestBodySize` zestawem w `IISServerOptions` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="87b3b-139">Zmiana `MaxRequestBodySize` nie wpłynie na `maxAllowedContentLength` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="87b3b-140">Aby zwiększyć `maxAllowedContentLength` , Dodaj wpis w programie, `web.config` Aby ustawić `maxAllowedContentLength` wyższą wartość.</span><span class="sxs-lookup"><span data-stu-id="87b3b-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="87b3b-141">Aby uzyskać więcej informacji, zobacz [Konfiguracja](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="87b3b-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="87b3b-142">Różnice między procesem hostingu w procesie a poza procesami</span><span class="sxs-lookup"><span data-stu-id="87b3b-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="87b3b-143">Następujące cechy są stosowane podczas hostingu w procesie:</span><span class="sxs-lookup"><span data-stu-id="87b3b-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="87b3b-144">Serwer HTTP IIS ( `IISHttpServer` ) jest używany zamiast serwera [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="87b3b-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="87b3b-145">W przypadku wywołań w procesie [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> :</span><span class="sxs-lookup"><span data-stu-id="87b3b-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="87b3b-146">Zarejestruj `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="87b3b-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="87b3b-147">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87b3b-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="87b3b-148">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="87b3b-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="87b3b-149">Ten [ `requestTimeout` atrybut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="87b3b-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="87b3b-150">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="87b3b-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="87b3b-151">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="87b3b-151">Use one app pool per app.</span></span>

* <span data-ttu-id="87b3b-152">Architektura (bitową) aplikacji i zainstalowane środowisko uruchomieniowe (x64 lub x86) musi być zgodna z architekturą puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="87b3b-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="87b3b-153">Na przykład aplikacje opublikowane dla 32-bitowej (x86) muszą mieć włączone 32-bitowe dla ich pul aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="87b3b-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="87b3b-154">Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie witryny usług IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="87b3b-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="87b3b-155">Wykryto rozłączenia klientów.</span><span class="sxs-lookup"><span data-stu-id="87b3b-155">Client disconnects are detected.</span></span> <span data-ttu-id="87b3b-156">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)Token anulowania jest anulowany, gdy klient odłączy się.</span><span class="sxs-lookup"><span data-stu-id="87b3b-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="87b3b-157">Podczas hostingu w procesie <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="87b3b-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="87b3b-158">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="87b3b-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="87b3b-159">Podczas przekształcania oświadczeń z <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacją, wywołaj polecenie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> Dodaj usługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="87b3b-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="87b3b-160">[Wdrożenia pakietów sieci Web (jednego pliku)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="87b3b-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
