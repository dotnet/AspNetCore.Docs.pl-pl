---
title: Moduł ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować moduł ASP.NET Core na potrzeby hostowania aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c5c26c72af8103167b421cfa4aa06c23c242c1d7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015910"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="2dea2-103">Moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2dea2-103">ASP.NET Core Module</span></span>

<span data-ttu-id="2dea2-104">Autorzy [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Krzysztof Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)i [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="2dea2-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2dea2-105">Moduł ASP.NET Core jest natywnym modułem usług IIS, który jest podłączany do potoku usług IIS:</span><span class="sxs-lookup"><span data-stu-id="2dea2-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2dea2-106">Hostowanie aplikacji ASP.NET Core w procesie roboczym usług IIS ( `w3wp.exe` ), nazywanym [modelem hostingu w procesie](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2dea2-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2dea2-107">Przekazuj żądania sieci Web do zaplecza ASP.NET Core aplikacji, na której uruchomiono [serwer Kestrel](xref:fundamentals/servers/kestrel), nazywany [modelem hostingu poza procesem](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2dea2-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2dea2-108">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="2dea2-108">Supported Windows versions:</span></span>

* <span data-ttu-id="2dea2-109">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2dea2-109">Windows 7 or later</span></span>
* <span data-ttu-id="2dea2-110">System Windows Server 2012 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2dea2-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="2dea2-111">Podczas hostingu w procesie moduł używa implementacji serwera w procesie dla usług IIS, nazywanego serwerem HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2dea2-112">Podczas hostingu poza procesem moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2dea2-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2dea2-113">Moduł nie działa z [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2dea2-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2dea2-114">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2dea2-115">Model hostingu w procesie</span><span class="sxs-lookup"><span data-stu-id="2dea2-115">In-process hosting model</span></span>

<span data-ttu-id="2dea2-116">Domyślnie ASP.NET Core aplikacje do modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="2dea2-117">Następujące cechy są stosowane podczas hostingu w procesie:</span><span class="sxs-lookup"><span data-stu-id="2dea2-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2dea2-118">Serwer HTTP IIS ( `IISHttpServer` ) jest używany zamiast serwera [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="2dea2-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2dea2-119">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) wywołań <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="2dea2-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2dea2-120">Zarejestruj `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2dea2-121">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dea2-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2dea2-122">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2dea2-123">[Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2dea2-124">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2dea2-125">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-125">Use one app pool per app.</span></span>

* <span data-ttu-id="2dea2-126">W przypadku korzystania z [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania [plikuapp_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać ZAMKNIĘTA natychmiast po otwarciu połączenia.</span><span class="sxs-lookup"><span data-stu-id="2dea2-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2dea2-127">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2dea2-128">Architektura (bitową) aplikacji i zainstalowane środowisko uruchomieniowe (x64 lub x86) musi być zgodna z architekturą puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2dea2-129">Wykryto rozłączenia klientów.</span><span class="sxs-lookup"><span data-stu-id="2dea2-129">Client disconnects are detected.</span></span> <span data-ttu-id="2dea2-130">Token anulowania [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) został anulowany po rozłączeniu klienta.</span><span class="sxs-lookup"><span data-stu-id="2dea2-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2dea2-131">W ASP.NET Core 2.2.1 lub wcześniejszym <xref:System.IO.Directory.GetCurrentDirectory*> zwraca katalog procesów roboczych procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="2dea2-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2dea2-132">Przykładowy kod, który ustawia bieżący katalog aplikacji, znajduje się w [klasie CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="2dea2-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2dea2-133">Wywołaj `SetCurrentDirectory` metodę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2dea2-134">Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> udostępnienia katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2dea2-135">Podczas hostingu w procesie <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2dea2-136">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="2dea2-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2dea2-137">Podczas przekształcania oświadczeń z <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacją, wywołaj polecenie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Dodaj usługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2dea2-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="2dea2-138">[Wdrożenia pakietów sieci Web (jednego pliku)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2dea2-139">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="2dea2-139">Out-of-process hosting model</span></span>

<span data-ttu-id="2dea2-140">Aby skonfigurować aplikację do hostingu poza procesem, ustaw wartość `<AspNetCoreHostingModel>` właściwości na `OutOfProcess` w pliku projektu (*. csproj*):</span><span class="sxs-lookup"><span data-stu-id="2dea2-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2dea2-141">Hosting w procesie jest ustawiony przy użyciu `InProcess` wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="2dea2-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="2dea2-142">Wartość `<AspNetCoreHostingModel>` jest uwzględniana wielkość liter, dlatego `inprocess` i `outofprocess` są prawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="2dea2-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2dea2-143">Serwer [Kestrel](xref:fundamentals/servers/kestrel) jest używany zamiast serwera http usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2dea2-144">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) połączeń z <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="2dea2-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2dea2-145">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dea2-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2dea2-146">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2dea2-147">Zmiany modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-147">Hosting model changes</span></span>

<span data-ttu-id="2dea2-148">Jeśli to `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśniono w sekcji [Konfiguracja z web.config](#configuration-with-webconfig) ), moduł odtwarza proces roboczy dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2dea2-149">W przypadku IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala bezpieczne zamknięcie bieżącego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2dea2-150">Następne żądanie do aplikacji powoduje zduplikowanie nowego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2dea2-151">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="2dea2-151">Process name</span></span>

<span data-ttu-id="2dea2-152">`Process.GetCurrentProcess().ProcessName`Raporty `w3wp` / `iisexpress` (w procesie) lub `dotnet` (pozaprocesowe).</span><span class="sxs-lookup"><span data-stu-id="2dea2-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2dea2-153">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2dea2-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2dea2-154">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="2dea2-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2dea2-155">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="2dea2-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2dea2-156">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2dea2-157">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2dea2-158">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2dea2-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2dea2-159">Jak zainstalować moduł ASP.NET Core i korzystać z niego</span><span class="sxs-lookup"><span data-stu-id="2dea2-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2dea2-160">Aby uzyskać instrukcje dotyczące sposobu instalowania modułu ASP.NET Core, zobacz [installing the .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="2dea2-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2dea2-161">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-161">Configuration with web.config</span></span>

<span data-ttu-id="2dea2-162">Moduł ASP.NET Core jest skonfigurowany przy użyciu `aspNetCore` sekcji `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2dea2-163">Następujący *web.config* plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="2dea2-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="2dea2-164">Następujące *web.config* są publikowane dla [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="2dea2-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="2dea2-165"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Właściwość jest ustawiona na `false` , aby wskazać, że ustawienia określone w [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemencie nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="2dea2-166">Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2dea2-167">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles* , który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2dea2-168">Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="2dea2-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2dea2-169">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2dea2-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2dea2-170">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2dea2-170">Attribute</span></span> | <span data-ttu-id="2dea2-171">Opis</span><span class="sxs-lookup"><span data-stu-id="2dea2-171">Description</span></span> | <span data-ttu-id="2dea2-172">Domyślny</span><span class="sxs-lookup"><span data-stu-id="2dea2-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2dea2-173">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-173">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-174">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2dea2-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2dea2-175">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-176">W przypadku wartości true strona **błędu 502,5 procesu** jest pomijana, a strona kodu stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="2dea2-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2dea2-177">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-178">Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje na% ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2dea2-179">Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2dea2-180">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-180">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-181">Określa model hostingu jako proces ( `InProcess` / `inprocess` ) lub out-of-Process ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="2dea2-182">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-183">Określa liczbę wystąpień procesu określonego w ustawieniu **processPath** , które może być przypadające na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2dea2-184">&dagger;W przypadku hostingu w procesie wartość jest ograniczona do `1` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2dea2-185">Ustawienie `processesPerApplication` jest niezalecane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2dea2-186">Ten atrybut zostanie usunięty w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="2dea2-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2dea2-187">Wartooć`1`</span><span class="sxs-lookup"><span data-stu-id="2dea2-187">Default: `1`</span></span><br><span data-ttu-id="2dea2-188">Długości`1`</span><span class="sxs-lookup"><span data-stu-id="2dea2-188">Min: `1`</span></span><br><span data-ttu-id="2dea2-189">Maksymalny`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2dea2-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2dea2-190">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-190">Required string attribute.</span></span></p><p><span data-ttu-id="2dea2-191">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="2dea2-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2dea2-192">Obsługiwane są ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="2dea2-192">Relative paths are supported.</span></span> <span data-ttu-id="2dea2-193">Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2dea2-194">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-195">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2dea2-196">W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="2dea2-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2dea2-197">Nieobsługiwane w przypadku hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2dea2-198">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="2dea2-198">Default: `10`</span></span><br><span data-ttu-id="2dea2-199">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-199">Min: `0`</span></span><br><span data-ttu-id="2dea2-200">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="2dea2-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2dea2-201">Opcjonalny atrybut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="2dea2-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2dea2-202">Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="2dea2-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2dea2-203">W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="2dea2-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2dea2-204">Nie dotyczy hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2dea2-205">W przypadku hostingu w procesie moduł czeka na aplikację w celu przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2dea2-206">Prawidłowe wartości segmentów minut i sekund ciągu mieszczą się w zakresie 0-59.</span><span class="sxs-lookup"><span data-stu-id="2dea2-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2dea2-207">Użycie **60** w wartości minut lub sekund skutkuje *błędem wewnętrznego serwera 500*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2dea2-208">Wartooć`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-208">Default: `00:02:00`</span></span><br><span data-ttu-id="2dea2-209">Długości`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-209">Min: `00:00:00`</span></span><br><span data-ttu-id="2dea2-210">Maksymalny`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2dea2-211">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-212">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny zostanie bezpiecznie zamknięty po wykryciu pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2dea2-213">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="2dea2-213">Default: `10`</span></span><br><span data-ttu-id="2dea2-214">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-214">Min: `0`</span></span><br><span data-ttu-id="2dea2-215">Maksymalny`600`</span><span class="sxs-lookup"><span data-stu-id="2dea2-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2dea2-216">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-217">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2dea2-218">Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</span><span class="sxs-lookup"><span data-stu-id="2dea2-218">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="2dea2-219">Podczas hostingu *w procesie*: proces **nie** jest ponownie uruchamiany i **nie używa ustawienia** **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-219">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="2dea2-220">Podczas hostingu *poza procesem*: moduł próbuje ponownie uruchomić proces, gdy odbierze nowe żądanie i kontynuuje próbę ponownego uruchomienia procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie uruchomi **rapidFailsPerMinute** liczby razy w ostatniej minucie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-220">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2dea2-221">Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-221">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2dea2-222">Wartooć`120`</span><span class="sxs-lookup"><span data-stu-id="2dea2-222">Default: `120`</span></span><br><span data-ttu-id="2dea2-223">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-223">Min: `0`</span></span><br><span data-ttu-id="2dea2-224">Maksymalny`3600`</span><span class="sxs-lookup"><span data-stu-id="2dea2-224">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2dea2-225">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-225">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-226">Jeśli wartość jest równa true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2dea2-226">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2dea2-227">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-227">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-228">Określa względną lub bezwzględną ścieżkę do pliku, dla którego jest rejestrowany **stdout** i **stderr** z procesu określonego w **processPath** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-228">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2dea2-229">Ścieżki względne są względne względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-229">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2dea2-230">Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="2dea2-230">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2dea2-231">Wszystkie foldery podane w ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-231">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2dea2-232">Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) są dodawane do ostatniego segmentu ścieżki **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-232">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2dea2-233">Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934. log* w folderze *Logs* , gdy jest zapisywany na 2/5/2018 o godzinie 19:41:32 przy użyciu identyfikatora procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="2dea2-233">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="2dea2-234">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="2dea2-234">Set environment variables</span></span>

<span data-ttu-id="2dea2-235">Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-235">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2dea2-236">Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-236">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2dea2-237">Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiskowymi systemowymi.</span><span class="sxs-lookup"><span data-stu-id="2dea2-237">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="2dea2-238">W poniższym przykładzie są ustawiane dwie zmienne środowiskowe w *web.config*. `ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji do programu `Development` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-238">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2dea2-239">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config* w celu wymuszenia załadowania [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-239">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2dea2-240">`CONFIG_DIR`to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-240">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="2dea2-241">Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-241">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2dea2-242">To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="2dea2-242">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="2dea2-243">Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="2dea2-243">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2dea2-244">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2dea2-244">app_offline.htm</span></span>

<span data-ttu-id="2dea2-245">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="2dea2-245">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2dea2-246">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core kasuje uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="2dea2-246">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2dea2-247">Gdy plik *app_offline.htm* jest obecny, moduł ASP.NET Core reaguje na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-247">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2dea2-248">Po usunięciu pliku *app_offline.htm* następnym żądaniu zostanie uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="2dea2-248">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="2dea2-249">W przypadku korzystania z modelu hostingu poza procesem aplikacja może nie zostać wyłączona natychmiast, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-249">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2dea2-250">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-250">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2dea2-251">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="2dea2-251">Start-up error page</span></span>

<span data-ttu-id="2dea2-252">Hosting zarówno w procesie, jak i poza procesem tworzy niestandardowe strony błędów, gdy nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-252">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2dea2-253">Jeśli moduł ASP.NET Core nie może odnaleźć programu obsługi żądania w procesie lub out-of-process, zostanie wyświetlona strona kodowa stanu *niepowodzenia ładowania 500,0-procesowego/wyjściowego* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-253">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2dea2-254">W przypadku hostingu w procesie, Jeśli uruchomienie aplikacji przez moduł ASP.NET Core nie powiedzie się, zostanie wyświetlona strona kod stanu *niepowodzenia 500,30* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-254">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2dea2-255">W przypadku hostingu poza procesem, jeśli moduł ASP.NET Core nie może uruchomić procesu zaplecza lub proces zaplecza zostanie uruchomiony, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kod stanu *niepowodzenia procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-255">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2dea2-256">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu 5xx usługi IIS, użyj `disableStartUpErrorPage` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-256">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2dea2-257">Aby uzyskać więcej informacji o konfigurowaniu niestandardowych komunikatów o błędach, zobacz [Błędy \<httpErrors> http ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2dea2-257">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2dea2-258">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="2dea2-258">Log creation and redirection</span></span>

<span data-ttu-id="2dea2-259">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="2dea2-259">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2dea2-260">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-260">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2dea2-261">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2dea2-261">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2dea2-262">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-262">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2dea2-263">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2dea2-263">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2dea2-264">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-264">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2dea2-265">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-265">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2dea2-266">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2dea2-266">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2dea2-267">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="2dea2-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2dea2-268">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-268">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2dea2-269">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) do ostatniego segmentu `stdoutLogFile` ścieżki (zazwyczaj *stdout*), które są rozdzielane znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2dea2-269">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2dea2-270">Jeśli `stdoutLogFile` ścieżka kończy się od *stdout*, dziennik aplikacji o identyfikatorze PID 1934 utworzony w 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-270">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2dea2-271">Jeśli `stdoutLogEnabled` ma wartość false, błędy występujące podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="2dea2-271">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2dea2-272">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-272">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2dea2-273">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-273">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2dea2-274">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2dea2-274">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2dea2-275">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-275">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2dea2-276">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2dea2-276">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2dea2-277">Aby utworzyć reguły filtru rejestrowania, zobacz sekcje [Konfiguracja](xref:fundamentals/logging/index#log-filtering) i [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dea2-277">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2dea2-278">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2dea2-278">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2dea2-279">Udoskonalone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="2dea2-279">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2dea2-280">Moduł ASP.NET Core można skonfigurować w celu udostępnienia dzienników diagnostyki rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="2dea2-280">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2dea2-281">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie w `debugLevel` celu `TRACE` ujawniania większej wierności informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="2dea2-281">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="2dea2-282">Wszystkie foldery w ścieżce (*dzienniki* w poprzednim przykładzie) są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-282">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="2dea2-283">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2dea2-283">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2dea2-284">Wartości poziomu debugowania ( `debugLevel` ) mogą zawierać zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-284">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2dea2-285">Poziomy (w kolejności od najmniejszej do największej szczegółowości):</span><span class="sxs-lookup"><span data-stu-id="2dea2-285">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2dea2-286">BŁĄD</span><span class="sxs-lookup"><span data-stu-id="2dea2-286">ERROR</span></span>
* <span data-ttu-id="2dea2-287">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="2dea2-287">WARNING</span></span>
* <span data-ttu-id="2dea2-288">INFORMACJE</span><span class="sxs-lookup"><span data-stu-id="2dea2-288">INFO</span></span>
* <span data-ttu-id="2dea2-289">TRACE</span><span class="sxs-lookup"><span data-stu-id="2dea2-289">TRACE</span></span>

<span data-ttu-id="2dea2-290">Lokalizacje (wiele lokalizacji jest dozwolonych):</span><span class="sxs-lookup"><span data-stu-id="2dea2-290">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2dea2-291">KONSOLI</span><span class="sxs-lookup"><span data-stu-id="2dea2-291">CONSOLE</span></span>
* <span data-ttu-id="2dea2-292">ELEMENCIE</span><span class="sxs-lookup"><span data-stu-id="2dea2-292">EVENTLOG</span></span>
* <span data-ttu-id="2dea2-293">PLIK</span><span class="sxs-lookup"><span data-stu-id="2dea2-293">FILE</span></span>

<span data-ttu-id="2dea2-294">Ustawienia programu obsługi można także zapewnić za pomocą zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="2dea2-294">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2dea2-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="2dea2-296">(Domyślnie: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="2dea2-296">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2dea2-297">`ASPNETCORE_MODULE_DEBUG`: Debugowanie ustawienia poziomu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-297">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2dea2-298">**Nie** pozostawiaj włączonej rejestracji debugowania w ramach wdrożenia dłużej niż jest to wymagane, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-298">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2dea2-299">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="2dea2-299">The size of the log isn't limited.</span></span> <span data-ttu-id="2dea2-300">Pozostawienie włączonego dziennika debugowania może spowodować wyczerpanie dostępnego miejsca na dysku i awarię serwera lub usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="2dea2-300">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2dea2-301">Zobacz sekcję [Configuration with web.config](#configuration-with-webconfig) , aby zobaczyć przykład `aspNetCore` elementu w pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-301">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="2dea2-302">Modyfikowanie rozmiaru stosu</span><span class="sxs-lookup"><span data-stu-id="2dea2-302">Modify the stack size</span></span>

<span data-ttu-id="2dea2-303">*Stosuje się tylko w przypadku korzystania z modelu hostingu w procesie.*</span><span class="sxs-lookup"><span data-stu-id="2dea2-303">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="2dea2-304">Skonfiguruj zarządzany rozmiar stosu przy użyciu `stackSize` Ustawienia w bajtach w *web.config*. Domyślny rozmiar to `1048576` bajty (1 MB).</span><span class="sxs-lookup"><span data-stu-id="2dea2-304">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2dea2-305">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="2dea2-305">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2dea2-306">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="2dea2-306">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="2dea2-307">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2dea2-307">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2dea2-308">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-308">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2dea2-309">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="2dea2-309">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2dea2-310">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="2dea2-310">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2dea2-311">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="2dea2-311">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2dea2-312">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="2dea2-312">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2dea2-313">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="2dea2-313">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2dea2-314">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-314">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2dea2-315">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-315">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2dea2-316">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="2dea2-316">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2dea2-317">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami konta **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-317">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2dea2-318">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="2dea2-318">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2dea2-319">W przypadku korzystania z konfiguracji udostępnionej przez usługi IIS na tym samym komputerze, na którym znajduje się instalacja usług IIS, uruchom Instalatora pakietu ASP.NET Core hostowania z `OPT_NO_SHARED_CONFIG_CHECK` parametrem ustawionym na `1` :</span><span class="sxs-lookup"><span data-stu-id="2dea2-319">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2dea2-320">Jeśli ścieżka do konfiguracji udostępnionej nie znajduje się na tym samym komputerze, na którym znajduje się instalacja usług IIS, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="2dea2-320">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2dea2-321">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-321">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2dea2-322">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="2dea2-322">Run the installer.</span></span>
1. <span data-ttu-id="2dea2-323">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="2dea2-323">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2dea2-324">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-324">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2dea2-325">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-325">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2dea2-326">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2dea2-326">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2dea2-327">W systemie hostingu przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-327">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2dea2-328">Znajdź plik *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-328">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2dea2-329">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-329">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2dea2-330">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-330">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2dea2-331">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem *C: \\ Użytkownicy \\ % username% \\ AppData \\ Local \\ temp*. Plik ma nazwę *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-331">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2dea2-332">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2dea2-332">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2dea2-333">Moduł</span><span class="sxs-lookup"><span data-stu-id="2dea2-333">Module</span></span>

<span data-ttu-id="2dea2-334">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2dea2-334">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2dea2-335">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-335">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-336">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-337">Module\V2\aspnetcorev2.dll rdzeń%ProgramFiles%\IIS\Asp.Net</span><span class="sxs-lookup"><span data-stu-id="2dea2-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2dea2-338">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="2dea2-339">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2dea2-339">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2dea2-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-341">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2dea2-343">% ProgramFiles (x86)% \ Express\Asp.Net Module\V2\aspnetcorev2.dll Core usług IIS</span><span class="sxs-lookup"><span data-stu-id="2dea2-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2dea2-344">Schemat</span><span class="sxs-lookup"><span data-stu-id="2dea2-344">Schema</span></span>

<span data-ttu-id="2dea2-345">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2dea2-345">**IIS**</span></span>

* <span data-ttu-id="2dea2-346">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2dea2-347">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="2dea2-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2dea2-348">**IIS Express**</span></span>

* <span data-ttu-id="2dea2-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2dea2-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2dea2-351">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="2dea2-351">Configuration</span></span>

<span data-ttu-id="2dea2-352">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2dea2-352">**IIS**</span></span>

* <span data-ttu-id="2dea2-353">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-353">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2dea2-354">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2dea2-354">**IIS Express**</span></span>

* <span data-ttu-id="2dea2-355">Visual Studio: {Aplikacja główna} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2dea2-356">*iisexpress.exe* INTERFEJS WIERSZA POLECENIA:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2dea2-357">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-357">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2dea2-358">Moduł ASP.NET Core jest natywnym modułem usług IIS, który jest podłączany do potoku usług IIS:</span><span class="sxs-lookup"><span data-stu-id="2dea2-358">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2dea2-359">Hostowanie aplikacji ASP.NET Core w procesie roboczym usług IIS ( `w3wp.exe` ), nazywanym [modelem hostingu w procesie](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2dea2-359">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2dea2-360">Przekazuj żądania sieci Web do zaplecza ASP.NET Core aplikacji, na której uruchomiono [serwer Kestrel](xref:fundamentals/servers/kestrel), nazywany [modelem hostingu poza procesem](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2dea2-360">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2dea2-361">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="2dea2-361">Supported Windows versions:</span></span>

* <span data-ttu-id="2dea2-362">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2dea2-362">Windows 7 or later</span></span>
* <span data-ttu-id="2dea2-363">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2dea2-363">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2dea2-364">Podczas hostingu w procesie moduł używa implementacji serwera w procesie dla usług IIS, nazywanego serwerem HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-364">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2dea2-365">Podczas hostingu poza procesem moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2dea2-365">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2dea2-366">Moduł nie działa z [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2dea2-366">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2dea2-367">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-367">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2dea2-368">Model hostingu w procesie</span><span class="sxs-lookup"><span data-stu-id="2dea2-368">In-process hosting model</span></span>

<span data-ttu-id="2dea2-369">Aby skonfigurować aplikację do hostingu w procesie, należy dodać `<AspNetCoreHostingModel>` Właściwość do pliku projektu aplikacji z wartością `InProcess` (hosting poza procesem jest ustawiony z `OutOfProcess` ):</span><span class="sxs-lookup"><span data-stu-id="2dea2-369">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2dea2-370">Model hostingu w procesie nie jest obsługiwany w przypadku aplikacji ASP.NET Core przeznaczonych dla .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2dea2-370">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="2dea2-371">Wartość `<AspNetCoreHostingModel>` jest uwzględniana wielkość liter, dlatego `inprocess` i `outofprocess` są prawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="2dea2-371">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2dea2-372">Jeśli `<AspNetCoreHostingModel>` Właściwość nie jest obecna w pliku, wartość domyślna to `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-372">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="2dea2-373">Następujące cechy są stosowane podczas hostingu w procesie:</span><span class="sxs-lookup"><span data-stu-id="2dea2-373">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2dea2-374">Serwer HTTP IIS ( `IISHttpServer` ) jest używany zamiast serwera [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="2dea2-374">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2dea2-375">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) wywołań <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="2dea2-375">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2dea2-376">Zarejestruj `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-376">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2dea2-377">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dea2-377">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2dea2-378">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-378">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2dea2-379">[Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-379">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2dea2-380">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-380">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2dea2-381">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-381">Use one app pool per app.</span></span>

* <span data-ttu-id="2dea2-382">W przypadku korzystania z [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania [plikuapp_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać ZAMKNIĘTA natychmiast po otwarciu połączenia.</span><span class="sxs-lookup"><span data-stu-id="2dea2-382">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2dea2-383">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-383">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2dea2-384">Architektura (bitową) aplikacji i zainstalowane środowisko uruchomieniowe (x64 lub x86) musi być zgodna z architekturą puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-384">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2dea2-385">Wykryto rozłączenia klientów.</span><span class="sxs-lookup"><span data-stu-id="2dea2-385">Client disconnects are detected.</span></span> <span data-ttu-id="2dea2-386">Token anulowania [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) został anulowany po rozłączeniu klienta.</span><span class="sxs-lookup"><span data-stu-id="2dea2-386">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2dea2-387">W ASP.NET Core 2.2.1 lub wcześniejszym <xref:System.IO.Directory.GetCurrentDirectory*> zwraca katalog procesów roboczych procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="2dea2-387">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2dea2-388">Przykładowy kod, który ustawia bieżący katalog aplikacji, znajduje się w [klasie CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="2dea2-388">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2dea2-389">Wywołaj `SetCurrentDirectory` metodę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-389">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2dea2-390">Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> udostępnienia katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-390">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2dea2-391">Podczas hostingu w procesie <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-391">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2dea2-392">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="2dea2-392">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2dea2-393">Podczas przekształcania oświadczeń z <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacją, wywołaj polecenie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Dodaj usługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2dea2-393">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2dea2-394">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="2dea2-394">Out-of-process hosting model</span></span>

<span data-ttu-id="2dea2-395">Aby skonfigurować aplikację do hostingu poza procesem, użyj jednego z następujących metod w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="2dea2-395">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="2dea2-396">Nie określaj `<AspNetCoreHostingModel>` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2dea2-396">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="2dea2-397">Jeśli `<AspNetCoreHostingModel>` Właściwość nie jest obecna w pliku, wartość domyślna to `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-397">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="2dea2-398">Ustaw wartość `<AspNetCoreHostingModel>` właściwości na `OutOfProcess` (hosting w procesie jest ustawiany z `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="2dea2-398">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2dea2-399">W tej wartości wielkość liter nie jest rozróżniana `inprocess` i `outofprocess` są prawidłowe wartości.</span><span class="sxs-lookup"><span data-stu-id="2dea2-399">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2dea2-400">Serwer [Kestrel](xref:fundamentals/servers/kestrel) jest używany zamiast serwera http usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-400">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2dea2-401">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) połączeń z <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="2dea2-401">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2dea2-402">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dea2-402">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2dea2-403">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-403">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2dea2-404">Zmiany modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-404">Hosting model changes</span></span>

<span data-ttu-id="2dea2-405">Jeśli to `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśniono w sekcji [Konfiguracja z web.config](#configuration-with-webconfig) ), moduł odtwarza proces roboczy dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-405">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2dea2-406">W przypadku IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala bezpieczne zamknięcie bieżącego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-406">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2dea2-407">Następne żądanie do aplikacji powoduje zduplikowanie nowego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-407">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2dea2-408">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="2dea2-408">Process name</span></span>

<span data-ttu-id="2dea2-409">`Process.GetCurrentProcess().ProcessName`Raporty `w3wp` / `iisexpress` (w procesie) lub `dotnet` (pozaprocesowe).</span><span class="sxs-lookup"><span data-stu-id="2dea2-409">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2dea2-410">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2dea2-410">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2dea2-411">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="2dea2-411">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2dea2-412">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="2dea2-412">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2dea2-413">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-413">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2dea2-414">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-414">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2dea2-415">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2dea2-415">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2dea2-416">Jak zainstalować moduł ASP.NET Core i korzystać z niego</span><span class="sxs-lookup"><span data-stu-id="2dea2-416">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2dea2-417">Aby uzyskać instrukcje dotyczące sposobu instalowania modułu ASP.NET Core, zobacz [installing the .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="2dea2-417">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2dea2-418">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-418">Configuration with web.config</span></span>

<span data-ttu-id="2dea2-419">Moduł ASP.NET Core jest skonfigurowany przy użyciu `aspNetCore` sekcji `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-419">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2dea2-420">Następujący *web.config* plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="2dea2-420">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="2dea2-421">Następujące *web.config* są publikowane dla [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="2dea2-421">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="2dea2-422"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Właściwość jest ustawiona na `false` , aby wskazać, że ustawienia określone w [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemencie nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-422">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="2dea2-423">Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-423">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2dea2-424">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles* , który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-424">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2dea2-425">Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="2dea2-425">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2dea2-426">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2dea2-426">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2dea2-427">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2dea2-427">Attribute</span></span> | <span data-ttu-id="2dea2-428">Opis</span><span class="sxs-lookup"><span data-stu-id="2dea2-428">Description</span></span> | <span data-ttu-id="2dea2-429">Domyślny</span><span class="sxs-lookup"><span data-stu-id="2dea2-429">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2dea2-430">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-430">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-431">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2dea2-431">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2dea2-432">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-432">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-433">W przypadku wartości true strona **błędu 502,5 procesu** jest pomijana, a strona kodu stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="2dea2-433">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2dea2-434">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-434">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-435">Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje na% ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-435">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2dea2-436">Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-436">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2dea2-437">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-437">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-438">Określa model hostingu jako proces ( `InProcess` / `inprocess` ) lub out-of-Process ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-438">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="2dea2-439">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-439">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-440">Określa liczbę wystąpień procesu określonego w ustawieniu **processPath** , które może być przypadające na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-440">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2dea2-441">&dagger;W przypadku hostingu w procesie wartość jest ograniczona do `1` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-441">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2dea2-442">Ustawienie `processesPerApplication` jest niezalecane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-442">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2dea2-443">Ten atrybut zostanie usunięty w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="2dea2-443">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2dea2-444">Wartooć`1`</span><span class="sxs-lookup"><span data-stu-id="2dea2-444">Default: `1`</span></span><br><span data-ttu-id="2dea2-445">Długości`1`</span><span class="sxs-lookup"><span data-stu-id="2dea2-445">Min: `1`</span></span><br><span data-ttu-id="2dea2-446">Maksymalny`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2dea2-446">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2dea2-447">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-447">Required string attribute.</span></span></p><p><span data-ttu-id="2dea2-448">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="2dea2-448">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2dea2-449">Obsługiwane są ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="2dea2-449">Relative paths are supported.</span></span> <span data-ttu-id="2dea2-450">Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-450">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2dea2-451">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-451">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-452">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-452">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2dea2-453">W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="2dea2-453">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2dea2-454">Nieobsługiwane w przypadku hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-454">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2dea2-455">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="2dea2-455">Default: `10`</span></span><br><span data-ttu-id="2dea2-456">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-456">Min: `0`</span></span><br><span data-ttu-id="2dea2-457">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="2dea2-457">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2dea2-458">Opcjonalny atrybut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="2dea2-458">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2dea2-459">Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="2dea2-459">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2dea2-460">W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="2dea2-460">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2dea2-461">Nie dotyczy hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-461">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2dea2-462">W przypadku hostingu w procesie moduł czeka na aplikację w celu przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-462">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2dea2-463">Prawidłowe wartości segmentów minut i sekund ciągu mieszczą się w zakresie 0-59.</span><span class="sxs-lookup"><span data-stu-id="2dea2-463">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2dea2-464">Użycie **60** w wartości minut lub sekund skutkuje *błędem wewnętrznego serwera 500*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-464">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2dea2-465">Wartooć`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-465">Default: `00:02:00`</span></span><br><span data-ttu-id="2dea2-466">Długości`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-466">Min: `00:00:00`</span></span><br><span data-ttu-id="2dea2-467">Maksymalny`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-467">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2dea2-468">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-468">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-469">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny zostanie bezpiecznie zamknięty po wykryciu pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-469">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2dea2-470">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="2dea2-470">Default: `10`</span></span><br><span data-ttu-id="2dea2-471">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-471">Min: `0`</span></span><br><span data-ttu-id="2dea2-472">Maksymalny`600`</span><span class="sxs-lookup"><span data-stu-id="2dea2-472">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2dea2-473">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-474">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-474">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2dea2-475">Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</span><span class="sxs-lookup"><span data-stu-id="2dea2-475">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="2dea2-476">Podczas hostingu *w procesie*: proces **nie** jest ponownie uruchamiany i **nie używa ustawienia** **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-476">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="2dea2-477">Podczas hostingu *poza procesem*: moduł próbuje ponownie uruchomić proces, gdy odbierze nowe żądanie i kontynuuje próbę ponownego uruchomienia procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie uruchomi **rapidFailsPerMinute** liczby razy w ostatniej minucie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-477">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2dea2-478">Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-478">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2dea2-479">Wartooć`120`</span><span class="sxs-lookup"><span data-stu-id="2dea2-479">Default: `120`</span></span><br><span data-ttu-id="2dea2-480">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-480">Min: `0`</span></span><br><span data-ttu-id="2dea2-481">Maksymalny`3600`</span><span class="sxs-lookup"><span data-stu-id="2dea2-481">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2dea2-482">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-482">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-483">Jeśli wartość jest równa true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2dea2-483">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2dea2-484">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-484">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-485">Określa względną lub bezwzględną ścieżkę do pliku, dla którego jest rejestrowany **stdout** i **stderr** z procesu określonego w **processPath** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-485">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2dea2-486">Ścieżki względne są względne względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-486">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2dea2-487">Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="2dea2-487">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2dea2-488">Wszystkie foldery podane w ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-488">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2dea2-489">Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) są dodawane do ostatniego segmentu ścieżki **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-489">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2dea2-490">Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934. log* w folderze *Logs* , gdy jest zapisywany na 2/5/2018 o godzinie 19:41:32 przy użyciu identyfikatora procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="2dea2-490">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="2dea2-491">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="2dea2-491">Setting environment variables</span></span>

<span data-ttu-id="2dea2-492">Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-492">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2dea2-493">Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-493">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2dea2-494">Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiskowymi systemowymi.</span><span class="sxs-lookup"><span data-stu-id="2dea2-494">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="2dea2-495">W poniższym przykładzie są ustawiane dwie zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="2dea2-495">The following example sets two environment variables.</span></span> <span data-ttu-id="2dea2-496">`ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji do programu `Development` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-496">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2dea2-497">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config* w celu wymuszenia załadowania [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-497">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2dea2-498">`CONFIG_DIR`to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-498">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="2dea2-499">Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-499">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2dea2-500">To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="2dea2-500">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="2dea2-501">Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="2dea2-501">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2dea2-502">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2dea2-502">app_offline.htm</span></span>

<span data-ttu-id="2dea2-503">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="2dea2-503">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2dea2-504">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core kasuje uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="2dea2-504">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2dea2-505">Gdy plik *app_offline.htm* jest obecny, moduł ASP.NET Core reaguje na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-505">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2dea2-506">Po usunięciu pliku *app_offline.htm* następnym żądaniu zostanie uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="2dea2-506">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="2dea2-507">W przypadku korzystania z modelu hostingu poza procesem aplikacja może nie zostać wyłączona natychmiast, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-507">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2dea2-508">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-508">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2dea2-509">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="2dea2-509">Start-up error page</span></span>

<span data-ttu-id="2dea2-510">Hosting zarówno w procesie, jak i poza procesem tworzy niestandardowe strony błędów, gdy nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-510">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2dea2-511">Jeśli moduł ASP.NET Core nie może odnaleźć programu obsługi żądania w procesie lub out-of-process, zostanie wyświetlona strona kodowa stanu *niepowodzenia ładowania 500,0-procesowego/wyjściowego* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-511">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2dea2-512">W przypadku hostingu w procesie, Jeśli uruchomienie aplikacji przez moduł ASP.NET Core nie powiedzie się, zostanie wyświetlona strona kod stanu *niepowodzenia 500,30* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-512">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2dea2-513">W przypadku hostingu poza procesem, jeśli moduł ASP.NET Core nie może uruchomić procesu zaplecza lub proces zaplecza zostanie uruchomiony, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kod stanu *niepowodzenia procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-513">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2dea2-514">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu 5xx usługi IIS, użyj `disableStartUpErrorPage` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-514">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2dea2-515">Aby uzyskać więcej informacji o konfigurowaniu niestandardowych komunikatów o błędach, zobacz [Błędy \<httpErrors> http ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2dea2-515">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2dea2-516">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="2dea2-516">Log creation and redirection</span></span>

<span data-ttu-id="2dea2-517">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="2dea2-517">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2dea2-518">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-518">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2dea2-519">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2dea2-519">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2dea2-520">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-520">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2dea2-521">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2dea2-521">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2dea2-522">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-522">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2dea2-523">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-523">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2dea2-524">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2dea2-524">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2dea2-525">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="2dea2-525">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2dea2-526">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-526">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2dea2-527">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) do ostatniego segmentu `stdoutLogFile` ścieżki (zazwyczaj *stdout*), które są rozdzielane znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2dea2-527">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2dea2-528">Jeśli `stdoutLogFile` ścieżka kończy się od *stdout*, dziennik aplikacji o identyfikatorze PID 1934 utworzony w 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-528">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2dea2-529">Jeśli `stdoutLogEnabled` ma wartość false, błędy występujące podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="2dea2-529">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2dea2-530">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-530">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2dea2-531">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-531">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2dea2-532">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2dea2-532">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2dea2-533">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-533">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2dea2-534">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2dea2-534">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2dea2-535">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2dea2-535">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2dea2-536">Udoskonalone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="2dea2-536">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2dea2-537">Moduł ASP.NET Core można skonfigurować w celu udostępnienia dzienników diagnostyki rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="2dea2-537">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2dea2-538">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie w `debugLevel` celu `TRACE` ujawniania większej wierności informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="2dea2-538">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="2dea2-539">Foldery w ścieżce przekazanej do `<handlerSetting>` wartości (*dzienniki* w powyższym przykładzie) nie są automatycznie tworzone przez moduł i powinny być wcześniej dostępne we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-539">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="2dea2-540">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2dea2-540">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2dea2-541">Wartości poziomu debugowania ( `debugLevel` ) mogą zawierać zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-541">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2dea2-542">Poziomy (w kolejności od najmniejszej do największej szczegółowości):</span><span class="sxs-lookup"><span data-stu-id="2dea2-542">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2dea2-543">BŁĄD</span><span class="sxs-lookup"><span data-stu-id="2dea2-543">ERROR</span></span>
* <span data-ttu-id="2dea2-544">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="2dea2-544">WARNING</span></span>
* <span data-ttu-id="2dea2-545">INFORMACJE</span><span class="sxs-lookup"><span data-stu-id="2dea2-545">INFO</span></span>
* <span data-ttu-id="2dea2-546">TRACE</span><span class="sxs-lookup"><span data-stu-id="2dea2-546">TRACE</span></span>

<span data-ttu-id="2dea2-547">Lokalizacje (wiele lokalizacji jest dozwolonych):</span><span class="sxs-lookup"><span data-stu-id="2dea2-547">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2dea2-548">KONSOLI</span><span class="sxs-lookup"><span data-stu-id="2dea2-548">CONSOLE</span></span>
* <span data-ttu-id="2dea2-549">ELEMENCIE</span><span class="sxs-lookup"><span data-stu-id="2dea2-549">EVENTLOG</span></span>
* <span data-ttu-id="2dea2-550">PLIK</span><span class="sxs-lookup"><span data-stu-id="2dea2-550">FILE</span></span>

<span data-ttu-id="2dea2-551">Ustawienia programu obsługi można także zapewnić za pomocą zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="2dea2-551">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2dea2-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="2dea2-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="2dea2-553">(Domyślnie: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="2dea2-553">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2dea2-554">`ASPNETCORE_MODULE_DEBUG`: Debugowanie ustawienia poziomu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-554">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2dea2-555">**Nie** pozostawiaj włączonej rejestracji debugowania w ramach wdrożenia dłużej niż jest to wymagane, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-555">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2dea2-556">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="2dea2-556">The size of the log isn't limited.</span></span> <span data-ttu-id="2dea2-557">Pozostawienie włączonego dziennika debugowania może spowodować wyczerpanie dostępnego miejsca na dysku i awarię serwera lub usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="2dea2-557">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2dea2-558">Zobacz sekcję [Configuration with web.config](#configuration-with-webconfig) , aby zobaczyć przykład `aspNetCore` elementu w pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-558">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2dea2-559">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="2dea2-559">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2dea2-560">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="2dea2-560">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="2dea2-561">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2dea2-561">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2dea2-562">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-562">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2dea2-563">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="2dea2-563">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2dea2-564">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="2dea2-564">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2dea2-565">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="2dea2-565">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2dea2-566">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="2dea2-566">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2dea2-567">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="2dea2-567">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2dea2-568">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-568">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2dea2-569">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-569">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2dea2-570">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="2dea2-570">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2dea2-571">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami konta **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-571">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2dea2-572">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="2dea2-572">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2dea2-573">W przypadku korzystania z konfiguracji udostępnionej przez usługi IIS na tym samym komputerze, na którym znajduje się instalacja usług IIS, uruchom Instalatora pakietu ASP.NET Core hostowania z `OPT_NO_SHARED_CONFIG_CHECK` parametrem ustawionym na `1` :</span><span class="sxs-lookup"><span data-stu-id="2dea2-573">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2dea2-574">Jeśli ścieżka do konfiguracji udostępnionej nie znajduje się na tym samym komputerze, na którym znajduje się instalacja usług IIS, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="2dea2-574">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2dea2-575">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-575">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2dea2-576">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="2dea2-576">Run the installer.</span></span>
1. <span data-ttu-id="2dea2-577">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="2dea2-577">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2dea2-578">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-578">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2dea2-579">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-579">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2dea2-580">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2dea2-580">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2dea2-581">W systemie hostingu przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-581">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2dea2-582">Znajdź plik *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-582">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2dea2-583">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-583">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2dea2-584">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-584">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2dea2-585">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem *C: \\ Użytkownicy \\ % username% \\ AppData \\ Local \\ temp*. Plik ma nazwę *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-585">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2dea2-586">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2dea2-586">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2dea2-587">Moduł</span><span class="sxs-lookup"><span data-stu-id="2dea2-587">Module</span></span>

<span data-ttu-id="2dea2-588">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2dea2-588">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2dea2-589">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-589">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-590">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-591">Module\V2\aspnetcorev2.dll rdzeń%ProgramFiles%\IIS\Asp.Net</span><span class="sxs-lookup"><span data-stu-id="2dea2-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2dea2-592">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="2dea2-593">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2dea2-593">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2dea2-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-595">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2dea2-597">% ProgramFiles (x86)% \ Express\Asp.Net Module\V2\aspnetcorev2.dll Core usług IIS</span><span class="sxs-lookup"><span data-stu-id="2dea2-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2dea2-598">Schemat</span><span class="sxs-lookup"><span data-stu-id="2dea2-598">Schema</span></span>

<span data-ttu-id="2dea2-599">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2dea2-599">**IIS**</span></span>

* <span data-ttu-id="2dea2-600">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2dea2-601">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="2dea2-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2dea2-602">**IIS Express**</span></span>

* <span data-ttu-id="2dea2-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2dea2-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2dea2-605">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="2dea2-605">Configuration</span></span>

<span data-ttu-id="2dea2-606">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2dea2-606">**IIS**</span></span>

* <span data-ttu-id="2dea2-607">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-607">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2dea2-608">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2dea2-608">**IIS Express**</span></span>

* <span data-ttu-id="2dea2-609">Visual Studio: {Aplikacja główna} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2dea2-610">*iisexpress.exe* INTERFEJS WIERSZA POLECENIA:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2dea2-611">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-611">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2dea2-612">Moduł ASP.NET Core jest natywnym modułem usług IIS, który jest podłączany do potoku usług IIS do przesyłania dalej żądań sieci Web do zaplecza ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-612">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="2dea2-613">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="2dea2-613">Supported Windows versions:</span></span>

* <span data-ttu-id="2dea2-614">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2dea2-614">Windows 7 or later</span></span>
* <span data-ttu-id="2dea2-615">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2dea2-615">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2dea2-616">Moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2dea2-616">The module only works with Kestrel.</span></span> <span data-ttu-id="2dea2-617">Moduł jest niezgodny z [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2dea2-617">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="2dea2-618">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje również zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="2dea2-618">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="2dea2-619">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli wystąpi awaria.</span><span class="sxs-lookup"><span data-stu-id="2dea2-619">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="2dea2-620">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji ASP.NET 4. x, które działają w procesie w usługach IIS, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="2dea2-620">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2dea2-621">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i aplikacją:</span><span class="sxs-lookup"><span data-stu-id="2dea2-621">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Moduł ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="2dea2-623">Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="2dea2-623">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2dea2-624">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2dea2-624">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2dea2-625">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="2dea2-625">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2dea2-626">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-626">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="2dea2-627">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-627">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2dea2-628">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-628">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2dea2-629">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-629">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2dea2-630">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-630">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2dea2-631">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2dea2-631">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2dea2-632">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-632">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2dea2-633">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2dea2-633">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2dea2-634">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="2dea2-634">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2dea2-635">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="2dea2-635">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2dea2-636">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-636">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2dea2-637">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-637">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2dea2-638">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2dea2-638">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2dea2-639">Jak zainstalować moduł ASP.NET Core i korzystać z niego</span><span class="sxs-lookup"><span data-stu-id="2dea2-639">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2dea2-640">Aby uzyskać instrukcje dotyczące sposobu instalowania modułu ASP.NET Core, zobacz [installing the .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="2dea2-640">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2dea2-641">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-641">Configuration with web.config</span></span>

<span data-ttu-id="2dea2-642">Moduł ASP.NET Core jest skonfigurowany przy użyciu `aspNetCore` sekcji `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-642">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2dea2-643">Następujący *web.config* plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="2dea2-643">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="2dea2-644">Następujące *web.config* są publikowane dla [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="2dea2-644">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="2dea2-645">Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-645">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2dea2-646">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles* , który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-646">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2dea2-647">Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="2dea2-647">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2dea2-648">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2dea2-648">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2dea2-649">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2dea2-649">Attribute</span></span> | <span data-ttu-id="2dea2-650">Opis</span><span class="sxs-lookup"><span data-stu-id="2dea2-650">Description</span></span> | <span data-ttu-id="2dea2-651">Domyślny</span><span class="sxs-lookup"><span data-stu-id="2dea2-651">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2dea2-652">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-652">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-653">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2dea2-653">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2dea2-654">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-655">W przypadku wartości true strona **błędu 502,5 procesu** jest pomijana, a strona kodu stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="2dea2-655">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2dea2-656">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-656">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-657">Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje na% ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-657">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2dea2-658">Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-658">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="2dea2-659">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-660">Określa liczbę wystąpień procesu określonego w ustawieniu **processPath** , które może być przypadające na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2dea2-660">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2dea2-661">Ustawienie `processesPerApplication` jest niezalecane.</span><span class="sxs-lookup"><span data-stu-id="2dea2-661">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2dea2-662">Ten atrybut zostanie usunięty w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="2dea2-662">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2dea2-663">Wartooć`1`</span><span class="sxs-lookup"><span data-stu-id="2dea2-663">Default: `1`</span></span><br><span data-ttu-id="2dea2-664">Długości`1`</span><span class="sxs-lookup"><span data-stu-id="2dea2-664">Min: `1`</span></span><br><span data-ttu-id="2dea2-665">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="2dea2-665">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="2dea2-666">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-666">Required string attribute.</span></span></p><p><span data-ttu-id="2dea2-667">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="2dea2-667">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2dea2-668">Obsługiwane są ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="2dea2-668">Relative paths are supported.</span></span> <span data-ttu-id="2dea2-669">Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-669">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2dea2-670">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-670">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-671">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="2dea2-671">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2dea2-672">W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="2dea2-672">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="2dea2-673">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="2dea2-673">Default: `10`</span></span><br><span data-ttu-id="2dea2-674">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-674">Min: `0`</span></span><br><span data-ttu-id="2dea2-675">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="2dea2-675">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2dea2-676">Opcjonalny atrybut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="2dea2-676">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2dea2-677">Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="2dea2-677">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2dea2-678">W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="2dea2-678">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="2dea2-679">Wartooć`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-679">Default: `00:02:00`</span></span><br><span data-ttu-id="2dea2-680">Długości`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-680">Min: `00:00:00`</span></span><br><span data-ttu-id="2dea2-681">Maksymalny`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2dea2-681">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2dea2-682">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-682">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-683">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny zostanie bezpiecznie zamknięty po wykryciu pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-683">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2dea2-684">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="2dea2-684">Default: `10`</span></span><br><span data-ttu-id="2dea2-685">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-685">Min: `0`</span></span><br><span data-ttu-id="2dea2-686">Maksymalny`600`</span><span class="sxs-lookup"><span data-stu-id="2dea2-686">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2dea2-687">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="2dea2-687">Optional integer attribute.</span></span></p><p><span data-ttu-id="2dea2-688">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-688">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2dea2-689">Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</span><span class="sxs-lookup"><span data-stu-id="2dea2-689">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2dea2-690">Moduł podejmuje próbę ponownego uruchomienia procesu, gdy odbierze nowe żądanie i kontynuuje ponowne uruchomienie procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie będzie mogła uruchomić **rapidFailsPerMinute** liczbę razy w ostatniej minucie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-690">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2dea2-691">Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-691">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2dea2-692">Wartooć`120`</span><span class="sxs-lookup"><span data-stu-id="2dea2-692">Default: `120`</span></span><br><span data-ttu-id="2dea2-693">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="2dea2-693">Min: `0`</span></span><br><span data-ttu-id="2dea2-694">Maksymalny`3600`</span><span class="sxs-lookup"><span data-stu-id="2dea2-694">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2dea2-695">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="2dea2-695">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2dea2-696">Jeśli wartość jest równa true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2dea2-696">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2dea2-697">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-697">Optional string attribute.</span></span></p><p><span data-ttu-id="2dea2-698">Określa względną lub bezwzględną ścieżkę do pliku, dla którego jest rejestrowany **stdout** i **stderr** z procesu określonego w **processPath** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-698">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2dea2-699">Ścieżki względne są względne względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2dea2-699">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2dea2-700">Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="2dea2-700">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2dea2-701">Wszystkie foldery podane w ścieżce muszą istnieć, aby moduł mógł utworzyć plik dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-701">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="2dea2-702">Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) są dodawane do ostatniego segmentu ścieżki **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-702">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2dea2-703">Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934. log* w folderze *Logs* , gdy jest zapisywany na 2/5/2018 o godzinie 19:41:32 przy użyciu identyfikatora procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="2dea2-703">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="2dea2-704">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="2dea2-704">Setting environment variables</span></span>

<span data-ttu-id="2dea2-705">Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-705">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2dea2-706">Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-706">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="2dea2-707">Zmienne środowiskowe ustawione w tej sekcji powodują konflikt z systemowymi zmiennymi środowiskowymi ustawionymi z tą samą nazwą.</span><span class="sxs-lookup"><span data-stu-id="2dea2-707">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="2dea2-708">Jeśli zmienna środowiskowa jest ustawiona zarówno w pliku *web.config* , jak i na poziomie systemu w systemie Windows, wartość z pliku *web.config* zostanie dołączona do systemowej wartości zmiennej środowiskowej (na przykład `ASPNETCORE_ENVIRONMENT: Development;Development` ), co uniemożliwia uruchomienie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-708">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="2dea2-709">W poniższym przykładzie są ustawiane dwie zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="2dea2-709">The following example sets two environment variables.</span></span> <span data-ttu-id="2dea2-710">`ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji do programu `Development` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-710">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2dea2-711">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config* w celu wymuszenia załadowania [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-711">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2dea2-712">`CONFIG_DIR`to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-712">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="2dea2-713">Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="2dea2-713">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2dea2-714">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2dea2-714">app_offline.htm</span></span>

<span data-ttu-id="2dea2-715">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="2dea2-715">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2dea2-716">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core kasuje uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="2dea2-716">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2dea2-717">Gdy plik *app_offline.htm* jest obecny, moduł ASP.NET Core reaguje na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-717">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2dea2-718">Po usunięciu pliku *app_offline.htm* następnym żądaniu zostanie uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="2dea2-718">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2dea2-719">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="2dea2-719">Start-up error page</span></span>

<span data-ttu-id="2dea2-720">Jeśli moduł ASP.NET Core nie może uruchomić procesu zaplecza lub proces zaplecza zostanie uruchomiony, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kod stanu *niepowodzenia procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-720">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="2dea2-721">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu 502 usług IIS, użyj `disableStartUpErrorPage` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-721">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2dea2-722">Aby uzyskać więcej informacji o konfigurowaniu niestandardowych komunikatów o błędach, zobacz [Błędy \<httpErrors> http ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2dea2-722">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Strona kodowa stanu niepowodzenia procesów 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2dea2-724">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="2dea2-724">Log creation and redirection</span></span>

<span data-ttu-id="2dea2-725">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="2dea2-725">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2dea2-726">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-726">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2dea2-727">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2dea2-727">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2dea2-728">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="2dea2-728">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2dea2-729">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2dea2-729">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2dea2-730">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2dea2-730">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2dea2-731">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2dea2-731">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2dea2-732">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2dea2-732">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2dea2-733">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="2dea2-733">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2dea2-734">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2dea2-734">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2dea2-735">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) do ostatniego segmentu `stdoutLogFile` ścieżki (zazwyczaj *stdout*), które są rozdzielane znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2dea2-735">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2dea2-736">Jeśli `stdoutLogFile` ścieżka kończy się od *stdout*, dziennik aplikacji o identyfikatorze PID 1934 utworzony w 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-736">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2dea2-737">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-737">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2dea2-738">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2dea2-738">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="2dea2-739">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2dea2-739">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2dea2-740">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2dea2-740">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2dea2-741">Aby utworzyć reguły filtru rejestrowania, zobacz sekcje [Konfiguracja](xref:fundamentals/logging/index#log-filtering) i [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2dea2-741">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2dea2-742">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2dea2-742">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2dea2-743">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="2dea2-743">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2dea2-744">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2dea2-744">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2dea2-745">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-745">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2dea2-746">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="2dea2-746">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2dea2-747">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="2dea2-747">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2dea2-748">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="2dea2-748">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2dea2-749">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="2dea2-749">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2dea2-750">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="2dea2-750">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2dea2-751">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2dea2-751">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2dea2-752">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-752">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2dea2-753">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="2dea2-753">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2dea2-754">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami konta **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="2dea2-754">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2dea2-755">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="2dea2-755">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2dea2-756">W przypadku korzystania z konfiguracji udostępnionej usług IIS wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="2dea2-756">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="2dea2-757">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-757">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2dea2-758">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="2dea2-758">Run the installer.</span></span>
1. <span data-ttu-id="2dea2-759">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="2dea2-759">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2dea2-760">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2dea2-760">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2dea2-761">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="2dea2-761">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2dea2-762">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2dea2-762">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2dea2-763">W systemie hostingu przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-763">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2dea2-764">Znajdź plik *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-764">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2dea2-765">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="2dea2-765">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2dea2-766">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="2dea2-766">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2dea2-767">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem *C: \\ Użytkownicy \\ % username% \\ AppData \\ Local \\ temp*. Plik ma nazwę *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="2dea2-767">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2dea2-768">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2dea2-768">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2dea2-769">Moduł</span><span class="sxs-lookup"><span data-stu-id="2dea2-769">Module</span></span>

<span data-ttu-id="2dea2-770">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2dea2-770">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2dea2-771">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-771">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-772">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="2dea2-773">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2dea2-773">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2dea2-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2dea2-775">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2dea2-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2dea2-776">Schemat</span><span class="sxs-lookup"><span data-stu-id="2dea2-776">Schema</span></span>

<span data-ttu-id="2dea2-777">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2dea2-777">**IIS**</span></span>

* <span data-ttu-id="2dea2-778">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="2dea2-779">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2dea2-779">**IIS Express**</span></span>

* <span data-ttu-id="2dea2-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2dea2-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2dea2-781">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="2dea2-781">Configuration</span></span>

<span data-ttu-id="2dea2-782">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2dea2-782">**IIS**</span></span>

* <span data-ttu-id="2dea2-783">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-783">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2dea2-784">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2dea2-784">**IIS Express**</span></span>

* <span data-ttu-id="2dea2-785">Visual Studio: {Aplikacja główna} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2dea2-786">*iisexpress.exe* INTERFEJS WIERSZA POLECENIA:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2dea2-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2dea2-787">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="2dea2-787">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2dea2-788">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2dea2-788">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="2dea2-789">[Źródło odwołania do modułu ASP.NET Core (gałąź główna)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Użyj listy rozwijanej **rozgałęzienie** , aby wybrać konkretną wersję (na przykład `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="2dea2-789">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
